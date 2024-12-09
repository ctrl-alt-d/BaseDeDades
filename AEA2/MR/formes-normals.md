# Introducció a les Formes Normals

Les **formes normals** són un conjunt de regles utilitzades en el disseny de bases de dades relacionals per organitzar les dades i reduir la redundància i anomalies en les actualitzacions, insercions o eliminacions. Aquest procés es coneix com a **normalització**.

El principal objectiu de les formes normals és:

1. **Evitar la duplicació de dades**: Reduir la redundància emmagatzemant dades de forma eficient.
2. **Millorar la consistència**: Garantir que les dades es mantinguin coherents i sense conflictes.
3. **Facilitar el manteniment**: Simplificar l’estructura de la base de dades per fer més fàcils les operacions d’actualització i consulta.

Cada forma normal té un conjunt específic de requisits que una taula ha de complir per assolir-la. Aquestes normes s'apliquen de manera seqüencial, començant per la Primera Forma Normal (1NF), i passant per la Segona (2NF) i la Tercera (3NF), i fins i tot més enllà en dissenys avançats.

En aquesta sèrie, explicarem les tres primeres formes normals, que són les més importants i utilitzades en el disseny pràctic de bases de dades.

---

# Exemple de Base de Dades Mal Normalitzada

Suposem que tenim una taula `Comandes` on es guarden informació de les comandes i detalls dels clients. Aquesta taula no està normalitzada i pot provocar problemes de consistència, redundància i anomalies en les operacions.

### Taula `Comandes`

| ID_Comanda | Nom_Client | Adreça               | Producte   | Quantitat | Preu_Unitari | Total  |
|------------|------------|----------------------|------------|-----------|--------------|--------|
| 1          | Anna López | C/ Major, 10        | Llibre     | 2         | 10.00€       | 20.00€ |
| 2          | Joan Ferrer| C/ Nou, 15          | Bolígrafs  | 5         | 2.00€        | 10.00€ |
| 3          | Anna López | C/ Major, 10        | Llibreta   | 3         | 5.00€        | 15.00€ |

---

## Problemes en aquesta estructura

1. **Redundància de Dades**
   - El nom i l’adreça de `Anna López` es repeteixen a les comandes 1 i 3.
   - Això incrementa l’espai d’emmagatzematge i dificulta el manteniment de les dades.

2. **Anomalies d’Actualització**
   - Si l’adreça de `Anna López` canvia, hem de modificar-la a totes les files on aparegui. Si ens oblidem d’alguna fila, les dades seran inconsistents.

3. **Anomalies d’Inserció**
   - No podem registrar un nou client sense que faci una comanda, ja que el camp `Producte` quedaria buit, violant les restriccions de la base de dades.

4. **Anomalies d’Eliminació**
   - Si eliminem totes les comandes d’un client, també perdríem la informació sobre aquest client.

---

Aquest tipus de problemes es poden solucionar aplicant les formes normals, començant per separar la informació en taules diferents amb relacions adequades.

# Primera Forma Normal (1NF)

La **Primera Forma Normal (1NF)** exigeix que una taula compleixi els següents criteris:

1. **Atomicitat dels valors**: Cada camp (o cel·la) ha de contenir només un valor indivisible, no una llista o conjunt de valors.
2. **No hi ha files duplicades**: Cada fila ha de ser única i identificable mitjançant una clau primària.
3. **Les columnes representen una sola propietat**: Cada columna ha de representar només un atribut de l’entitat.

---

## Exemple: Taula No en 1NF

Suposem que tenim la següent taula de `Comandes`:

| ID_Comanda | Nom_Client | Productes                | Quantitats | Preu_Total |
|------------|------------|--------------------------|------------|------------|
| 1          | Anna López | Llibre, Bolígrafs       | 2, 5       | 30.00€     |
| 2          | Joan Ferrer| Llibreta                | 3          | 15.00€     |

### Problemes:
- **Valors no atòmics**: Les columnes `Productes` i `Quantitats` contenen múltiples valors (llistes).
- **Dificultats d’actualització**: Si volem modificar el preu o la quantitat d’un producte específic, hem de treballar amb dades en forma de llistes, cosa que complica el manteniment.

---

## Taula en 1NF

Per convertir aquesta taula en 1NF, dividim els valors no atòmics en files individuals. Cada fila conté una sola instància d'un producte i la seva quantitat.

| ID_Comanda | Nom_Client | Producte    | Quantitat | Preu_Total |
|------------|------------|-------------|-----------|------------|
| 1          | Anna López | Llibre      | 2         | 20.00€     |
| 1          | Anna López | Bolígrafs   | 5         | 10.00€     |
| 2          | Joan Ferrer| Llibreta    | 3         | 15.00€     |

### Millores:
1. Els **valors són atòmics**: Cada cel·la conté només un valor.
2. És més fàcil realitzar consultes, actualitzacions i mantenir consistència.
3. S’ha eliminat la redundància de columnes amb múltiples valors.

---

Amb la taula en 1NF, podem treballar de manera més eficaç amb les dades i establir relacions amb altres taules per a una millor organització.

# Segona Forma Normal (2NF)

La **Segona Forma Normal (2NF)** s’aplica a taules que ja compleixen la Primera Forma Normal (1NF) i afegeix una condició addicional:

1. **Ha de complir la 1NF.**
2. **Elimina la dependència parcial dels atributs en claus compostes:** Tots els atributs no clau han de dependre completament de la clau primària i no només d’una part d’una clau composta.

Aquest principi busca evitar que informació que no depèn totalment de la clau primària es repeteixi innecessàriament.

---

## Exemple: Taula No en 2NF

Suposem una taula `Comandes` que ja està en 1NF:

| ID_Comanda | Producte    | Nom_Client | Adreça             | Quantitat |
|------------|-------------|------------|--------------------|-----------|
| 1          | Llibre      | Anna López | C/ Major, 10       | 2         |
| 1          | Bolígrafs   | Anna López | C/ Major, 10       | 5         |
| 2          | Llibreta    | Joan Ferrer| C/ Nou, 15         | 3         |

### Problemes:
- La clau primària és composta: `{ID_Comanda, Producte}`.
- Les columnes `Nom_Client` i `Adreça` depenen només de `ID_Comanda`, però no de `Producte`. Això genera **dependències parcials**, que poden provocar redundància:
  - Si un client fa diverses comandes amb diferents productes, `Nom_Client` i `Adreça` es repeteixen per cada producte.

---

## Taula en 2NF

Per normalitzar la taula i portar-la a la 2NF, dividim la informació en dues taules separades per eliminar les dependències parcials.

### Taula `Comandes` (Clau Primària: `ID_Comanda`)
| ID_Comanda | Nom_Client | Adreça             |
|------------|------------|--------------------|
| 1          | Anna López | C/ Major, 10       |
| 2          | Joan Ferrer| C/ Nou, 15         |

### Taula `Detall_Comandes` (Clau Primària: `{ID_Comanda, Producte}`)
| ID_Comanda | Producte    | Quantitat |
|------------|-------------|-----------|
| 1          | Llibre      | 2         |
| 1          | Bolígrafs   | 5         |
| 2          | Llibreta    | 3         |

### Millores:
1. Els atributs `Nom_Client` i `Adreça` ara depenen completament de `ID_Comanda` (no hi ha dependències parcials).
2. La taula `Detall_Comandes` s’encarrega de relacionar els productes amb les comandes, sense redundància d’informació dels clients.

---

Amb la taula en 2NF, hem eliminat la redundància i les anomalies associades a les dependències parcials, preparant la base de dades per a una millor consistència i eficiència.

# Tercera Forma Normal (3NF)

La **Tercera Forma Normal (3NF)** s’aplica a taules que ja compleixen la Segona Forma Normal (2NF) i afegeix un altre requisit:

1. **Ha de complir la 2NF.**
2. **No hi ha dependències transitives:** Cap atribut no clau ha de dependre d’un altre atribut no clau. Tots els atributs no clau han de dependre únicament de la clau primària.

Aquesta regla busca eliminar redundàncies que poden sorgir quan un atribut no clau depèn d’un altre atribut no clau.

---

## Exemple: Taula No en 3NF

Suposem que tenim la següent taula `Comandes` en 2NF:

| ID_Comanda | ID_Client | Nom_Client | Adreça             | Producte    | Quantitat |
|------------|-----------|------------|--------------------|-------------|-----------|
| 1          | 101       | Anna López | C/ Major, 10       | Llibre      | 2         |
| 1          | 101       | Anna López | C/ Major, 10       | Bolígrafs   | 5         |
| 2          | 102       | Joan Ferrer| C/ Nou, 15         | Llibreta    | 3         |

### Problemes:
- Hi ha una **dependència transitiva**: 
  - `Nom_Client` i `Adreça` depenen de `ID_Client`, i no directament de `ID_Comanda`. Això genera redundància:
    - Si l’adreça de `Anna López` canvia, hem de modificar múltiples files.

---

## Taula en 3NF

Per portar la taula a la 3NF, cal dividir-la en més taules per eliminar la dependència transitiva.

### Taula `Comandes` (Clau Primària: `ID_Comanda`)
| ID_Comanda | ID_Client |
|------------|-----------|
| 1          | 101       |
| 2          | 102       |

### Taula `Clients` (Clau Primària: `ID_Client`)
| ID_Client | Nom_Client | Adreça             |
|-----------|------------|--------------------|
| 101       | Anna López | C/ Major, 10       |
| 102       | Joan Ferrer| C/ Nou, 15         |

### Taula `Detall_Comandes` (Clau Primària: `{ID_Comanda, Producte}`)
| ID_Comanda | Producte    | Quantitat |
|------------|-------------|-----------|
| 1          | Llibre      | 2         |
| 1          | Bolígrafs   | 5         |
| 2          | Llibreta    | 3         |

### Millores:
1. Ara, `Nom_Client` i `Adreça` depenen directament de `ID_Client` (no hi ha dependències transitives).
2. Les dades estan millor organitzades i no hi ha redundàncies.
3. Si l’adreça d’un client canvia, només cal modificar-la en la taula `Clients`.

---

Amb aquesta normalització, la base de dades compleix la 3NF, evitant anomalies i redundàncies, i millorant la integritat de les dades.

# Exercici: Passar una Taula a la Tercera Forma Normal (3NF)

## Enunciat

Tens la següent taula `Comandes_Clients` que no està normalitzada. El teu objectiu és transformar-la fins a la **Tercera Forma Normal (3NF)**.

### Taula Inicial: `Comandes_Clients`

| ID_Comanda | Nom_Client  | Adreça              | Producte     | Quantitat | Preu_Unitari | Total |
|------------|-------------|---------------------|--------------|-----------|--------------|-------|
| 1          | Anna López  | C/ Major, 10        | Llibre       | 2         | 10.00€       | 20.00€|
| 1          | Anna López  | C/ Major, 10        | Bolígrafs    | 5         | 2.00€        | 10.00€|
| 2          | Joan Ferrer | C/ Nou, 15          | Llibreta     | 3         | 5.00€        | 15.00€|

---

### Objectiu

1. **Identificar les dependències transitives i parcials.**
2. **Separar les dades en taules noves per complir la 3NF.**
3. **Crear les claus primàries i foranes necessàries per relacionar les noves taules.**

---

### Preguntes per Guiar l’Exercici

1. Quina és la clau primària de la taula original?
2. Quins atributs no clau depenen directament de la clau primària?
3. Hi ha atributs que depenen d’un altre atribut no clau? Quins?
4. Com pots dividir la taula per eliminar redundàncies i dependències transitives?

---

### Tasques

1. Crear una taula per guardar la informació dels **clients**.
2. Crear una taula per guardar la informació de les **comandes**.
3. Crear una taula per guardar els **detalls de les comandes** (productes i quantitats).
4. Especificar les relacions entre les noves taules mitjançant claus foranes.

---

### Solució Proposada

Després de completar l'exercici, la taula original s’hauria de transformar en un conjunt de taules que compleixen la 3NF, amb claus primàries i foranes correctament definides. Comprova si has arribat a una estructura similar a l'exemple següent:

1. Taula `Clients`
2. Taula `Comandes`
3. Taula `Detall_Comandes`

# Exercici: Passar una Taula d'Hospital a la Tercera Forma Normal (3NF)

## Enunciat

Suposem que tenim la següent taula `Pacients_Visites` que no està normalitzada. Conté informació sobre pacients, metges i visites a l'hospital. El teu objectiu és transformar-la fins a la **Tercera Forma Normal (3NF)**.

### Taula Inicial: `Pacients_Visites`

| ID_Visita | Nom_Pacient   | Adreça_Pacient        | Metge_Assignat | Especialitat   | Data_Visita | Cost |
|-----------|---------------|-----------------------|----------------|----------------|-------------|------|
| 1         | Anna López    | C/ Major, 10         | Dr. Ferrer     | Cardiologia    | 2024-01-10  | 100€ |
| 2         | Joan Ferrer   | C/ Nou, 15           | Dr. Gómez      | Dermatologia   | 2024-01-15  | 80€  |
| 3         | Anna López    | C/ Major, 10         | Dr. Ferrer     | Cardiologia    | 2024-02-05  | 100€ |

---

## Objectiu

1. **Identificar les dependències transitives i parcials.**
2. **Transformar la taula en diverses taules que compleixin la 3NF.**
3. **Definir les claus primàries i foranes necessàries.**

---

### Preguntes per Guiar l’Exercici

1. Quina és la clau primària de la taula original?
2. Hi ha dades que es repeteixen innecessàriament? Quines?
3. Quins atributs no clau depenen directament de la clau primària?
4. Hi ha atributs que depenen d’altres atributs no clau? Quins?

---

### Tasques

1. Crea una taula per guardar la informació dels **pacients** (amb el seu nom i adreça).
2. Crea una taula per guardar la informació dels **metges** (amb el seu nom i especialitat).
3. Crea una taula per guardar les **visites** (amb la informació del pacient, metge, data i cost).
4. Defineix les relacions entre aquestes taules utilitzant claus primàries i foranes.

---

### Solució Proposada

Després de completar l'exercici, la taula `Pacients_Visites` hauria de transformar-se en:

1. **Taula `Pacients`**:
   - Clau primària: `ID_Pacient`.
   - Atributs: `Nom_Pacient`, `Adreça_Pacient`.

2. **Taula `Metges`**:
   - Clau primària: `ID_Metge`.
   - Atributs: `Nom_Metge`, `Especialitat`.

3. **Taula `Visites`**:
   - Clau primària: `ID_Visita`.
   - Claus foranes: `ID_Pacient`, `ID_Metge`.
   - Atributs: `Data_Visita`, `Cost`.

