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
### 1.Describe con tus palabras las tres fases del ciclo Fetch-Decode-Execute. ¿Qué rol juega el Program Counter (PC) en este ciclo?
El ciclo Fetch–Decode–Execute consiste en que la CPU primero busca la siguiente instrucción en memoria usando la dirección almacenada en el Program Counter (PC) y la carga en el registro de instrucción; luego decodifica esa instrucción para entender qué operación debe realizar y qué datos o direcciones involucra; finalmente ejecuta la instrucción, ya sea realizando una operación aritmética, accediendo a memoria o cambiando el flujo del programa. Durante este proceso, el PC normalmente se incrementa para señalar la siguiente instrucción, pero puede cambiar si la instrucción es un salto, permitiendo bucles y decisiones.
### 2.¿Cuál es la diferencia fundamental entre una instrucción-A (que empieza con @) y una instrucción-C (que involucra D, M, A, etc.) en el lenguaje ensamblador de Hack? Da un ejemplo de cada una.
En el lenguaje ensamblador, una instrucción‑A (que empieza con @) sirve para cargar un valor numérico o una dirección de memoria en el registro A, preparando a la CPU para acceder a datos o realizar saltos, por ejemplo: `@100` coloca el valor 100 en el registro A; en cambio, una instrucción‑C es la que realiza operaciones computacionales, como cálculos en la ALU, asignaciones a registros o saltos condicionales, e involucra componentes como `D`, `A` y `M`, por ejemplo: `D=M+1` toma el valor almacenado en la dirección apuntada por A, le suma 1 y guarda el resultado en D.
Otros ejemplos:
Intruccion-A: @300

Intruccion-C(tipo D): D = D + 1

Intruccion-C(tipo M): @12, M = 0

Intruccion-C(tipo A): @50, D=D-A

### 3.Explica la función de los siguientes componentes del computador Hack: el registro D, el registro A y la ALU.
En el computador Hack (de Nand2Tetris), el registro D, el registro A y la ALU son componentes esenciales que cooperan para ejecutar instrucciones:

El registro D es un registro de propósito general usado principalmente para almacenar valores temporales y resultados intermedios durante los cálculos; no puede direccionar memoria, solo guardar datos. El registro A cumple una doble función: puede almacenar valores numéricos utilizados en cálculos, pero también actúa como registro de direcciones, ya que su contenido selecciona qué posición de memoria se accede como M. Finalmente, la ALU (Unidad Aritmético‑Lógica) es el componente que realiza todas las operaciones computacionales, como sumas, restas, negaciones, comparaciones y operaciones lógicas, tomando como entrada los valores de D y ya sea A o M, y produciendo un resultado junto con banderas que permiten ejecutar saltos condicionales. Estos tres elementos trabajan juntos: A indica la dirección o valor, D guarda datos, y la ALU realiza el cálculo usando ambas entradas.
### 4.¿Cómo se implementa un salto condicional en Hack? Describe un ejemplo (p. ej., saltar si el valor de D es mayor que cero).
En Hack, un salto condicional se realiza con una instrucción‑C de la forma comp ; jump, donde la ALU calcula comp y, según las banderas resultantes (ZR = cero, NG = negativo), el campo jump decide si el PC carga la dirección del registro A (salta) o simplemente avanza al siguiente comando. Importante: el destino del salto está en A, así que primero debes cargar la dirección/etiqueta con una instrucción‑A.
~~~ assembly
// Supongamos que D ya tiene algún valor computado antes.
@POS       // 1) Cargar en A la dirección de la etiqueta POS
D;JGT      // 2) Si D > 0 (ZR=0 y NG=0), PC := A  -> salta a (POS)
           //    Si no, PC := PC+1 -> sigue secuencia normal

// Código si D <= 0
@END
0;JMP      // Salto incondicional para evitar ejecutar la rama POS

(POS)
// Código cuando D > 0

(END)
// Continuació
~~~
### 5.¿Cómo se implementa un loop en el computador Hack? Describe un ejemplo (p. ej., un loop que decremente un valor hasta que llegue a cero).
En Hack, un loop se implementa con una etiqueta que marca el inicio del ciclo y un salto (condicional o incondicional) que regresa a esa etiqueta mientras la condición se cumpla. La condición se evalúa con una instrucción‑C (comp;jump), y el destino del salto está en el registro A, que debes cargar previamente (ya sea con una etiqueta o con una dirección numérica).
Ejemplo: loop que decrementa un valor hasta llegar a cero
Supongamos que el valor inicial está en RAM[10] (dirección 10). El loop restará 1 y continuará mientras el valor sea distinto de 0.
~~~ assembly
@5
D=A
@10
M=D
(LOOP)
    @10
    D=M
    @END
    D;JEQ 
    @10
    M=M-1
    @LOOP
    0;JMP
(END)
    @END
    0;JMP
~~~
### 6. ¿Cuál es la diferencia entre la instrucción D=M y la instrucción M=D?
La diferencia es que `D = M` copia el contenido de la memoria hacia el registro D, es decir, lee el valor almacenado en la dirección apuntada por A y lo carga en D, mientras que `M = D` copia el contenido del registro D hacia la memoria, es decir, escribe en la dirección apuntada por A el valor que ya tenía D; en resumen, `D=M` es una operación de lectura desde memoria hacia un registro, mientras que `M=D` es una operación de escritura desde un registro hacia memoria.
### 7. Describe brevemente qué se necesita para leer un valor del teclado (KBD) y para “pintar” un pixel en la pantalla (SCREEN).
Para leer del teclado (KBD) en Hack basta con acceder a la dirección de memoria `KBD` (dirección 24576): el hardware coloca allí continuamente el código de la tecla presionada, por lo que solo necesitas poner `@KBD` y luego usar `D=M` para leer su valor. Para pintar un pixel en la pantalla (SCREEN) debes escribir en la memoria que empieza en `SCREEN` (dirección 16384), donde cada palabra de 16 bits controla 16 píxeles consecutivos**; así, basta con poner la dirección correcta en*A (`@SCREEN`, o un offset como `@SCREEN+5`) y luego escribir con `M=D` un patrón de bits donde los 1 encienden píxeles y los 0 los apagan.

