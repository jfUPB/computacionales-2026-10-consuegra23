# Unidad 2

## Bitácora de proceso de aprendizaje
~~~ assembly
(START)

// i = SCREEN
@SCREEN
D=A
@i
M=D
//Inicio la pantalla pintando una linea
@SCREEN
M=-1

(LOOP)
@KBD 
D=M
@100
D=D-A
@derecha
D;JEQ

@KBD 
D=M
@105
D=D-A
@izquierda
D;JEQ
@LOOP
0;JMP


(derecha)
@i
A=M
M=0
A=A+1
M=-1
D=A
@i
M=D
@LOOP
0;JMP

(izquierda)
@i
A=M
M=0
A=A-1
M=-1
D=A
@i
M=D
@LOOP
0;JMP



@fin
(fin)
0;JMP
~~~
<img width="1244" height="890" alt="image" src="https://github.com/user-attachments/assets/d3298be1-7f2b-4aea-85f8-5ff74ab3d4bb" />

## Bitácora de aplicación 

### Actividad 08.
~~~ assembly
@10
D=A
@a
M=D


@20
D=A
@b
M=D


@a
D=A
@R0
M=D


@b
D=A
@R1
M=D


@returnFromSwap
D=A
@R15
M=D


@swap
0;JMP


(returnFromSwap)

@fin
(fin)
0;JMP


(swap)
    @R0
    A=M     
    D=M    
    @TMP
    M=D     


    @R1
    A=M     
    D=M     
    @R0
    A=M     
    M=D    

    @TMP
    D=M  
    @R1
    A=M  
    M=D 

    @R15
    A=M
    0;JMP
~~~
Como explicación general.
Primero se inicializan las variables del “main”: se guardan los valores 10 y 20 en las posiciones simbólicas a y b (ubicadas por el ensamblador a partir de la RAM 16). Para llamar a swap, se pasa la dirección de a en R0 y la dirección de b en R1 (equivalentes a &a y &b), y se almacena en R15 la dirección de retorno etiquetada como returnFromSwap. Luego se realiza un salto incondicional a la función swap. Al regresar, el programa entra en el bucle fin para detener la ejecución.
Sobre la función swap.
La función interpreta R0 y R1 como punteros: desreferencia R0 para leer *pa y lo guarda en el temporal TMP; después desreferencia R1 para leer *pb y lo escribe en la dirección apuntada por R0 (efectuando *pa = *pb); por último, toma TMP y lo escribe en la dirección apuntada por R1 (*pb = tmp). Con esto se completa el intercambio de valores. Para finalizar, carga A con la dirección guardada en R15 y salta allí, restaurando el flujo a returnFromSwap.
<img width="684" height="827" alt="image" src="https://github.com/user-attachments/assets/1efd004d-200f-4d5f-9003-1b79222cac4b" />

<img width="685" height="824" alt="image" src="https://github.com/user-attachments/assets/1c193988-4b75-4f6b-8963-db4056e36e66" />

<img width="706" height="823" alt="Captura de pantalla 2026-02-11 165201" src="https://github.com/user-attachments/assets/e5822721-2396-4f2b-9e44-2b69ccca4927" />
~~~ assembly

@10
D=A
@arr0
M=D

@15
D=A
@arr1
M=D

@2
D=A
@arr2
M=D

@3
D=A
@arr3
M=D

@50
D=A
@arr4
M=D


@arr0
D=A
@R0
M=D

@5
D=A
@R1
M=D

@returnFromCalSum
D=A
@R15
M=D
@calSum
0;JMP

(returnFromCalSum)
@R0
D=M
@sum
M=D

@fin
(fin)
0;JMP




(calSum)
    @SUM
    M=0

    @I
    M=0

(calSum_LOOP)
    @I
    D=M        
    @R1
    D=D-M       
    @calSum_END
    D;JGE       


    @R0
    D=M


    @I
    A=M     
    D=D+A        


    @ADDR
    M=D

    @ADDR
    A=M
    D=M

    @SUM
    M=D+M

    @I
    M=M+1

    @calSum_LOOP
    0;JMP

(calSum_END)
    @SUM
    D=M
    @R0
    M=D
    @R15
    A=M
    0;JMP
~~~
Como explicación general.
Primero se materializa en memoria el arreglo del main: se crean cinco celdas consecutivas (arr0…arr4) y se cargan los valores 10, 15, 2, 3 y 50, respetando la disposición contigua que tendría int arr[] en C++. Para llamar a calSum, se sigue la misma convención ya usada: en R0 se guarda el puntero al primer elemento (&arr0) y en R1 el tamaño del arreglo (5). Además, se coloca en R15 la dirección de retorno (returnFromCalSum) y se salta a la etiqueta calSum. Cuando la función termina, el valor devuelto queda en R0, y main lo copia a la variable simbólica sum. Finalmente, el programa entra en el bucle fin para detenerse.
Sobre la función calSum.
La función interpreta R0 como puntero base del arreglo y R1 como el tamaño. Inicializa SUM = 0 (acumulador) e I = 0 (índice). En el bucle, compara I con R1 y, mientras I < arrSize, calcula la dirección efectiva del elemento actual como parr + i: para ello, toma la dirección base desde R0, suma el índice I y guarda el resultado en ADDR. Luego desreferencia ADDR (pone A=M y lee M) para obtener el valor del elemento y lo acumula en SUM. Incrementa I y repite. Al finalizar, mueve SUM a R0 para retornar el total y salta a la dirección guardada en R15, de modo que la ejecución vuelve a returnFromCalSum en main, donde el resultado queda almacenado en sum (en este ejemplo, 80).
<img width="1240" height="824" alt="Captura de pantalla 2026-02-11 165927" src="https://github.com/user-attachments/assets/785f7bfa-25fe-43d4-b335-297a5c5a93ce" />
Como se puede ver de la posicion de memoria 16 a la 20 estan guardado el array de (10,15,2,3,50)
<img width="681" height="797" alt="image" src="https://github.com/user-attachments/assets/fc355b03-83a0-47ed-9d0b-9485e93e4750" />
Aqui inicia para comensar la suma, en la memoria 22 esta puesto el numero 10 el primero de la lista y se empieza una sumatoria secundaria en la memoria 23 que es un contador para saber en que numero nos encontramos actualmente
<img width="1242" height="868" alt="Captura de pantalla 2026-02-11 165947" src="https://github.com/user-attachments/assets/c641b3b7-e805-4f1e-b392-3a9b64cc2f58" />
La suma ya ha empezado, en la memoria 22 se puede ver que ya esta colocado el numero 25 (10+15) y en el 23 se genera un contador que muestra en que numero del array se encuentra actualmente en este caso acaba de pasar por el segundo numero
<img width="1236" height="857" alt="Captura de pantalla 2026-02-11 170020" src="https://github.com/user-attachments/assets/dd80fafe-abf6-4ee4-9ae6-509eca36f90b" />
en la ultima captura se muestra como ambos bloques de memoria el 22 y el 23 han terminado sus ciclos, la 22 quedo en la suma total que debia ser 80 y la 23 completo el contador pues ya paso por el ultimo numero




## Bitácora de reflexión



