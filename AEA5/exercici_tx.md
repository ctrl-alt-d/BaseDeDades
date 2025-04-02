# Exercici TX

Anem a fer una pràctica per treballar amb transaccions i familiaritzar-nos amb el concepte de transacció.

## Context

En una empresa volen portar el control dels estocs. Quan fem una venda, hem d'assegurar-nos que, en aquell moment, hi ha prou estoc per poder vendre el producte. Les taules són les següents:

```sql
CREATE TABLE producte (
    codi_producte VARCHAR(20) PRIMARY KEY,
    nom_producte VARCHAR(100)
);

CREATE TABLE variaciostock (
    id INT PRIMARY KEY IDENTITY,
    codi_producte VARCHAR(20),
    quantitat INT, -- pot ser un número positiu o negatiu
    comentari VARCHAR(255),
    FOREIGN KEY (codi_producte) REFERENCES producte(codi_producte)
);

CREATE TABLE comanda (
    id INT PRIMARY KEY IDENTITY,
    codi_producte VARCHAR(20),
    quantitat INT,
    FOREIGN KEY (codi_producte) REFERENCES producte(codi_producte)
);
```

En el moment de fer una comanda hem de saber si tenim prou estoc per realitzar-la. Per això, fem una suma de totes les variacions d'estoc del producte i comprovem si la quantitat resultant és superior o igual a la quantitat de la comanda. Si és així, executem dues sentències SQL: una per afegir una nova variació d’estoc (amb quantitat negativa) i una altra per inserir la comanda.

## Dades inicials per fer proves

A continuació, es mostren uns `INSERT` per tenir dades de prova a les taules `producte` i `variaciostock`.

```sql
-- Inserim alguns productes
INSERT INTO producte (codi_producte, nom_producte)
VALUES 
('P001', 'Llapis'),
('P002', 'Bolígraf'),
('P003', 'Quadern');

-- Inserim algunes variacions d’estoc (entrada de productes)
INSERT INTO variaciostock (codi_producte, quantitat, comentari)
VALUES 
('P001', 100, 'Entrada inicial d’estoc'),
('P002', 200, 'Entrada inicial d’estoc'),
('P003', 150, 'Entrada inicial d’estoc'),

('P001', -10, 'Venda a client A'),
('P002', -5, 'Venda a client B'),
('P003', -20, 'Venda a client C');
```


## Primera part de l'exercici

* Com podem fer per assegurar-nos que les sentències que calen a l'hora de fer una comanda s'executen totes o cap?
* Quin nivell d'aïllament és el més adequat per aquest cas?
* Escriu les comandes que hauria de fer el teu programa de vendes en fer una comanda.

## Segona part de l'exercici

Un cap de projectes ha determinat que desnormalitzant la base de dades podem millorar el rendiment. L'estructura que proposa és afegir un nou camp `stock` dins la taula `producte`:

```sql
ALTER TABLE producte
ADD stock INT;
```

A continuació, informem l'estoc inicial a partir de la suma de les variacions d’estoc:

```sql
UPDATE producte
SET stock = (
    SELECT ISNULL(SUM(quantitat), 0)
    FROM variaciostock
    WHERE variaciostock.codi_producte = producte.codi_producte
);
```

Aquest nou camp contindrà l’estoc actual de cada producte. Quan es fa una comanda, caldrà:

1. Comprovar el valor del camp `stock` a la taula `producte`.
2. Actualitzar aquest valor restant-hi la quantitat venuda.
3. Afegir una nova fila a la taula `variaciostock` amb la quantitat negativa i el comentari pertinent.

* En quin sentit estem "desnormalitzant" la base de dades? (pista: informació duplicada)
* Quins problemes pot comportar aquesta desnormalització?
* Què ens canvia, respecte a l’aïllament, el fet de consultar el `stock` a la taula `producte` en comptes de fer la suma de les variacions? (Pista: el camp `stock` s’actualitza amb un `UPDATE`)
* Per què creus que el cap de projectes ha proposat aquesta solució? Quin seria ara el nivell d'aïllament més adequat?
* Escriu les comandes que hauria de fer el teu programa de vendes en fer una comanda.
