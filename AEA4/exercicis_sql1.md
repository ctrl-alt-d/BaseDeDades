### Preparar l'entorn d'aprenentatge

Instruccions als alumnes: Crea una base de dades nova anomenada Facturació. Farem els exercicis a partir de les següents taules, crea les taules a la teva base de dades:

```sql
CREATE TABLE Categories (
    Codi VARCHAR(4) NOT NULL,
    Nom VARCHAR(250) NOT NULL,
    CONSTRAINT Categories_PK PRIMARY KEY (Codi),
    CONSTRAINT Categories_AK UNIQUE (Nom)
);

CREATE TABLE Clients (
    Codi VARCHAR(4) NOT NULL,
    Nom VARCHAR(250) NOT NULL,
    Telefon VARCHAR(100) NULL,
    CONSTRAINT Clients_PK PRIMARY KEY (Codi),
    CONSTRAINT Clients_AK UNIQUE (Nom)
);

CREATE TABLE Productes (
    Codi VARCHAR(4) NOT NULL,
    Nom VARCHAR(250) NOT NULL,
    Preu_Actual MONEY NOT NULL,
    Codi_Categoria VARCHAR(4) NULL,
    CONSTRAINT Productes_PK PRIMARY KEY (Codi),
    CONSTRAINT Productes_AK UNIQUE (Nom),
    CONSTRAINT Producte_Categoria_FK FOREIGN KEY (Codi_Categoria) REFERENCES Categories (Codi)
);

CREATE TABLE Factures (
    Exercici INT NOT NULL,
    Numero INT NOT NULL,
    Client_Codi VARCHAR(4) NOT NULL,
    Data DATE NOT NULL,
    CONSTRAINT Factures_PK PRIMARY KEY (Exercici, Numero),
    CONSTRAINT Factures_Client_FK FOREIGN KEY (Client_Codi) REFERENCES Clients (Codi)
);

CREATE TABLE LiniesDeFactura (
    Exercici INT NOT NULL,
    Numero_Factura INT NOT NULL,
    Numero_Linia INT NOT NULL,
    Producte_Codi VARCHAR(4) NOT NULL,
    Quantitat INT NOT NULL,
    Preu_Venda MONEY NOT NULL,
    Descompte DECIMAL(5, 2) NOT NULL,
    CONSTRAINT LiniesDeFactura_PK PRIMARY KEY (Exercici, Numero_Factura, Numero_Linia),
    CONSTRAINT LiniesDeFactura_Factura_FK FOREIGN KEY (Exercici, Numero_Factura) REFERENCES Factures (Exercici, Numero),
    CONSTRAINT LiniesDeFactura_Producte_FK FOREIGN KEY (Producte_Codi) REFERENCES Productes (Codi)
);
```

### Aprenent Inserts

La comanda INSERT permet afegir dades a una taula. La sintaxi és la següent:

```sql
INSERT INTO nom_taula (columna1, columna2, columna3, ...)
VALUES (valor1, valor2, valor3, ...);
```

Hi ha SGBDR (Sistemes Gestors de Bases de Dades Relacionals) que permeten inserir més d'una fila en una sola comanda INSERT. La sintaxi és la següent:

```sql
INSERT INTO nom_taula (columna1, columna2, columna3, ...)
VALUES (valor1, valor2, valor3, ...),
       (valor1, valor2, valor3, ...),
       (valor1, valor2, valor3, ...),
       ...
       (valor1, valor2, valor3, ...);
```

Més informació: https://learn.microsoft.com/en-us/sql/t-sql/statements/insert-transact-sql?view=sql-server-ver16

Ara anem a insertar dades a les taules creades anteriorment:


```sql
-- Inserció de dades a la taula Categories
INSERT INTO Categories (Codi, Nom) VALUES
('C001', 'Electrònica'),
('C002', 'Llibres'),
('C003', 'Roba'),
('C004', 'Alimentació');

-- Inserció de dades a la taula Clients
INSERT INTO Clients (Codi, Nom, Telefon) VALUES
('CL01', 'Joan Pérez', '666111222'),
('CL02', 'Maria Garcia', '667333444'),
('CL03', 'Anna López', '668555666'),
('CL04', 'Pau Martínez', NULL);

-- Inserció de dades a la taula Productes
INSERT INTO Productes (Codi, Nom, Preu_Actual, Codi_Categoria) VALUES
('P001', 'Telèfon Mòbil', 299.99, 'C001'),
('P002', 'Portàtil', 899.99, 'C001'),
('P003', 'Novel·la Fantàstica', 19.99, 'C002'),
('P004', 'Samarreta', 15.99, 'C003'),
('P005', 'Formatge', 7.99, 'C004');

-- Inserció de dades a la taula Factures
INSERT INTO Factures (Exercici, Numero, Client_Codi, Data) VALUES
(2025, 1, 'CL01', '2025-01-01'),
(2025, 2, 'CL02', '2025-01-15'),
(2025, 3, 'CL03', '2025-01-20'),
(2025, 4, 'CL04', '2025-01-25');

-- Inserció de dades a la taula LiniesDeFactura
INSERT INTO LiniesDeFactura (Exercici, Numero_Factura, Numero_Linia, Producte_Codi, Quantitat, Preu_Venda, Descompte) VALUES
(2025, 1, 1, 'P001', 1, 299.99, 0.00),
(2025, 1, 2, 'P003', 2, 19.99, 10.00),
(2025, 2, 1, 'P004', 3, 15.99, 5.00),
(2025, 3, 1, 'P002', 1, 899.99, 0.00),
(2025, 4, 1, 'P005', 5, 7.99, 0.00),
(2025, 4, 2, 'P003', 1, 19.99, 15.00);
```


### Aprenent Consultes Select

Per seleccionar dades fem servir la comanda SELECT. La sintaxi és la següent:

```sql
SELECT columna1, columna2, columna3, ...
FROM nom_taula;
```

Si volem seleccionar totes les columnes farem servir l'asterisc:

```sql
SELECT *
FROM nom_taula;
```

Més informació: https://learn.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql?view=sql-server-ver16

Anem a practicar amb unes consultes senzilles:

```sql
/* Consulta 1: Recupera totes les categories disponibles */
/* Mostra el codi i el nom de cada categoria */
SELECT * FROM Categories;

/* Consulta 2: Recupera la llista completa de clients */
/* Mostra el codi, nom i telèfon de tots els clients */
SELECT * FROM Clients;

/* Consulta 3: Recupera tots els productes disponibles */
/* Mostra el codi, nom, preu actual i codi de la categoria associada */
SELECT * FROM Productes;
```

Fes tu ara els següents exercicis:

* Selecció de totes les dades de les factures emeses
* Selecció el codi i la data de les factures emeses
* Selecció de totes les dades de totes les línies de factura


### Introduïm order by:

Si volem les dades en ordre farem servir la clàusula ORDER BY. La sintaxi és la següent:

```sql
SELECT columna1, columna2, columna3, ...
FROM nom_taula
ORDER BY columna7, columna2, columna3, ...;
```

Si volem ordre descendent farem:

```sql
SELECT columna1, columna2, columna3, ...
FROM nom_taula
ORDER BY columna2 DESC, columna5 DESC, columna7 DESC;
```

>Les columnes que apareixen a l' `order by` no tenen perquè aparèixer a la llista de selecció.

Anem a practicar amb unes consultes senzilles:

```sql
/* Consulta 1: Ordenar les categories per nom en ordre alfabètic */
SELECT * FROM Categories
ORDER BY Nom;

/* Consulta 2: Llistar els clients ordenats pel seu nom en ordre descendent */
SELECT * FROM Clients
ORDER BY Nom DESC;

/* Consulta 3: Mostrar els productes ordenats pel preu actual, de més barat a més car */
SELECT * FROM Productes
ORDER BY Preu_Actual;
```

Fes tu ara els següents exercicis:
* Ordenar les factures per data
* Ordenar les línies de factura per quantitat
* Ordenar les línies de factura per preu de venda en ordre descendent

### Introduïm where:

Si volem filtrar les dades farem servir la clàusula WHERE. La sintaxi és la següent:

```sql
SELECT columna1, columna2, columna3, ...
FROM nom_taula
WHERE condicio;
```

Exemples de condicions:

* Igualtat: `columna = valor`
* Diferent: `columna <> valor` o bé `columna != valor`
* Major que: `columna > valor`
* Major o igual que: `columna >= valor`
* Menor que: `columna < valor`
* Menor o igual que: `columna <= valor`
* Comença per: `columna LIKE 'valor%'`
* Conté: `columna LIKE '%valor%'`
* Està entre: `columna BETWEEN valor1 AND valor2`
* És nul: `columna IS NULL`
* No és nul: `columna IS NOT NULL`
* En una llista: `columna IN (valor1, valor2, ...)`
* No en una llista: `columna NOT IN (valor1, valor2, ...)`



```sql
/* Consulta 1: Recupera la informació del client amb codi 'CL02' */
SELECT * FROM Clients
WHERE Codi = 'CL02';

/* Consulta 2: Mostra les categories amb noms que comencin per 'A' */
SELECT * FROM Categories
WHERE Nom LIKE 'A%';

/* Consulta 3: Recupera els productes amb preu actual superior a 50 euros */
SELECT * FROM Productes
WHERE Preu_Actual > 50;
```

Exercicis:

* Recupera la informació de les factures emeses l'any 2025
* Mostra les línies de factura amb quantitat superior a 2
* Recupera els clients amb nom que continguin dues `n` al seu nom

### Introduim inner joins:

El join serveix per combinar dades de dues taules. La sintaxi és la següent:

```sql
SELECT taula1.columna1, taula1,columna2, taula2.columna1, ...
FROM taula1
INNER JOIN taula2 ON taula1.columna = taula2.columna;
```

Es poden combinar més de dues taules:

```sql
SELECT taula1.columna1, taula2.columna1, taula3.columna1, ...
FROM taula1
INNER JOIN taula2 ON taula1.columna = taula2.columna
INNER JOIN taula3 ON taula2.columna = taula3.columna;
```

Podem posar alias a les taules per si el seu nom és molt llarg o per si volem fer join dues vegades contra la mateixa taula:

```sql
SELECT t1.columna1, t2.columna1, t3.columna1, ...
FROM taula1 t1
INNER JOIN taula2 t2 ON t1.columna = t2.columna
INNER JOIN taula2 t3 ON t2.columna = t3.columna;
```

Exemples:


```sql
/* Consulta 1: Recupera la informació de les factures emeses pels clients */
/* Mostra l'exercici, número de factura, codi del client, data, nom del client i telèfon */
SELECT Factures.Exercici, Factures.Numero, Factures.Client_Codi, Factures.Data, Clients.Nom, Clients.Telefon
FROM Factures
INNER JOIN Clients ON Factures.Client_Codi = Clients.Codi;

/* La mateixa consulta usant alias de taules */
SELECT F.Exercici, F.Numero, F.Client_Codi, F.Data, C.Nom, C.Telefon
FROM Factures F
INNER JOIN Clients C ON F.Client_Codi = C.Codi;

/* Consulta 2: Recupera la informació de les línies de factura amb el nom del producte usant alias*/
/* Mostra l'exercici, número de factura, número de línia, codi del producte, quantitat, preu de venda, descompte i nom del producte */
SELECT LF.Exercici, LF.Numero_Factura, LF.Numero_Linia, LF.Producte_Codi, LF.Quantitat, LF.Preu_Venda, LF.Descompte, P.Nom
FROM LiniesDeFactura LF
INNER JOIN Productes P ON LF.Producte_Codi = P.Codi;
```

### Combinem joins amb where:


```sql
/* Consulta 1: Recupera la informació de les línies de factura amb el nom del producte per a una factura específica */
/* Mostra l'exercici, número de factura, número de línia, codi del producte, quantitat, preu de venda, descompte i nom del producte */
SELECT LF.Exercici, LF.Numero_Factura, LF.Numero_Linia, LF.Producte_Codi, LF.Quantitat, LF.Preu_Venda, LF.Descompte, P.Nom
FROM LiniesDeFactura LF
INNER JOIN Productes P ON LF.Producte_Codi = P.Codi
WHERE LF.Exercici = 2025 AND LF.Numero_Factura = 1;

/* Consulta 2: Recupera la informació de les factures emeses pels clients amb nom que comença per 'M' */
/* Mostra l'exercici, número de factura, codi del client, data, nom del client i telèfon */
SELECT F.Exercici, F.Numero, F.Client_Codi, F.Data, C.Nom, C.Telefon
FROM Factures F
INNER JOIN Clients C ON F.Client_Codi = C.Codi
WHERE C.Nom LIKE 'M%';
```

Exercicis:

* Recupera la informació de les línies de factura amb el nom del producte per a una factura específica.
* Recupera la informació de les factures emeses pels clients amb nom que comença per 'A'.
* Recupera la informació de les línies de factura amb el nom del producte per a una factura emesa per un client amb nom que comença per 'J'. Hauràs de fer servir dos joins.

# TODO

A partir d'aquí es pot explicar:
* Expresions aritmètiques (exemple: multiplicar preu per quantitat per obtenir el total)
* Funcions (ex: data, string, cast, ...)
* outer joins (left, right, full)
* cross join
* group by (i funcions d'agregació)
* having
* subqueries
* union i union all
* intersect
* except
* select distinct
* inserts des de select
* updates
* deletes
