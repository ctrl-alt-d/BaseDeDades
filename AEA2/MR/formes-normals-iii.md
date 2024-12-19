# Tercera Forma Normal (3FN)  
## DAW-MP02-UF1 - Exercici d'Introducció a les Bases de Dades  

### **Definició**

> Una taula està en **tercera forma normal (3FN)** si i només si compleix amb les dues condicions següents:
> - La relació R (taula) està en **segona forma normal (2FN)**.  
> - Cada atribut no primer de R no depèn transitivament de cap clau primària de R.  

Un **atribut no primer** és aquell que no forma part de cap clau candidata. Una **dependència transitiva** es dona quan un atribut depèn funcionalment d'un altre atribut que no forma part de la clau primària.

---

### **Interpretació**

La tercera forma normal ens diu que:  
1. No poden haver **dependències transitives**.  
2. Cal complir prèviament la **segona forma normal (2FN)**.

#### Dependència transitiva:
Quan un atribut depèn d'un altre atribut o conjunt d'atributs que **no formen part de la clau primària**.

#### Solució:
- Mou els atributs que depenen transitivament de la clau primària a una altra relació juntament amb una còpia del seu determinant, que actuarà com a clau primària a la nova taula.

---

### **Exemple**

Relació inicial:  

| &Alumne& | Població     | Comarca      |
|------------|-------------|--------------|
| Pere       | Roses       | Alt Empordà  |
| Marta      | Olot        | La Garrotxa  |

- Clau primària: **Alumne**  
- **Problema:** L'atribut *Comarca* depèn transitivament de *Alumne*, perquè depèn de *Població*.  

#### Pas a 3FN:

**Taula 1: Alumnes (3FN):**  

| &Alumne& | Població     |
|------------|-------------|
| Pere       | Roses       |
| Marta      | Olot        |

**Taula 2: Poblacions (3FN):**  

| &Població& | Comarca      |
|--------------|--------------|
| Roses        | Alt Empordà  |
| Olot         | La Garrotxa  |

---

## **Exercicis**

### **1. Normalitza aquesta relació:**

Relació inicial:  

| &Jugador& | Country       | &Dia&     | MàximaPuntuació | Continent |
|-------------|---------------|-------------|-----------------|-----------|
| Zoom        | Cat           | 1/9/2016    | 24K            | Europe    |
| Zoom        | Cat           | 2/9/2016    | 23K            | Europe    |
| Creep       | Italy         | 1/9/2016    | 45K            | Europe    |
| Creep       | Italy         | 2/9/2016    | 13K            | Europe    |
| Nation      | Algery        | 2/9/2016    | 78K            | Europe    |
| Smoker      | Saudi Arabia  | 2/9/2016    | 67K            | Asia      |

#### Solució:

1. **Comprova si està en 3FN:**  
   - Clau primària: **Jugador** + **Dia**.  
   - Problema: L'atribut *Continent* depèn transitivament de la clau primària, ja que depèn de *Country*.  

2. **Pas a 3FN:**  
   - Mou l'atribut *Continent* a una nova taula juntament amb *Country*.

**Taula 1: JugadorDia (3FN):**  

| &Jugador& | &Dia&     | MàximaPuntuació |
|-------------|-------------|-----------------|
| Zoom        | 1/9/2016    | 24K            |
| Zoom        | 2/9/2016    | 23K            |
| Creep       | 1/9/2016    | 45K            |
| Creep       | 2/9/2016    | 13K            |
| Nation      | 2/9/2016    | 78K            |
| Smoker      | 2/9/2016    | 67K            |

**Taula 2: Countries (3FN):**  

| &Country&      | Continent |
|------------------|-----------|
| Cat              | Europe    |
| Italy            | Europe    |
| Algery           | Europe    |
| Saudi Arabia     | Asia      |

---

### **2. Observa i normalitza les preguntes de StackOverflow:**

#### Pregunta 1: [How can I have multiple items or elements in a SQL cell?](http://stackoverflow.com/questions/8593609/how-can-i-have-multiple-items-or-element-in-a-sql-cell)  
**Problema:** L'OP emmagatzema diversos elements separats per comes en una cel·la.  

**Solució:**  
1. **Despivotar** la informació per complir la 1FN.  
2. Si hi ha dependències transitives, normalitzar a 3FN seguint els passos anteriors.

#### Pregunta 2: [T-SQL text sorting](http://stackoverflow.com/questions/8976703/t-sql-text-sorting)  
**Problema:** Els valors multivaluats estan separats per comes dins d'una mateixa columna.  

**Solució:**  
1. **Despivotar** els valors en files separades.  
2. Garantir que cada columna conté un sol valor i aplicar la 3FN.

#### Pregunta 3: [SQL Server 2008 R2 Multiple Row Maths](http://stackoverflow.com/questions/18631178/sql-server-2008-r2-multiple-row-maths)  
**Problema:** L'OP utilitza una estructura inadequada per fer càlculs entre files.  

**Solució:**  
1. **Despivotar** les dades per complir la 1FN.  
2. Crear relacions adequades en 3FN per garantir càlculs fiables i consistents.

---

### **Conclusions:**  
La tercera forma normal elimina dependències transitives i garanteix que cada atribut depèn directament de la clau primària. Això millora la integritat i la mantenibilitat de les dades.
