# Unidad 3

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
### Diagnostico del problema.
<img width="1108" height="585" alt="image" src="https://github.com/user-attachments/assets/28ada8b0-cdf0-4984-8e0a-d27d3ff927b6" />

Error 1: Copia superficial del puntero `estadisticas`.  
Qué es: al hacer `Personaje copiaHeroe = heroe;` se usa el constructor de copia por defecto, que copia la dirección del puntero, no el contenido del arreglo. Por qué ocurre (mecanismo): `heroe` y `copiaHeroe` son objetos automáticos en el stack, pero ambos contienen un puntero `int*` que apunta al mismo bloque de 3 enteros reservado con `new[]` en el heap por el constructor. Como solo se duplica el puntero (no se clona el buffer), se produce aliasing de la memoria dinámica. Consecuencia: cualquier escritura a `estadisticas` desde uno afecta al otro y, si se añadiera un destructor que hiciera `delete[]`, habría doble liberación al destruir ambos objetos que comparten el mismo puntero, lo que provoca comportamiento indefinido.

Error 2: Fuga de memoria por falta de destructor.  
Qué es: el constructor hace `estadisticas = new int[3];` pero la clase no define destructor (`~Personaje`) que invoque `delete[] estadisticas`. Por qué ocurre (mecanismo): cuando `simularEncuentro()` termina, los objetos `heroe` y `copiaHeroe` se destruyen automáticamente en el stack, pero el arreglo apuntado por `estadisticas` permanece vivo en el heap al no liberarse. Consecuencia: se pierde la referencia a ese bloque (ya no hay puntero válido que lo apunte), generando una fuga de memoria persistente; repetir este patrón en escenarios reales degrada el uso de memoria y puede llevar a agotamiento del heap y fallos por falta de memoria.
### Usa el depurador.
<img width="1875" height="536" alt="Captura de pantalla 2026-02-27 162841" src="https://github.com/user-attachments/assets/7d8232bc-3b3d-46d6-a67c-a0d633d7f057" />
Aqui se puede demostrar que si al programa le forzamos a mostrar la ranura en la que guarda las estadisticas de heroe y copia de heroe, se alcanza a ver que es en la misma direccion lo que causa que al modificar una de las dos se modifique la otra automaticamente.

<img width="1728" height="622" alt="image" src="https://github.com/user-attachments/assets/dd8622a3-9615-40bf-9e0f-cd8c381fff9b" />

## Bitácora de reflexión
### 1.Explica con tus propias palabras qué es el stack y qué es el heap en C++.
stack es un espacio de memoria mas pequeño y rapido (parecido al funcionamiento de la memoria ram en un computador)y heap es uno mas amplio y lento (parecido a el disco duro)
### 2.¿Qué diferencia hay entre una variable local, una variable global y una variable local estática? ¿En qué segmento del mapa de memoria se almacena cada una?
La variable local se crea cuando el programa llega a su linea,  Si llamas a la función 10 veces, la variable se crea y se destruye 10 veces y se guarda en el stack.
la variable global se crea al iniciar el programa, mantiene su valor toda la ejecuccion y se guarda en el segmento de datos.
