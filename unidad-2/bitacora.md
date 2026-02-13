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

En la primera imagen en la memoria 16 y 17 se guardan ambos numeros el 10 y el 20.

<img width="685" height="824" alt="image" src="https://github.com/user-attachments/assets/1c193988-4b75-4f6b-8963-db4056e36e66" />

En esta se crea en lo que la explicacion defini como a& y b& que estan en la memoria 15 y 18 respectivamente, uno 22 y otro 10 son compias de los datos 16 y 17 respectivamente(aunque el 16 no sea exacto posee esa funcion) funcionando para reemplazar a los 16 y 17 eventualmente en una poscicion diferente.

<img width="706" height="823" alt="Captura de pantalla 2026-02-11 165201" src="https://github.com/user-attachments/assets/e5822721-2396-4f2b-9e44-2b69ccca4927" />

Aqui la transformacion ya se realizo y los datos estan de la posicion del otro.

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

Primero el diseño
<img width="1310" height="561" alt="image" src="https://github.com/user-attachments/assets/ccd33709-30da-4a29-9930-505750411cb4" />

Este fue el diseño realizado, una carita feliz sencilla y regular.
~~~
	function void draw(int location) {
	var int memAddress; 
	let memAddress = 16384+location;
	// column 0
	do Memory.poke(memAddress, 4112);
	do Memory.poke(memAddress +32, 4112);
	do Memory.poke(memAddress +64, 4112);
	do Memory.poke(memAddress +96, 4112);
	do Memory.poke(memAddress +128, 4112);
	do Memory.poke(memAddress +160, 4112);
	do Memory.poke(memAddress +192, 4112);
	do Memory.poke(memAddress +224, 4112);
	do Memory.poke(memAddress +288, 1);
	do Memory.poke(memAddress +320, 1);
	do Memory.poke(memAddress +352, -32766);
	do Memory.poke(memAddress +384, 24580);
	do Memory.poke(memAddress +416, 12312);
	do Memory.poke(memAddress +448, 4064);
	// column 1
	do Memory.poke(memAddress +289, 1);
	do Memory.poke(memAddress +321, 1);
	do Memory.poke(memAddress +353, 1);
	return;
}
~~~
este es su bitmap y los puntos en la memoria en la que fue guardada

~~~ assembly
(draw)
	// put bitmap location value in R12
	// put code return address in R13
	@SCREEN
	D=A
	@R12
	AD=D+M
	// row 4
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 5
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 6
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 7
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 8
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 9
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 10
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 11
	D=A // D holds previous addr
	@32
	AD=D+A
	@4112 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 13
	D=A // D holds previous addr
	@64
	AD=D+A
	M=1
	AD=A+1 // D holds addr
	M=1
	// row 14
	D=A // D holds previous addr
	@31
	AD=D+A
	M=1
	AD=A+1 // D holds addr
	M=1
	// row 15
	D=A // D holds previous addr
	@31
	AD=D+A
	@32766 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=A-D // RAM[addr]=-val
	AD=A+1 // D holds addr
	M=1
	// row 16
	D=A // D holds previous addr
	@31
	AD=D+A
	@24580 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 17
	D=A // D holds previous addr
	@32
	AD=D+A
	@12312 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// row 18
	D=A // D holds previous addr
	@32
	AD=D+A
	@4064 // A holds val
	D=D+A // D = addr + val
	A=D-A // A=addr + val - val = addr
	M=D-A // RAM[addr] = val
	// return
	@R13
	A=M
	D;JMP

~~~
este seria el codigo de assembly. 
ahora el proceso seria modificar el codigo para que pueda generar la imagen borrando y escribiendo en pantalla con las teclas d y e

<img width="813" height="266" alt="image" src="https://github.com/user-attachments/assets/b3577426-79f3-4f15-abb3-5276f18b6f5b" />
Asi se veria en assembly.
ahora es aplicar el codigo y que se vea el como se genera lentamente y se pueda borrar con las teclas d y e. para eso utilizare la inteligencia artificial claude.
Como primer prompt se le otorgo a la ia el codigo anteriormente mencionado y se le pidio analizarlo a completa profundidad sin modificarlo ni intentar mejorarlo.
<img width="722" height="451" alt="image" src="https://github.com/user-attachments/assets/18249d55-2c2c-4975-a5a8-40765fa8781d" />
<img width="734" height="356" alt="image" src="https://github.com/user-attachments/assets/08549ae7-308a-4ef3-a400-031c046fbc15" />
Adjunto la conversacion con la inteligencia artificial para la planteacion del codigo. las secciones de p y r, son P preguntas que hace la ia para pedir aclaraciones de como funciona el codigo y r son respuestas que yo le di, tomando en cuenta que me daba opciones de respuesta multiple.
~~~assembly
// ============================================================
// Carita feliz - dibuja/borra bit a bit con teclas D(68) y E(69)
//
// Entrada:
//   R12 = offset base dentro de SCREEN
//   R13 = dirección de retorno
//
// Estado persistente entre llamadas:
//   R0  = índice del bit actual (0=vacío, 37=completo)
//   R14 = flag init (0=no inicializado, 1=ya listo)
//
// Tabla de bits en RAM: R16..R89
//   Entrada i → R[16+i*2] = offset_word, R[16+i*2+1] = mascara
//
// Scratch: R1=dirección word, R2=máscara, R3=índice*2
// ============================================================

(draw)
    // ¿Ya fue inicializada la tabla?
    @R14
    D=M
    @draw_main
    D;JNE

// ============================================================
// INIT: copiar tabla a RAM[16..89]
// ============================================================
(init_tabla)
    // Entrada 0: offset=128, mask=4096
    @128
    D=A
    @16
    M=D
    @4096
    D=A
    @17
    M=D

    // Entrada 1: offset=128, mask=16
    @128
    D=A
    @18
    M=D
    @16
    D=A
    @19
    M=D

    // Entrada 2: offset=160, mask=4096
    @160
    D=A
    @20
    M=D
    @4096
    D=A
    @21
    M=D

    // Entrada 3: offset=160, mask=16
    @160
    D=A
    @22
    M=D
    @16
    D=A
    @23
    M=D

    // Entrada 4: offset=192, mask=4096
    @192
    D=A
    @24
    M=D
    @4096
    D=A
    @25
    M=D

    // Entrada 5: offset=192, mask=16
    @192
    D=A
    @26
    M=D
    @16
    D=A
    @27
    M=D

    // Entrada 6: offset=224, mask=4096
    @224
    D=A
    @28
    M=D
    @4096
    D=A
    @29
    M=D

    // Entrada 7: offset=224, mask=16
    @224
    D=A
    @30
    M=D
    @16
    D=A
    @31
    M=D

    // Entrada 8: offset=256, mask=4096
    @256
    D=A
    @32
    M=D
    @4096
    D=A
    @33
    M=D

    // Entrada 9: offset=256, mask=16
    @256
    D=A
    @34
    M=D
    @16
    D=A
    @35
    M=D

    // Entrada 10: offset=288, mask=4096
    @288
    D=A
    @36
    M=D
    @4096
    D=A
    @37
    M=D

    // Entrada 11: offset=288, mask=16
    @288
    D=A
    @38
    M=D
    @16
    D=A
    @39
    M=D

    // Entrada 12: offset=320, mask=4096
    @320
    D=A
    @40
    M=D
    @4096
    D=A
    @41
    M=D

    // Entrada 13: offset=320, mask=16
    @320
    D=A
    @42
    M=D
    @16
    D=A
    @43
    M=D

    // Entrada 14: offset=352, mask=4096
    @352
    D=A
    @44
    M=D
    @4096
    D=A
    @45
    M=D

    // Entrada 15: offset=352, mask=16
    @352
    D=A
    @46
    M=D
    @16
    D=A
    @47
    M=D

    // Entrada 16: offset=416, mask=1
    @416
    D=A
    @48
    M=D
    @1
    D=A
    @49
    M=D

    // Entrada 17: offset=417, mask=1
    @417
    D=A
    @50
    M=D
    @1
    D=A
    @51
    M=D

    // Entrada 18: offset=448, mask=1
    @448
    D=A
    @52
    M=D
    @1
    D=A
    @53
    M=D

    // Entrada 19: offset=449, mask=1
    @449
    D=A
    @54
    M=D
    @1
    D=A
    @55
    M=D

    // Entrada 20: offset=480, mask=2
    @480
    D=A
    @56
    M=D
    @2
    D=A
    @57
    M=D

    // Entrada 21: offset=480, mask=-32768
    @480
    D=A
    @58
    M=D
    @32767
    D=A
    D=D+1    // D = -32768 en 16-bit (overflow intencional)
    @59
    M=D

    // Entrada 22: offset=481, mask=1
    @481
    D=A
    @60
    M=D
    @1
    D=A
    @61
    M=D

    // Entrada 23: offset=512, mask=16384
    @512
    D=A
    @62
    M=D
    @16384
    D=A
    @63
    M=D

    // Entrada 24: offset=512, mask=8192
    @512
    D=A
    @64
    M=D
    @8192
    D=A
    @65
    M=D

    // Entrada 25: offset=512, mask=4
    @512
    D=A
    @66
    M=D
    @4
    D=A
    @67
    M=D

    // Entrada 26: offset=544, mask=8192
    @544
    D=A
    @68
    M=D
    @8192
    D=A
    @69
    M=D

    // Entrada 27: offset=544, mask=4096
    @544
    D=A
    @70
    M=D
    @4096
    D=A
    @71
    M=D

    // Entrada 28: offset=544, mask=16
    @544
    D=A
    @72
    M=D
    @16
    D=A
    @73
    M=D

    // Entrada 29: offset=544, mask=8
    @544
    D=A
    @74
    M=D
    @8
    D=A
    @75
    M=D

    // Entrada 30: offset=576, mask=2048
    @576
    D=A
    @76
    M=D
    @2048
    D=A
    @77
    M=D

    // Entrada 31: offset=576, mask=1024
    @576
    D=A
    @78
    M=D
    @1024
    D=A
    @79
    M=D

    // Entrada 32: offset=576, mask=512
    @576
    D=A
    @80
    M=D
    @512
    D=A
    @81
    M=D

    // Entrada 33: offset=576, mask=256
    @576
    D=A
    @82
    M=D
    @256
    D=A
    @83
    M=D

    // Entrada 34: offset=576, mask=128
    @576
    D=A
    @84
    M=D
    @128
    D=A
    @85
    M=D

    // Entrada 35: offset=576, mask=64
    @576
    D=A
    @86
    M=D
    @64
    D=A
    @87
    M=D

    // Entrada 36: offset=576, mask=32
    @576
    D=A
    @88
    M=D
    @32
    D=A
    @89
    M=D

    // Marcar como inicializado
    @1
    D=A
    @R14
    M=D

// ============================================================
// LOOP PRINCIPAL
// ============================================================
(draw_main)

    // Esperar a que se suelte cualquier tecla (debounce)
(wait_release)
    @KBD
    D=M
    @wait_release
    D;JNE

    // Esperar D(68) o E(69)
(wait_key)
    @KBD
    D=M
    @68
    D=D-A
    @pressed_D
    D;JEQ

    @KBD
    D=M
    @69
    D=D-A
    @pressed_E
    D;JEQ

    @wait_key
    0;JMP

// ============================================================
// PRESIONARON D: encender el siguiente bit
// ============================================================
(pressed_D)
    // Verificar R0 < 37
    @R0
    D=M
    @37
    D=D-A
    @wait_release
    D;JGE          // cara completa, ignorar

    // R3 = R0 * 2
    @R0
    D=M
    @R3
    M=D
    D=D+M          // D = R0*2
    @R3
    M=D

    // Leer offset: RAM[16 + R3]
    @16
    D=A
    @R3
    A=D+M          // A = base + R3
    D=M            // D = offset

    // Dirección absoluta: SCREEN + R12 + offset
    @SCREEN
    D=D+A
    @R12
    D=D+M
    @R1
    M=D            // R1 = dirección del word

    // Leer máscara: RAM[16 + R3 + 1]
    @16
    D=A
    @R3
    D=D+M          // D = base + R3
    @1
    A=D+A          // A = base + R3 + 1
    D=M
    @R2
    M=D            // R2 = máscara

    // Encender bit: RAM[R1] += R2
    @R1
    A=M
    D=M
    @R2
    D=D+M
    @R1
    A=M
    M=D

    // Avanzar índice
    @R0
    M=M+1

    @wait_release
    0;JMP

// ============================================================
// PRESIONARON E: apagar el último bit
// ============================================================
(pressed_E)
    // Verificar R0 > 0
    @R0
    D=M
    @wait_release
    D;JEQ

    // Retroceder índice
    @R0
    M=M-1

    // R3 = R0 * 2
    @R0
    D=M
    @R3
    M=D
    D=D+M
    @R3
    M=D

    // Leer offset: RAM[16 + R3]
    @16
    D=A
    @R3
    A=D+M
    D=M
    @SCREEN
    D=D+A
    @R12
    D=D+M
    @R1
    M=D

    // Leer máscara: RAM[16 + R3 + 1]
    @16
    D=A
    @R3
    D=D+M
    @1
    A=D+A
    D=M
    @R2
    M=D

    // Apagar bit: RAM[R1] -= R2
    @R1
    A=M
    D=M
    @R2
    D=D-M
    @R1
    A=M
    M=D

    @wait_release
    0;JMP

// ============================================================
// RETORNO AL LLAMADOR
// ============================================================
(draw_end)
    @R13
    A=M
    0;JMP
~~~
Este fue el primer codigo entregado por la ia, a pesar de que detecta las teclas, no funciona, pues no inica nada, el segundo intento se va a realizar cambiando que en vez de que vaya pixel por pixel va a ir linea por linea, esperando que se simplifique su funcionamiento y logre funcionar correctamente.
~~~ assembly
// ============================================================
// Carita feliz - dibuja/borra línea a línea con D(68) y E(69)
//
// Entrada:
//   R12 = offset base dentro de SCREEN
//   R13 = dirección de retorno
//
// R0 = paso actual (0=vacío, 17=completo)
// R0 debe estar en 0 antes de la primera llamada.
// ============================================================

(draw)

    // Esperar que se suelte cualquier tecla (debounce)
(wait_release)
    @KBD
    D=M
    @wait_release
    D;JNE

    // Esperar D(68) o E(69)
(wait_key)
    @KBD
    D=M
    @68
    D=D-A
    @pressed_D
    D;JEQ

    @KBD
    D=M
    @69
    D=D-A
    @pressed_E
    D;JEQ

    @wait_key
    0;JMP

// ============================================================
// D presionada: dibujar el siguiente paso
// ============================================================
(pressed_D)
    @R0
    D=M
    @17
    D=D-A
    @wait_release
    D;JGE          // R0 >= 17: cara completa, ignorar

    // Switch por R0: saltar al paso correspondiente
    @R0
    D=M
    @draw_paso_0
    D;JEQ
    @R0
    D=M
    @1
    D=D-A
    @draw_paso_1
    D;JEQ
    @R0
    D=M
    @2
    D=D-A
    @draw_paso_2
    D;JEQ
    @R0
    D=M
    @3
    D=D-A
    @draw_paso_3
    D;JEQ
    @R0
    D=M
    @4
    D=D-A
    @draw_paso_4
    D;JEQ
    @R0
    D=M
    @5
    D=D-A
    @draw_paso_5
    D;JEQ
    @R0
    D=M
    @6
    D=D-A
    @draw_paso_6
    D;JEQ
    @R0
    D=M
    @7
    D=D-A
    @draw_paso_7
    D;JEQ
    @R0
    D=M
    @8
    D=D-A
    @draw_paso_8
    D;JEQ
    @R0
    D=M
    @9
    D=D-A
    @draw_paso_9
    D;JEQ
    @R0
    D=M
    @10
    D=D-A
    @draw_paso_10
    D;JEQ
    @R0
    D=M
    @11
    D=D-A
    @draw_paso_11
    D;JEQ
    @R0
    D=M
    @12
    D=D-A
    @draw_paso_12
    D;JEQ
    @R0
    D=M
    @13
    D=D-A
    @draw_paso_13
    D;JEQ
    @R0
    D=M
    @14
    D=D-A
    @draw_paso_14
    D;JEQ
    @R0
    D=M
    @15
    D=D-A
    @draw_paso_15
    D;JEQ
    @R0
    D=M
    @16
    D=D-A
    @draw_paso_16
    D;JEQ

(draw_paso_0)
    // Paso 0: offset=128, valor=4112
    @128
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_1)
    // Paso 1: offset=160, valor=4112
    @160
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_2)
    // Paso 2: offset=192, valor=4112
    @192
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_3)
    // Paso 3: offset=224, valor=4112
    @224
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_4)
    // Paso 4: offset=256, valor=4112
    @256
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_5)
    // Paso 5: offset=288, valor=4112
    @288
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_6)
    // Paso 6: offset=320, valor=4112
    @320
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_7)
    // Paso 7: offset=352, valor=4112
    @352
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4112       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_8)
    // Paso 8: offset=416, valor=1
    @416
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @1       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_9)
    // Paso 9: offset=417, valor=1
    @417
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @1       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_10)
    // Paso 10: offset=448, valor=1
    @448
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @1       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_11)
    // Paso 11: offset=449, valor=1
    @449
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @1       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_12)
    // Paso 12: offset=480, valor=-32766
    @480
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @32766     // A = |valor|
    D=D+A          // D = addr + |val|
    A=D-A          // A = addr
    M=A-D          // RAM[addr] = -|val| = valor negativo
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_13)
    // Paso 13: offset=481, valor=1
    @481
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @1       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_14)
    // Paso 14: offset=512, valor=24580
    @512
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @24580       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_15)
    // Paso 15: offset=544, valor=12312
    @544
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @12312       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

(draw_paso_16)
    // Paso 16: offset=576, valor=4064
    @576
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M          // D = dirección absoluta
    @4064       // A = valor
    D=D+A          // D = addr + val
    A=D-A          // A = addr
    M=D-A          // RAM[addr] = val
    @R0
    M=M+1          // R0++
    @wait_release
    0;JMP

// ============================================================
// E presionada: borrar el último paso
// ============================================================
(pressed_E)
    @R0
    D=M
    @wait_release
    D;JEQ          // R0 == 0: nada que borrar

    // Retroceder índice primero
    @R0
    M=M-1

    // Switch por R0: saltar al borrado correspondiente
    @R0
    D=M
    @erase_paso_0
    D;JEQ
    @R0
    D=M
    @1
    D=D-A
    @erase_paso_1
    D;JEQ
    @R0
    D=M
    @2
    D=D-A
    @erase_paso_2
    D;JEQ
    @R0
    D=M
    @3
    D=D-A
    @erase_paso_3
    D;JEQ
    @R0
    D=M
    @4
    D=D-A
    @erase_paso_4
    D;JEQ
    @R0
    D=M
    @5
    D=D-A
    @erase_paso_5
    D;JEQ
    @R0
    D=M
    @6
    D=D-A
    @erase_paso_6
    D;JEQ
    @R0
    D=M
    @7
    D=D-A
    @erase_paso_7
    D;JEQ
    @R0
    D=M
    @8
    D=D-A
    @erase_paso_8
    D;JEQ
    @R0
    D=M
    @9
    D=D-A
    @erase_paso_9
    D;JEQ
    @R0
    D=M
    @10
    D=D-A
    @erase_paso_10
    D;JEQ
    @R0
    D=M
    @11
    D=D-A
    @erase_paso_11
    D;JEQ
    @R0
    D=M
    @12
    D=D-A
    @erase_paso_12
    D;JEQ
    @R0
    D=M
    @13
    D=D-A
    @erase_paso_13
    D;JEQ
    @R0
    D=M
    @14
    D=D-A
    @erase_paso_14
    D;JEQ
    @R0
    D=M
    @15
    D=D-A
    @erase_paso_15
    D;JEQ
    @R0
    D=M
    @16
    D=D-A
    @erase_paso_16
    D;JEQ

(erase_paso_0)
    // Borrar paso 0: offset=128
    @128
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_1)
    // Borrar paso 1: offset=160
    @160
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_2)
    // Borrar paso 2: offset=192
    @192
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_3)
    // Borrar paso 3: offset=224
    @224
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_4)
    // Borrar paso 4: offset=256
    @256
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_5)
    // Borrar paso 5: offset=288
    @288
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_6)
    // Borrar paso 6: offset=320
    @320
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_7)
    // Borrar paso 7: offset=352
    @352
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_8)
    // Borrar paso 8: offset=416
    @416
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_9)
    // Borrar paso 9: offset=417
    @417
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_10)
    // Borrar paso 10: offset=448
    @448
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_11)
    // Borrar paso 11: offset=449
    @449
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_12)
    // Borrar paso 12: offset=480
    @480
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_13)
    // Borrar paso 13: offset=481
    @481
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_14)
    // Borrar paso 14: offset=512
    @512
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_15)
    // Borrar paso 15: offset=544
    @544
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

(erase_paso_16)
    // Borrar paso 16: offset=576
    @576
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M
    A=D
    M=0            // borrar word
    @wait_release
    0;JMP

// ============================================================
// Retorno al llamador
// ============================================================
(draw_end)
    @R13
    A=M
    0;JMP
~~~
el segundo codigo tambien fue un fracaso, al intentar sobrecorregir lo anterior con una nueva logica termino siendo extremadamente largo y no funciono tampoco.
a la ia se le pidio iniciar desde 0 como si no hubiera hecho intentos anteriores
~~~ assembly
// ============================================================
// Carita feliz - dibuja/borra fila a fila con D(68) y E(69)
//
// Entrada:
//   R12 = offset base dentro de SCREEN
//   R13 = dirección de retorno
//
// R0  = paso actual (0=vacío, 17=completo). Inicializar en 0.
// R1  = scratch (dirección temporal)
// R15 = scratch (valor del teclado)
// ============================================================

(draw)

// ---- Esperar que no haya ninguna tecla presionada ----
(wait_release)
    @KBD
    D=M
    @wait_release
    D;JNE        // mientras KBD != 0, esperar

// ---- Esperar D(68) o E(69) ----
(wait_key)
    @KBD
    D=M
    @R15
    M=D          // guardar tecla en R15
    @68
    D=D-A
    @pressed_D
    D;JEQ
    @R15
    D=M
    @69
    D=D-A
    @pressed_E
    D;JEQ
    @wait_key
    0;JMP

// ============================================================
// D: dibujar el siguiente paso
// ============================================================
(pressed_D)
    @R0
    D=M
    @17
    D=D-A
    @wait_release
    D;JGE        // R0 >= 17: ya está completo

    // saltar según R0
    @R0
    D=M
    @draw_paso_0
    D;JEQ
    @R0
    D=M
    @1
    D=D-A
    @draw_paso_1
    D;JEQ
    @R0
    D=M
    @2
    D=D-A
    @draw_paso_2
    D;JEQ
    @R0
    D=M
    @3
    D=D-A
    @draw_paso_3
    D;JEQ
    @R0
    D=M
    @4
    D=D-A
    @draw_paso_4
    D;JEQ
    @R0
    D=M
    @5
    D=D-A
    @draw_paso_5
    D;JEQ
    @R0
    D=M
    @6
    D=D-A
    @draw_paso_6
    D;JEQ
    @R0
    D=M
    @7
    D=D-A
    @draw_paso_7
    D;JEQ
    @R0
    D=M
    @8
    D=D-A
    @draw_paso_8
    D;JEQ
    @R0
    D=M
    @9
    D=D-A
    @draw_paso_9
    D;JEQ
    @R0
    D=M
    @10
    D=D-A
    @draw_paso_10
    D;JEQ
    @R0
    D=M
    @11
    D=D-A
    @draw_paso_11
    D;JEQ
    @R0
    D=M
    @12
    D=D-A
    @draw_paso_12
    D;JEQ
    @R0
    D=M
    @13
    D=D-A
    @draw_paso_13
    D;JEQ
    @R0
    D=M
    @14
    D=D-A
    @draw_paso_14
    D;JEQ
    @R0
    D=M
    @15
    D=D-A
    @draw_paso_15
    D;JEQ
    @R0
    D=M
    @16
    D=D-A
    @draw_paso_16
    D;JEQ

(draw_paso_0)  // offset=128 valor=4112
    @128
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_1)  // offset=160 valor=4112
    @160
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_2)  // offset=192 valor=4112
    @192
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_3)  // offset=224 valor=4112
    @224
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_4)  // offset=256 valor=4112
    @256
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_5)  // offset=288 valor=4112
    @288
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_6)  // offset=320 valor=4112
    @320
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_7)  // offset=352 valor=4112
    @352
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4112
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_8)  // offset=416 valor=1
    @416
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @1
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_9)  // offset=417 valor=1
    @417
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @1
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_10)  // offset=448 valor=1
    @448
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @1
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_11)  // offset=449 valor=1
    @449
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @1
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_12)  // offset=480 valor=-32766
    @480
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @32767
    D=A
    D=D+1        // D = -32768 (overflow 16-bit)
    @2
    D=D+A        // D = -32768 + 2 = -32766
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_13)  // offset=481 valor=1
    @481
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @1
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_14)  // offset=512 valor=24580
    @512
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @24580
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_15)  // offset=544 valor=12312
    @544
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @12312
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

(draw_paso_16)  // offset=576 valor=4064
    @576
    D=A
    @SCREEN
    D=D+A
    @R12
    D=D+M        // D = dirección absoluta
    @R1
    M=D          // R1 = dirección
    @4064
    D=A
    @R1
    A=M          // A = dirección
    M=D          // RAM[dirección] = valor
    @R0
    M=M+1
    @wait_release
    0;JMP

// ============================================================
// E: borrar el último paso
// ============================================================
(pressed_E)
    @R0
    D=M
    @wait_release
    D;JEQ        // R0 == 0: nada que borrar
    @R0
    M=M-1        // retroceder índice

    // saltar según R0
    @R0
    D=M
    @erase_paso_0
    D;JEQ
    @R0
    D=M
    @1
    D=D-A
    @erase_paso_1
    D;JEQ
    @R0
    D=M
    @2
    D=D-A
    @erase_paso_2
    D;JEQ
    @R0
    D=M
    @3
    D=D-A
    @erase_paso_3
    D;JEQ
    @R0
    D=M
    @4
    D=D-A
    @erase_paso_4
    D;JEQ
    @R0
    D=M
    @5
    D=D-A
    @erase_paso_5
    D;JEQ
    @R0
    D=M
    @6
    D=D-A
    @erase_paso_6
    D;JEQ
    @R0
    D=M
    @7
    D=D-A
    @erase_paso_7
    D;JEQ
    @R0
    D=M
    @8
    D=D-A
    @erase_paso_8
    D;JEQ
    @R0
    D=M
    @9
    D=D-A
    @erase_paso_9
    D;JEQ
    @R0
    D=M
    @10
    D=D-A
    @erase_paso_10
    D;JEQ
    @R0
    D=M
    @11
    D=D-A
    @erase_paso_11
    D;JEQ
    @R0
    D=M
    @12
    D=D-A
    @erase_paso_12
    D;JEQ
    @R0
    D=M
    @13
    D=D-A
    @erase_paso_13
    D;JEQ
    @R0
    D=M
    @14
    D=D-A
    @erase_paso_14
    D;JEQ
    @R0
    D=M
    @15
    D=D-A
    @erase_paso_15
    D;JEQ
    @R0
    D=M
    @16
    D=D-A
    @erase_paso_16
    D;JEQ

(erase_paso_0)  // offset=128
    @128
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_1)  // offset=160
    @160
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_2)  // offset=192
    @192
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_3)  // offset=224
    @224
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_4)  // offset=256
    @256
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_5)  // offset=288
    @288
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_6)  // offset=320
    @320
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_7)  // offset=352
    @352
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_8)  // offset=416
    @416
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_9)  // offset=417
    @417
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_10)  // offset=448
    @448
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_11)  // offset=449
    @449
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_12)  // offset=480
    @480
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_13)  // offset=481
    @481
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_14)  // offset=512
    @512
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_15)  // offset=544
    @544
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

(erase_paso_16)  // offset=576
    @576
    D=A
    @SCREEN
    D=D+A
    @R12
    A=D+M        // A = dirección absoluta
    M=0          // borrar word
    @wait_release
    0;JMP

// ============================================================
// Retorno al llamador
// ============================================================
(draw_end)
    @R13
    A=M
    0;JMP
~~~
El ultimo y la version 3 del codigo tampoco funciono. :(
