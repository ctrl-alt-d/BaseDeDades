# Enunciat examen

### Observa aquests inserts:

```sql
INSERT INTO Color (r, g, b, nom) VALUES (255, 0, 0, 'Vermell');
INSERT INTO Color (r, g, b, nom) VALUES (0, 255, 0, 'Verd');
INSERT INTO Color (r, g, b, nom) VALUES (0, 0, 255, 'Blau');
-- etc
```

1) (1 punt) Exercici: Esborra tots els colors amb Luminància inferior a 128:

```sql
Luminància = 0.2126 × R + 0.7152 × G + 0.0722 × B
```

2) (0.5 punts) Exercici: Actualitza el nom de tots els colors que tinguin més de 200 de component R o més de 128 de component G. Posa el nom en blanc (cadena buida). Sense tocar la resta.

### Observa aquests inserts

```sql
INSERT INTO Treballador(id, departament, nom, salari) VALUES (1, 'Vendes', 'Dani', 100);
```

3) (2 punts) Exercici: Selecciona tots els treballadors que guanyen més de la mitjana de tots els treballadors. Pots fer servir una subconsulta.
4) (2 punts) Exercici: Mostra, per cada departament, la suma de salaris dels seus treballadors. Ordena de més salari a menys. En cas de mateix salari, per ordre alfabètic de nom de departament. No mostris els departaments amb menys de 3 treballadors.

### Observa aquests inserts

```sql
INSERT INTO Raca (Codi, Nom) VALUES (100, 'Pastor Alemany');
INSERT INTO Gossos (num_xip, nom, data_neix, raca) VALUES (1, 'Boby', '2020-06-01', 100);
INSERT INTO Gossos (num_xip, nom, data_neix, raca) VALUES (2, 'Layka', '2020-08-01', Null);
```

5) (2 punts) Exercici: Mostra, per a cada raça, quants gossos tenim a la gossera (sense subqueries) cal que apareguin totes les races. Recorda que el `count(*)` fa recompte de fileres i et demana quants gossos hi ha. Justifica a sota de la consulta l'expressió d'agregació que facis servir.
6) (0.5 punts) Exercici: Esborra tots els gossos que amb `data_neix` anterior a 1 gener de 2020.
7) (1 punt) Exercici: Creua tots els gossos contra tots (exepte ells mateixos) mostrant la diferència de dies entre que va nèixer un i l'altre:

| gos 1 | gos 2 | diferència |
|---|----|----|
| boby | layka | 61  |
| layka | boby | -61  |


8) (1 punt) Problema: A la consulta anterior, pensa com ho pots fer per tal que no apareguin dos cops cada parella de gossos, és a dir, en comptes de trobar boby-layka, layka-boby, que només trobem boby-layka. No facis servir `Distinct` ni `group by`.



# Solucions

### 1) Esborra tots els colors amb Luminància inferior a 128

**Luminància = 0.2126 × R + 0.7152 × G + 0.0722 × B**

```sql
DELETE FROM Color
WHERE (0.2126 * r + 0.7152 * g + 0.0722 * b) < 128;
```

**Explicació:**
- Utilitzem la fórmula de luminància per calcular la lluminositat de cada color.
- Eliminem aquells registres on el resultat de la fórmula és inferior a 128.

---

### 2) Actualitza el nom de tots els colors que tinguin més de 200 de component R o més de 128 de component G. Posa el nom en blanc (cadena buida). Sense tocar la resta.

```sql
UPDATE Color
SET nom = ''
WHERE r > 200 OR g > 128;
```

**Explicació:**
- La condició `r > 200 OR g > 128` selecciona els colors amb una component vermella superior a 200
  o una component verda superior a 128.
- Llavors, el seu `nom` es canvia per una cadena buida. Recorda que `null` no és cadena buida. Recorda que les cadenes, a SQL, es delimiten amb `'` i no pas amb `"`.

---

### 3) Selecciona tots els treballadors que guanyen més de la mitjana de tots els treballadors. Pots fer servir una subconsulta.

```sql
SELECT * 
FROM Treballador 
WHERE salari > (SELECT AVG(salari) FROM Treballador);
```

**Explicació:**
- La subconsulta `SELECT AVG(salari) FROM Treballador` calcula el salari mitjà de tots els treballadors.
- Després, seleccionem només aquells que tenen un salari superior a aquest valor.

---

### 4) Mostra, per cada departament, la suma de salaris dels seus treballadors. Ordena de més salari a menys. En cas de mateix salari, per ordre alfabètic de nom de departament. No mostris els departaments amb menys de 3 treballadors.

```sql
SELECT departament, SUM(salari) AS total_salari
FROM Treballador
GROUP BY departament
HAVING COUNT(*) >= 3
ORDER BY SUM(salari) DESC, departament ASC;
```

**Explicació:**
- `GROUP BY departament` agrupa els treballadors per departament.
- `SUM(salari)` calcula la suma dels salaris de cada departament.
- `HAVING COUNT(*) >= 3` assegura que només mostrem departaments amb 3 o més treballadors.
- `ORDER BY SUM(salari) DESC, departament ASC` ordena per salari total (descendent) i alfabèticament en cas d'empat.

---

### 5) Mostra, per a cada raça, quants gossos tenim a la gossera (sense subqueries). Cal que apareguin totes les races.

```sql
SELECT Raca.Nom, COUNT(Gossos.num_xip) AS num_gossos
FROM Raca
LEFT JOIN Gossos ON Raca.Codi = Gossos.raca
GROUP BY Raca.Nom;
```

**Explicació:**
- `LEFT JOIN` assegura que es mostrin totes les races, encara que no tinguin gossos.
- `COUNT(Gossos.num_xip)` compta el nombre de gossos per raça. A diferència de `COUNT(*)` que faria recompte de fileres del grup, i sempre trobaria al menys una filera perquè fem servir `LEFT JOIN`.
- `GROUP BY Raca.Nom` agrupa els resultats per raça.

---

### 6) Esborra tots els gossos amb `data_neix` anterior a 1 gener de 2020.

```sql
DELETE FROM Gossos
WHERE data_neix < '2020-01-01';
```

**Explicació:**
- La condició `data_neix < '2020-01-01'` selecciona tots els gossos nascuts abans del 2020.
- `DELETE FROM Gossos` els elimina de la taula.

---

### 7) Creua tots els gossos contra tots (excepte ells mateixos) mostrant la diferència de dies entre que va néixer un i l'altre:

| gos 1 | gos 2 | diferència |
|---|----|----|
| boby | layka | 61  |
| layka | boby | -61  |

```sql
SELECT g1.nom AS gos1, g2.nom AS gos2, DATEDIFF(DAY, g1.data_neix, g2.data_neix) AS diferencia
FROM Gossos g1
CROSS JOIN Gossos g2 
WHERE g1.num_xip <> g2.num_xip;
```

**Explicació:**
- `CROSS JOIN Gossos g2` fa el producte cartesià, tots contra tots. També es pot fer servir simplement `JOIN`. 
- `g1.num_xip <> g2.num_xip` per tal d'eliminar les parelles d'un gos contra ell mateix. També podem fer servir `!=`.
- `DATEDIFF(DAY, g1.data_neix, g2.data_neix)` calcula la diferència en dies entre les dates de naixement. És una de les poques funcions (que no són funció d'agregació) vistes a classe (juntament amb el `CONCAT`)

Aquest exercici també es podia resoldre així, però no ho hem vist a classe:

```sql
SELECT g1.nom AS gos1, g2.nom AS gos2, DATEDIFF(DAY, g1.data_neix, g2.data_neix) AS diferencia
FROM Gossos g1
JOIN Gossos g2 ON g1.num_xip <> g2.num_xip;
```

---

### 8) A la consulta anterior, pensa com ho pots fer per tal que no apareguin dos cops cada parella de gossos, és a dir, en comptes de trobar boby-layka, layka-boby, que només trobem boby-layka. No facis servir `DISTINCT` ni `GROUP BY`.

```sql
SELECT g1.nom AS gos1, g2.nom AS gos2, DATEDIFF(g1.data_neix, g2.data_neix) AS diferencia
FROM Gossos g1
CROSS JOIN Gossos g2 
WHERE g1.num_xip > g2.num_xip;
```

**Explicació:**
- En lloc de comparar `g1.num_xip <> g2.num_xip`, utilitzem `g1.num_xip > g2.num_xip`.
- Això assegura que cada parella només apareix una vegada, ja que sempre es tria el gos amb el número de xip més gran com `g1`.

Aquest exercici també es podia resoldre així, però no ho hem vist a classe:

```sql
SELECT g1.nom AS gos1, g2.nom AS gos2, DATEDIFF(DAY, g1.data_neix, g2.data_neix) AS diferencia
FROM Gossos g1
JOIN Gossos g2 ON g1.num_xip > g2.num_xip;
```
