# Pràctica guiada: Procediment emmagatzemat per gestionar comandes

En aquesta pràctica crearem un **procediment emmagatzemat** ([CREATE PROCEDURE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-procedure-transact-sql?view=sql-server-ver16)) que rep com a paràmetres un codi de producte i una quantitat, i:

1. Comprova si hi ha estoc suficient.
2. Si n'hi ha, descompta l'estoc, crea una nova entrada a `variaciostock` i insereix la comanda.
3. Utilitza una **transacció** per assegurar consistència.
4. Gestiona errors amb **TRY...CATCH**.
5. Fa servir **SET NOCOUNT ON** per evitar missatges addicionals.

---

## Pas 1: Crear el procediment

```sql
CREATE PROCEDURE fer_comanda
    @codi_producte VARCHAR(20),
    @quantitat INT
AS
BEGIN
    SET NOCOUNT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        -- Comprovem l’estoc actual del producte
        DECLARE @stock_actual INT;

        SELECT @stock_actual = stock
        FROM producte
        WHERE codi_producte = @codi_producte;

        -- Comprovem si hi ha estoc suficient
        IF @stock_actual IS NULL
        BEGIN
            RAISERROR('El producte no existeix.', 16, 1);
            ROLLBACK TRANSACTION;
            RETURN;
        END

        IF @stock_actual < @quantitat
        BEGIN
            RAISERROR('Estoc insuficient per fer la comanda.', 16, 1);
            ROLLBACK TRANSACTION;
            RETURN;
        END

        -- Actualitzem el camp d’estoc a la taula producte
        UPDATE producte
        SET stock = stock - @quantitat
        WHERE codi_producte = @codi_producte;

        -- Afegim una nova entrada a la taula variaciostock
        INSERT INTO variaciostock (codi_producte, quantitat, comentari)
        VALUES (@codi_producte, -@quantitat, 'Venda realitzada mitjançant procediment');

        -- Inserim la comanda
        INSERT INTO comanda (codi_producte, quantitat)
        VALUES (@codi_producte, @quantitat);

        -- Confirmem la transacció
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        -- En cas d'error, fem rollback i mostrem l'error
        ROLLBACK TRANSACTION;

        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```
---

## Pas 2: Cridar el procediment

Per provar el procediment, podem fer:

```sql
EXEC fer_comanda @codi_producte = 'P001', @quantitat = 5;
```
---

## Notes finals

* Aquest procediment assumeix que treballem amb la taula `producte` que conté el camp `stock`.
* Si es treballa només amb `variaciostock` sense el camp `stock`, caldria adaptar la lògica per fer la suma en temps real.

---

## Aclariment: Com funciona `RAISERROR`

La instrucció `RAISERROR` ([RAISERROR (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/language-elements/raiserror-transact-sql?view=sql-server-ver16)) serveix per llençar errors personalitzats dins de SQL Server. És útil per controlar el flux d'execució dins de blocs `TRY...CATCH` i per mostrar missatges significatius als usuaris o al sistema.

### Sintaxi bàsica:
```sql
RAISERROR('Missatge d''error', Severitat, Estat);
```

### Paràmetres:
- **Missatge d'error**: És una cadena de text que descriu el problema. Pot ser literal o provinent d’una variable.
- **Severitat**: És un número entre 0 i 25. Els valors:
  - De 0 a 10 són informatius.
  - De 11 a 16 són errors generats per l’usuari (com estoc insuficient, dades incorrectes, etc.).
  - De 17 en amunt representen errors més greus, habitualment del sistema.
- **Estat**: Un número enter entre 0 i 255. Permet identificar l'origen de l'error o diferenciar errors similars. No té un efecte directe sobre l'execució, però pot ser útil per a diagnòstics.
