# Pràctica de Programació i Base de Dades: Gestió de Comandes

## Context

Aquesta pràctica gira al voltant de la integració entre C# i SQL Server. Treballarem una lògica de negoci implementada com a procediment emmagatzemat (T-SQL) i la compararem amb la seva implementació directa des de C# mitjançant transaccions. El cas d'estudi és la gestió de comandes i l'actualització del stock.

## Recursos

- **T_SQL del Procediment emmagatzemat `fer_comanda`**:  
  [https://github.com/ctrl-alt-d/BaseDeDades/blob/main/AEA5/exercici_store.md](https://github.com/ctrl-alt-d/BaseDeDades/blob/main/AEA5/exercici_store.md)

- **Script de creació de taules i dades de prova**:  
  [https://github.com/ctrl-alt-d/BaseDeDades/blob/main/AEA5/exercici_tx.md](https://github.com/ctrl-alt-d/BaseDeDades/blob/main/AEA5/exercici_tx.md)

---

## Objectius

1. **Entendre i executar un procediment emmagatzemat des de C#**
2. **Reproduir la mateixa lògica del procediment íntegrament des de C#**
3. **Comparar els avantatges i inconvenients de cada enfocament**
4. **(Opcional) Mesurar rendiment: 5.000 comandes amb cada mètode**
5. **(Opcional) Simular execució concurrent i veure errors per concurrència**

---

## Exercicis

### Exercici 1: Execució del Procediment des de C#

- Escriu un programa en C#. Fes una funció que invoqui el procediment emmagatzemat `fer_comanda`. Comprova que funciona.

### Exercici 2: Reimplementar la lògica des de C# (sense procediment)

- Implementa una funció en C# trascrivint el T-SQL de `fer_comanda`. No oblidis obrir la transacció.
- Comprova que funciona

### Exercici 3: Comparació dels dos enfocaments

| Aspecte                      | T-SQL                           | C#                                 |
|-----------------------------|----------------------------------|-------------------------------------|
| Rendiment                   | ✅ Més ràpid                     | ❌ Més roundtrips BD <-> C#        |
| Control de versions         | ❌ Difícil                       | ✅ Integrat amb Git                |
| Manteniment i debugging     | ❌ Difícil de traçar             | ✅ Visual Studio, breakpoints...   |

---

## Opcional

### Exercici 4 (fàcil): Benchmark

- Genera 5.000 comandes fictícies (pots fer un `for` amb valors aleatoris).
- Executa-les:
  - Amb el procediment `store_order`.
  - Amb la reimplementació en C#.
- Mesura i compara el **temps total** de cadascun.

### Exercici 5 (difícil): Concurrència

- Simula múltiples threads o processos accedint al sistema alhora.
- Observa si hi ha errors de concurrència (ex: intents de modificar el mateix stock simultàniament).
- Proposa maneres de gestionar-ho (nivell d’aïllament, bloqueigs optimistes/pessimistes...).

---

## Conclusions

Reflexiona sobre:

- Quin enfocament prefereixes i per què?
- En quines situacions té més sentit fer servir T-SQL i en quines C#?


