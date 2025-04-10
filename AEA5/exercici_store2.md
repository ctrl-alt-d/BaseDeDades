# Pràctica guiada: Procediment emmagatzemat per votar en una enquesta

## Context

En una aplicació d’enquestes, volem que els usuaris puguin votar una de les opcions disponibles. Cada usuari només pot votar una vegada per enquesta. A més, volem controlar que no es pugui votar més enllà de la data límit.

Aquest procediment gestionarà tot el procés de votació, assegurant integritat i controlant possibles errors.

---

## Estructura de taules (documentada)


- `usuaris(id, nom)`
- `enquestes(id, text, opcio_mes_votada_id, data_max_votar)`
- `opcions(id, enquesta_id, text)`
- `vots(usuari_id, opcio_id)`


---

## Objectiu del procediment

Crear un procediment emmagatzemat anomenat `votar` amb els següents paràmetres:

- `@usuari_id INT`
- `@opcio_id INT`

Aquest procediment:

1. Comprova que l’opció existeix i pertany a una enquesta que **encara es pot votar** (`data_max_votar >= GETDATE()`).
2. Comprova que l’usuari **no hagi votat ja** en aquella enquesta.
3. Si tot és correcte, insereix el vot a la taula `vots`.
4. Fa servir una **transacció**.
5. Utilitza `TRY...CATCH` i `RAISERROR`.
6. Fa servir `SET NOCOUNT ON`.
7. Actualitza el camp `opcio_mes_votada_id` a `enquestes` si aquesta opció esdevé la més votada.

---

## Requisits i condicions

- Un usuari **només pot votar una vegada per enquesta**. L'usuari ha d'existir.
- No es pot votar **fora de termini**.
- Només es pot votar per **opcions que existeixin**.
- El procediment **no ha de retornar dades**, però pot llençar errors amb missatges útils (`RAISERROR`).
- El control d’errors i la consistència són fonamentals.

---

## Exemple de crida

```sql
EXEC votar @usuari_id = 3, @opcio_id = 12;
```

---

## Preguntes per reflexionar

1. Què passa si dos usuaris voten alhora la mateixa opció? Cal controlar concurrència? Quin nivell d'isolació has posat i perquè?
2. Si després volem permetre esborrar vots, com hauria de canviar la lògica del procediment?
3. Quin seria el millor nivell d’aïllament en un entorn amb moltes votacions simultànies?
4. Quina és l'operació que provoca més bloquejos en aquest sistema de votacions?


# Resolució

Aquí es planteja l'inici de la solució, continua a partir d'aquest punt:

```sql
-- usuaris(id, nom)
-- enquestes(id, text, opcio_mes_votada_id, data_max_votar)
-- opcions(id, enquesta_id, text)
-- vots(usuari_id, opcio_id)
CREATE TABLE usuaris
  (
     id  INT NOT NULL IDENTITY,
     nom VARCHAR(250) NOT NULL,
     CONSTRAINT usuaris_pk PRIMARY KEY (id)
  );

CREATE TABLE enquestes
  (
     id                  INT NOT NULL IDENTITY PRIMARY KEY,
     [text]              VARCHAR(250) NOT NULL,
     opcio_mes_votada_id INT NULL,-- PENDENT DE FK
     data_max_votar      DATETIME NOT NULL
  )

CREATE TABLE opcions
  (
     id          INT NOT NULL IDENTITY PRIMARY KEY,
     enquesta_id INT NOT NULL REFERENCES enquestes (id),
     [text]      VARCHAR(250) NOT NULL
  );

ALTER TABLE enquestes
  ADD CONSTRAINT enquestes_2_opcions FOREIGN KEY (opcio_mes_votada_id)
  REFERENCES opcions(id);

CREATE TABLE vots
  (
     usuari_id INT NOT NULL REFERENCES usuaris (id),
     opcio_id  INT NOT NULL REFERENCES opcions (id),
     CONSTRAINT vots_pk PRIMARY KEY (usuari_id, opcio_id)
  )

ALTER PROCEDURE Votar @usuari_id INT,
                      @opcio_id  INT
AS
  BEGIN
      BEGIN try
          SET nocount ON;

          BEGIN TRANSACTION;

          SET TRANSACTION isolation level serializable;

          DECLARE @ENQUESTA INT;

          -- comprovo si existeix l'enquesta
          SELECT @ENQUESTA = E.id
          FROM   enquestes E
                 INNER JOIN opcions O
                         ON E.id = O.enquesta_id
          WHERE  O.id = @opcio_id;

          IF @ENQUESTA IS NULL
            BEGIN
                RAISERROR('L''Enquesta no existeix.',16,1);

                ROLLBACK TRANSACTION;

                RETURN;
            END;

          -- Comprovar si encara es pot votar
          DECLARE @ES_POT_VOTAR BIT;

          SELECT @ES_POT_VOTAR = Iif(E.data_max_votar >= Getdate(), 1, 0)
          FROM   enquestes E
          WHERE  E.id = @ENQUESTA;

          IF @ES_POT_VOTAR = 0
            BEGIN
                RAISERROR('L''Enquesta ja està tancada.',16,1);

                ROLLBACK TRANSACTION;

                RETURN;
            END;

          -- Usuari existeix?
          DECLARE @USUARI_EXISTEIX BIT;

          SELECT @USUARI_EXISTEIX = Count(*)
          FROM   usuaris u
          WHERE  u.id = @usuari_id;

          IF @USUARI_EXISTEIX = 0
            BEGIN
                RAISERROR('L''usuari no existeix.',16,1);

                ROLLBACK TRANSACTION;

                RETURN;
            END;

          -- Comprovar que no ha votat en aquella enquesta
          -- Compte!! Una enquesta té diverses opcions, pot 
          -- ser que l'opció no es correspongui.


          -- Altres validacions

          -- Insertar a la taula de vots

          
          COMMIT;
      END try

      BEGIN catch
          -- En cas d'error, fem rollback i mostrem l'error
          ROLLBACK TRANSACTION;

          DECLARE @ErrorMessage NVARCHAR(4000);
          DECLARE @ErrorSeverity INT;
          DECLARE @ErrorState INT;

          SELECT @ErrorMessage = Error_message(),
                 @ErrorSeverity = Error_severity(),
                 @ErrorState = Error_state();

          RAISERROR(@ErrorMessage,@ErrorSeverity,@ErrorState);
      END catch
  END;
```

Executar procediment:

```sql
EXEC Votar
  1,
  1
```

Insertar dades de prova:

```sql
INSERT INTO enquestes
VALUES      ('Quin dinosaure és el més fort?',
             NULL,
             '2025-01-01' )

INSERT INTO opcions
VALUES      (1,
             'Veloziraptor')

UPDATE enquestes
SET    data_max_votar = '2025-06-12'

INSERT INTO usuaris
VALUES      ('dani') 
```