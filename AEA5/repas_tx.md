# Exercici reserva de visites al veterinari

Un centre veterinari vol obrir una web per tal que els clients pugui fer `reserva` d'hora de visita. Cda franja horària permeten diverses visites. Aquestes són les taules que han disenyat:

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

Per a la franja de les 12:00h del dia 24 de juny, obren 3 visites.


```sql
INSERT INTO Disponibles(DiaHora, maxVisites)
values ('2025-06-24 12:00', 3)
```

## Primera part

Fes un procediment emmagatzemant que, donat el `nomAnimal` i el `DiaiHora` anoti la `reserva`. El procediment emmagatzemant comprovarà que la data i hora és del futur. També comprovarà que sigui una franja que permet reserves. Per últim comprovarà que en aquella franja queda disponibilitat, que no ens hem passat del `maxVisites`. Penda i justifica el nivell d'isolació triat.

Solució:


```sql
CREATE PROCEDURE Add_reserva @DiaiHora  DATETIME,
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

          SELECT @reservescompromeses = Count(*)  -- Select *1 (mirar més endavant)
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

Provem de fer la reserva:

```sql
Exec add_reserva '2025-06-24 12:00', 'xuxu'
```

Cal també provar de fer reserves per testar la resta de casos.

Hem triat nivell d'isolació serialitzable perquè quan fem la select *1 no podem permetre que apareguin phantom reads a la següent lectura, és a dir, que aquesta select ens ha d'aïllar també dels inserts, no només del updates i deletes (que seria el repeatable read)

## Segona part

Un cop posat en marxa el sistema, un desenvolupador sèrior ens explica que, en aquests casos, és millor 'desnormalitzar' i, un camp que podria ser calculat, el camp `visitesCompromeses` el materialitzem, és a dir, creem el camp `visitesCompromeses` a la taula `Disponibles` de la base de dades, al costat del camp `maxVisites`, d'aquesta manera, sabrem quantes de es visites disponibles ja estan compromeses sense haver de fer un select a la taula de reserves. Això implica gestionar aquest comptador (sumar 1 a `visitesCompromeses` a cada reserva)

Primer creem el camp:

```sql
alter table disponibles
add column visitesCompromeses int not null default(0)
```

Modifica el procediment emmagatzemat i digues quin seria ara el nivell d'isolació.

Solució:

```sql
alter PROCEDURE Add_reserva @DiaiHora  DATETIME,
                            @NomAnimal VARCHAR(200)
AS
  BEGIN
      BEGIN try
          SET nocount ON;

          BEGIN TRANSACTION;

          SET TRANSACTION isolation level REPEATABLE READ ;

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

          -- Aquí mirem quantes reserves tenim en aquella franja sense
          -- haver d'anar a la taula de reserves.
          SELECT @reservescompromeses = visitesCompromeses
          FROM   Disponibles
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

          -- Actualitzem el comptador de visitesCompromeses amb un update.
          update Disponibles
             set visitesCompromeses = visitesCompromeses + 1
           where DiaHora = @DiaiHora;
          
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

Hem pogut relaxar el nivell d'isolació fins a `repeatable read` perquè ara ja no fem un select count a la taula de `reserves` sino que consultem el comptador a la mateixa taula de `disponibles`, aquest comptador s'actualitza mitjançant un `update` i el nivell d'isolació `repeatable read` ens isola dels updates.