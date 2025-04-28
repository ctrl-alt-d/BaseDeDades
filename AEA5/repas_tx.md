# Exercici reserva de visites al veterinari

Create tables:

```sql
CREATE TABLE Disponibles(
  DiaHora DateTime not null primary key,
  maxVisites int not null
)

CREATE TABLE Reserves (
  DiaHora DateTime not null references Disponibles(DiaHora),
  nomAnimal varchar(200) not null,
  CONSTRAINT reserves_pk 
    primary key (DiaHora, nomAnimal)
)

```

Inserts:


```sql
INSERT INTO Disponibles(DiaHora, maxVisites)
values ('2025-06-24 12:00', 3)
```




```sql
ALTER PROCEDURE Add_reserva @DiaiHora  DATETIME,
                            @NomAnimal VARCHAR(200)
AS
  BEGIN
      BEGIN try
          SET nocount ON;

          BEGIN TRANSACTION;

          SET TRANSACTION isolation level serializable;

          DECLARE @datadisponible DATETIME;

          IF @DiaiHora < Getdate()
            BEGIN
                RAISERROR('No es pot reservar amb dates del passat',16,1);

                ROLLBACK TRANSACTION;

                RETURN;
            END

          DECLARE @reservesdisponibles INT;
          DECLARE @reservescompromeses INT;

          SELECT @reservesdisponibles = maxvisites
          FROM   disponibles
          WHERE  diahora = @DiaiHora;

          IF @reservesdisponibles IS NULL
            BEGIN
                RAISERROR('No hi ha disponibilitat en aquesta franja',16,1);

                ROLLBACK TRANSACTION;

                RETURN;
            END

          SELECT @reservescompromeses = Count(*)
          FROM   reserves
          WHERE  diahora = @DiaiHora;

          IF @reservesdisponibles - @reservescompromeses <= 0
            BEGIN
                RAISERROR('No queden visites per a aquesta data',16,1);

                ROLLBACK TRANSACTION;

                RETURN;
            END

          INSERT INTO reserves
          VALUES      (@DiaiHora,
                       @NomAnimal);

          PRINT( 'Reserva realitzada' )

          COMMIT;
      END try

      BEGIN catch
          ROLLBACK;

          DECLARE @ErrorMessage NVARCHAR(4000);
          DECLARE @ErrorState INT;
          DECLARE @ErrorSeverity INT;

          SELECT @ErrorMessage = Error_message(),
                 @ErrorState = Error_state(),
                 @ErrorSeverity = Error_severity();

          RAISERROR(@ErrorMessage,@ErrorState,@ErrorSeverity)
      END catch
  END 
  ```