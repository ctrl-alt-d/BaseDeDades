# El concepte de claus i tipus de claus.
## DAW-MP02-UF1 - Exercici de Introducció a les bases de dades
Totes les instàncies d'una entitat tipus han de poder-se diferenciar entre elles. El mateix passa amb les tuples d'una relació. Per tant, hi ha d'haver al menys un atribut, o conjunt d'atributs, que els seus valorsidentifíquin unívocament una tupla.

Documenta els següents conceptes:

* `claus candidates`
* `claus primàries`
* `claus alternatives`

Fixa't que com que cal poder diferenciar una tupla d'una altre, podriem dir que si hagafem tots els camps ja tenim la clau. Això és cert fins a cert punt, doncs per tal de ser clau candidata ha de cumplir la condició de Minimalitat, és a dir, que no podem incloure a la clau aquells atributs que no ajuden a la identificació unívoca de la tupla.

Hi ha un tipus de claus que no serveixen per identificar la propia tupla, sino que servei per identificar una tupla forana d'una altre relació. A aquestes claus les anomenen `claus foranes`.
