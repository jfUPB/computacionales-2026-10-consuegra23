# Unidad 1

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
### Crea un programa que use un ciclo para sumar los números del 1 al 5 y guarde el resultado en la dirección de memoria 12.

~~~assembly
@12
M=0
@i
M=1

(LOOP)
    @i
    D=M
    @5
    D=D-A
    @END
    D;JGT
    @i
    D=M
    @12
    M=D+M
    @i
    M=M+1

    @LOOP
    0;JMP

(END)
    @END
    0;JMP
~~~
El funcionamiento del codigo primero es declarando los espacios de memoria el 12 y el 16 (representado por @i), lo que permite al programa funcion loop, que es controlada por un condicional que analiza una resta, D-5 y por medio del D;JGT se comprueba que la resta de mas que cero, realizara el loop mientras esto se cumpla pero apenas D llegue  a 6, la resta dara menos que cero lo que provoca que se cierre el loop inmediatamente.

## Bitácora de reflexión
