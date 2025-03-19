# Exercicis SQL

## DML

Observa aquestes taules i 

```sql
CREATE TABLE Color (
    r int not null,
    g int not null,
    b int not null,
    nom varchar(250) not null,
    CONSTRAINT Color_pk PRIMARY KEY (r, g, b),
    CONSTRAINT Color_nom UNIQUE (nom)
);
```

## DDL

### INSERSIÓ DE DADES

```SQL
INSERT INTO Color (r, g, b, nom) VALUES (255, 0, 0, 'Vermell');
INSERT INTO Color (r, g, b, nom) VALUES (0, 255, 0, 'Verd');
INSERT INTO Color (r, g, b, nom) VALUES (0, 0, 255, 'Blau');
INSERT INTO Color (r, g, b, nom) VALUES (255, 255, 0, 'Groc');
INSERT INTO Color (r, g, b, nom) VALUES (0, 255, 255, 'Cian');
INSERT INTO Color (r, g, b, nom) VALUES (255, 0, 255, 'Magenta');
INSERT INTO Color (r, g, b, nom) VALUES (255, 165, 0, 'Taronja');
INSERT INTO Color (r, g, b, nom) VALUES (128, 0, 128, 'Porpra');
INSERT INTO Color (r, g, b, nom) VALUES (165, 42, 42, 'Marró');
INSERT INTO Color (r, g, b, nom) VALUES (255, 192, 203, 'Rosa');
INSERT INTO Color (r, g, b, nom) VALUES (192, 192, 192, 'Plata');
INSERT INTO Color (r, g, b, nom) VALUES (128, 128, 128, 'Gris');
INSERT INTO Color (r, g, b, nom) VALUES (0, 0, 0, 'Negre');
INSERT INTO Color (r, g, b, nom) VALUES (255, 255, 255, 'Blanc');
INSERT INTO Color (r, g, b, nom) VALUES (0, 128, 128, 'Turquesa');
INSERT INTO Color (r, g, b, nom) VALUES (173, 216, 230, 'Blau cel');
INSERT INTO Color (r, g, b, nom) VALUES (124, 252, 0, 'Verd gespa');
INSERT INTO Color (r, g, b, nom) VALUES (240, 230, 140, 'Groc pàl·lid');
INSERT INTO Color (r, g, b, nom) VALUES (210, 105, 30, 'Xocolata');
INSERT INTO Color (r, g, b, nom) VALUES (250, 128, 114, 'Salmó');
```

### Consulta Barreja de Colors

Fes una consulta que retorni la barreja de tots els colors contra tots (exceptuant ell mateix)

| Color 1 | Codi | Color 2 | Codi | Barreja |
|---------|---------|---------|---------|---------|
| Vermell | REG(255,0,0) | Verd | RGB(0,255,0)    | RGB(128, 128, 0)


Fixa't que el color resultant s'obtè de fer la mitjana de cadascú dels tres components dels dos colors que barregem.

En T-SQL (SQL Server), pots fer servir les següents funcions per a ajudar-te amb la sentència SQL:
* Concatenar cadenes: `CONCAT()` o loperador `+`
* Convertir enters a cadena sense espais innecessaris: `CAST(valor AS VARCHAR)` o `CONVERT(VARCHAR, valor)`

Diferències entre `CONCAT()` i `+`
* `CONCAT()` converteix automàticament els enters a VARCHAR, mentre que amb + s'ha d'usar `CAST()` o `CONVERT()`.
* L'operador `+` pot fallar si algun valor és NULL, mentre que `CONCAT()` tracta NULL com una cadena buida ('').

Ex:

```sql
SELECT CONCAT('RGB(', r, ',', g, ',', b, ')') FROM Color WHERE nom = 'Vermell';
-- Resultat: "RGB(255,0,0)"
```

# Barreja HTML


* Realitza una consulta que generi codi html. El resultat que volem, per a cada filera, seria com el següent:

```html
<div>
    Quan barregem el color <span style="color: rgb(255,0,0)";>Vermell</span>
    amb el color <span style="color: rgb(0,255,0)";>Verd</span>
    obenim <span style="color: rgb(128,128,0)">aquest color</span>
</div>
```

Pots fer copy-paste de les fileres resultants en una pàgina web per veure com queda.


# Bonus track

## Hi ha colors que no destaquen, com ho solucionem?

Si us fixeu, hi ha lletres que son clares i d'altres de fosques. Si posem de color de fons el blanc i les lletres són blanques no es llegirà. Per decidir si el fons ha de ser **negre** o **blanc**, cal calcular la **lluminositat percebuda** del color resultant. Utilitzem la fórmula de **luminància relativa** (Relative Luminance), que pondera els valors RGB segons la seva percepció en l'ull humà:

```sql
Luminància = 0.2126 × R + 0.7152 × G + 0.0722 × B
```

### **Condició per posar el fons negre**
Si la luminància és **menor que 128**, el color resultant és fosc, per tant, el text hauria de tenir **fons negre**. Si és major o igual a 128, el text hauria de tenir **fons blanc**.

### **Condició SQL en T-SQL**

```sql
CASE 
    WHEN (0.2126 * r + 0.7152 * g + 0.0722 * b) < 128 THEN '#000000' -- negre
    ELSE '#ffffff' -- blanc
END
```

Aquesta condició es pot utilitzar per establir automàticament el fons adequat de cada color quan es generi el codi HTML.
