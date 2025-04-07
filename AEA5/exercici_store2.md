# Pràctica guiada: Procediment emmagatzemat per votar en una enquesta

## Context

En una aplicació d’enquestes, volem que els usuaris puguin votar una de les opcions disponibles. Cada usuari només pot votar una vegada per enquesta. A més, volem controlar que no es pugui votar més enllà de la data límit.

Aquest procediment gestionarà tot el procés de votació, assegurant integritat i controlant possibles errors.

---

## Estructura de taules (documentada)


- `usuari(id, nom)`
- `enquestes(id, text, opcio_mes_votada_id, data_max_votar)`
- `opcions(id, enquesta_id, text)`
- `vots(usuari_id, opcio_id)`


---

## Objectiu del procediment

Crear un procediment emmagatzemat anomenat `votar` amb els següents paràmetres:

- `@usuari_id INT`
- `@opcio_id INT`

Aquest procediment:

1. Comprova que l’opció existeix i pertany a una enquesta que **encara es pot votar** (`data_max_votar >= GETDATE()`).
2. Comprova que l’usuari **no hagi votat ja** en aquella enquesta.
3. Si tot és correcte, insereix el vot a la taula `vots`.
4. Fa servir una **transacció**.
5. Utilitza `TRY...CATCH` i `RAISERROR`.
6. Fa servir `SET NOCOUNT ON`.
7. Actualitza el camp `opcio_mes_votada_id` a `enquestes` si aquesta opció esdevé la més votada.

---

## Requisits i condicions

- Un usuari **només pot votar una vegada per enquesta**. L'usuari ha d'existir.
- No es pot votar **fora de termini**.
- Només es pot votar per **opcions que existeixin**.
- El procediment **no ha de retornar dades**, però pot llençar errors amb missatges útils (`RAISERROR`).
- El control d’errors i la consistència són fonamentals.

---

## Exemple de crida

```sql
EXEC votar @usuari_id = 3, @opcio_id = 12;
```

---

## Preguntes per reflexionar

1. Què passa si dos usuaris voten alhora la mateixa opció? Cal controlar concurrència? Quin nivell d'isolació has posat i perquè?
2. Si després volem permetre esborrar vots, com hauria de canviar la lògica del procediment?
3. Quin seria el millor nivell d’aïllament en un entorn amb moltes votacions simultànies?
4. Quina és l'operació que provoca més bloquejos en aquest sistema de votacions?


