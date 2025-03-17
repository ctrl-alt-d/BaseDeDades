# Exercicis SQL

## DML

Observa aquestes taules i 

```sql
CREATE TABLE Categories (
    Codi VARCHAR(4) NOT NULL,
    Nom VARCHAR(250) NOT NULL,
    CONSTRAINT Categories_PK PRIMARY KEY (Codi),
    CONSTRAINT Categories_AK UNIQUE (Nom)
);

CREATE TABLE Electrodomestic (
    NS VARCHAR(12) NOT NULL,
    Categoria_Codi VARCHAR(4) NOT NULL,
    Descripcio VARCHAR(250) NOT NULL,
    Preu DECIMAL(10, 2) NOT NULL,
    CONSTRAINT Electrodomestic_PK PRIMARY KEY (NS),
    CONSTRAINT Electrodomestic_2_Categoria FOREIGN KEY (Categoria_Codi) REFERENCES Categories (Codi)
);

CREATE TABLE Lloguer (
    NS VARCHAR(12) NOT NULL,
    Data_Inici DATE NOT NULL,
    Data_Fi DATE NULL,
    Client VARCHAR(250) NOT NULL,
    Preu DECIMAL(10, 2) NULL,
    CONSTRAINT Lloguer_PK PRIMARY KEY (NS, Data_Inici),
    CONSTRAINT Lloguer_2_Electrodomestic FOREIGN KEY (NS) REFERENCES Electrodomestic (NS)
);
```

Per comprovar que entens l'estructura fes:

* el MFD (Model Físic de les Dades) 
* el MCD (Model Conceptual de les Dades) 
* l'univers de discurs. Caldrà que miris els inserts (a sota) per explicar com funciona el sistema de lloguers.

## DDL

### INSERSIÓ DE DADES

```SQL
-- Inserció de categories
INSERT INTO Categories (Codi, Nom)
VALUES 
    ('FRI1', 'Neveres'),
    ('MIC1', 'Microones');

-- Inserció d'electrodomèstics
INSERT INTO Electrodomestic (NS, Categoria_Codi, Descripcio, Preu)
VALUES 
    ('NS000001', 'FRI1', 'Nevera petita', 30.00),
    ('NS000002', 'FRI1', 'Nevera mitjana', 45.00),
    ('NS000003', 'MIC1', 'Microones bàsic', 20.00),
    ('NS000004', 'MIC1', 'Microones amb grill', 25.00),
    ('NS000005', 'FRI1', 'Nevera gran', 60.00);
    ;

-- INSERTS PER A LA TAULA Lloguer

-- Lloguers finalitzats (abans de la data actual)
INSERT INTO Lloguer (NS, Data_Inici, Data_Fi, Client, Preu)
VALUES 
    ('NS000001', DATEADD(DAY, -30, GETDATE()), DATEADD(DAY, -20, GETDATE()), 'Client A', 30.00 * 10),
    ('NS000002', DATEADD(DAY, -60, GETDATE()), DATEADD(DAY, -30, GETDATE()), 'Client B', 45.00 * 30),
    ('NS000003', DATEADD(DAY, -90, GETDATE()), DATEADD(DAY, -60, GETDATE()), 'Client C', 20.00 * 30);

-- Lloguers actuals (Data_Inici abans d'avui i Data_Fi NULL o en el futur)
INSERT INTO Lloguer (NS, Data_Inici, Data_Fi, Client, Preu)
VALUES 
    ('NS000001', DATEADD(DAY, -10, GETDATE()), NULL, 'Client D', 30.00),
    ('NS000002', DATEADD(DAY, -15, GETDATE()), DATEADD(DAY, 5, GETDATE()), 'Client E', NULL),
    ('NS000003', DATEADD(DAY, -20, GETDATE()), DATEADD(DAY, 10, GETDATE()), 'Client F', NULL);

-- Lloguers futurs (Data_Inici posterior a avui)
INSERT INTO Lloguer (NS, Data_Inici, Data_Fi, Client, Preu)
VALUES 
    ('NS000001', DATEADD(DAY, 5, GETDATE()), DATEADD(DAY, 15, GETDATE()), 'Client A', NULL),
    ('NS000002', DATEADD(DAY, 10, GETDATE()), NULL), 'Client H', NULL),
    ('NS000004', DATEADD(DAY, 15, GETDATE()), DATEADD(DAY, 25, GETDATE()), 'Client I', NULL);
```

### CONSULTES


1. ✅ Mostra els electrodomèstics llogats actualment.  
2. ✅ Mostra els electrodomèstics llogats actualment amb el nom de la categoria.  
3. ✅ Quants lloguers estan programats per al futur?  
4. ✅ Quants lloguers estan finalitzats?  
5. ✅ Quants lloguers estan actualment actius?
6. 🔄 Quant hem facturat per categoria?
7. 🔄 Quant hem facturat per electrodomèstic? Cal que apareguin tots, posem 0 si no ha facturat res.
8. 🔄 Suma de tots els dies en què els electrodomèstics han estat llogats (Comptant també els lloguers no finalitzats fins a data fi de lloguer o fins a data d'avuí si no la sabem) (Solució a sota).  
9. 🔄 Quina és la mitjana de temps que cada electrodomèstic ha estat llogat? (Només per lloguers finalitzats).  
10. 🔄 Quina és la mitjana de temps que els electrodomèstics han estat llogats per a cada categoria? (Només per lloguers finalitzats).  
11. 🔄 Per quines categories hem facturat més de 700€?  
12. 🔄 Quines categories facturen de mitjana de lloguer més de la mitjana de facturació de tots els lloguers?  
13. 🔄 Quins clients han llogat més de 1000€?  
14. 🔄 Quins clients han llogat més de 1000€ en una sola operació?  
15. 🔄 Quins clients han llogat més de 1000€ en una sola operació i més de 2000€ en total?  
16. 🔄 Quins clients han llogat més de 1000€ en una sola operació, més de 2000€ en total i han llogat més de 3 electrodomèstics?  
17. 🚀 Quins són els electrodomèstics que han estat llogats més de 5 vegades?  
18. 🚀 Quins són els 5 electrodomèstics que han estat llogats durant més dies? (només lloguers finalitzats)
19. 🚀 Quins són els clients que han fet més lloguers?  
20. 🚀 Quins clients han llogat més electrodomèstics diferents?  
21. 🚀 Quins clients han llogat nomès electrodomèstics de la mateixa categoria?
22. 🚀 Quins clients han llogat nomès electrodomèstics de la mateixa categoria i quina és aquesta categoria?
23. 🚀 Hi ha algun electrodomèstic que no hagi estat mai llogat? (resoldre amb subquery i resoldre amb outer join)
24. 🚀 Quina és la facturació mensual per categoria?
25. 🚀 Quina és la facturació mitjana per lloguer?
26. 🚀 Hi ha algun mes en què no s’hagi fet cap lloguer? (Ull, molt difícil, fora de temari)
28. 🚀 Quin és el període més llarg sense cap lloguer? (Ull, molt difícil, fora de temari) 
29. 🚀 Quants electrodomèstics estan actualment disponibles per ser llogats?  
30. 🚀 Quina és la mitjana de temps que passa entre el final d’un lloguer i l’inici d’un nou lloguer per al mateix electrodomèstic? (Ull, molt difícil, fora de temari) 
31. 🚀 Hi ha algun client que hagi fet més d’un lloguer simultani? (Ull, molt difícil, fora de temari)
32. 🚀 Quins són els lloguers més llargs de la base de dades?  
33. 🚀 Quins electrodomèstics tenen el preu de lloguer més alt?  
34. 🚀 Quina és la mitjana de dies que un electrodomèstic roman sense ser llogat entre lloguer i lloguer? (Ull, molt difícil, fora de temari)  
36. 🚀 Quins electrodomèstics han estat llogats per més d'un client diferent?  


Resolució exercici 8: Suma de tots els dies en què els electrodomèstics han estat llogats (Comptant també els lloguers no finalitzats fins a data fi de lloguer o fins a data d'avuí si no la sabem) (Solució a sota).  
   
```sql
SELECT 
    SUM(                                  -- Calcula la suma
        DATEDIFF(                         -- Diferència entre dues dates
            DAY,                          -- Unitat de mesura de la diferència
            Data_Inici,                   -- Data "des de"
            COALESCE(                     -- Funció que retorna el primer valor no NULL
                Data_Fi,                  -- Data "fins a" si sabem la data de fi
                GETDATE()                 -- Data actual si no saben la data fi
            )                             
        )
    ) AS Dies
FROM
    Lloguer;
```


### Actualitzacions

1. Actualitza el preu de l'electrodomèstic amb NS `NS000001` a 35.00
2. Avui ens retornen l'electrodomèstic `NS000001` i Data_Inici= `DATEADD(DAY, -10, GETDATE())`.
3. El client `'Client I'` ens truca i diu que ha canviat d'opinió, que s'el quedarà 20 dies en comptes de 10.
