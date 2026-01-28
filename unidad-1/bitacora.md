# Unidad 1

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
### Crea un programa que use un ciclo para sumar los números del 1 al 5 y guarde el resultado en la dirección de memoria 12.
~~~
Memoria(12) = 0
i = 1
while(i<=5)
{
Memoria(12) = Memoria(12) + i
i = i + 1
}
Memoria(12)<- Suma acumulada = 15
~~~
Con Ayuda del profesor se realizo el anterior seudo codigo que tiene el funcionamiento que deberia de tener el programa
~~~ assembly
@12
M = 0
i = 1
@i
M = 1
@i
D=M
@12
M = D+M
~~~
Despues todavia con ayuda del profesor, se tradujo parte del codigo a assembly, lo que permitio que se pueda terminar la idea
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
<img width="965" height="804" alt="image" src="https://github.com/user-attachments/assets/43778c14-4aa8-4002-8e8b-e9dbeed96f7b" />

## Bitácora de reflexión

