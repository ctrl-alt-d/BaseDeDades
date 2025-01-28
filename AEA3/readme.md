# DDL - Data Definition Language

Aprendran DDM, crear taules, modificar l'estructura de les taules. Declarar restriccions de clau forana: cascada, restrict, default, ...

Observa aquestes taules i fes el MCD per comprovar que entens l'estructura:

```sql

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
