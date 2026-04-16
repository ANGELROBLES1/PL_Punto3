# Ambigüedad en gramáticas if-then-else (Punto 3)
## Descripción

En este punto se analiza la ambigüedad presente en una gramática para estructuras condicionales tipo if-then-else.

El objetivo es demostrar que la gramática es ambigua tanto de forma teórica como práctica, y posteriormente presentar una solución que elimine dicha ambigüedad.

Gramática original

La gramática propuesta es:
``
prop -> if expr then prop 
     | prop_emparejada
prop_emparejada -> if expr then prop_emparejada else prop 
                 | otras
``
Esta gramática intenta separar estructuras completas de incompletas, pero no logra evitar completamente la ambigüedad.

Se implementó una versión simplificada en ANTLR:

Ambiguo.g4
``
grammar Ambiguo;
start : stmt+ EOF ;
stmt
    : 'if' expr 'then' stmt
    | 'if' expr 'then' stmt 'else' stmt
    | 'X'
    ;
expr : 'A' | 'B' ;
WS : [ \t\r\n]+ -> skip ;
``
Cadena de prueba
``
if A then if B then X else X
``
## Ejecución
## Generación del parser
``
antlr4 -Dlanguage=Python3 Ambiguo.g4
``
## Ejecución
``
python3 main.py
``
## Resultado del parser

Ejemplo de salida:
``
(start (stmt if A then (stmt if B then (stmt X) else (stmt X))))
``
Análisis 
La cadena evaluada:
``
if A then if B then X else X
``
puede interpretarse de dos formas diferentes.

Árbol else asociado al if interno

        if A
          |
        then
          |
        if B
       /     \
    then     else
     |         |
     X         X

Interpretación:

if A then (if B then X else X)
Árbol else asociado al if externo

        if A
       /     \
    then     else
     |         |
   if B        X
     |
    then
     |
     X


Interpretación:
``
(if A then if B then X) else X
``

La gramática es ambigua, ya que permite múltiples árboles sintácticos para una misma entrada.
