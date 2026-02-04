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



## Bitácora de reflexión
