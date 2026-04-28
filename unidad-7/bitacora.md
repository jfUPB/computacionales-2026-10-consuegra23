# Unidad 7

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
triangle.cpp
~~~ C++
#include <iostream>
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <cmath>

void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height);
}

void processInput(GLFWwindow* window) {
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}

const unsigned int SCR_WIDTH = 400;
const unsigned int SCR_HEIGHT = 400;

// ??? CAMBIO 1: se añade uniform uOffset para controlar la posición ???????????
const char* vertexShaderSrc = R"glsl(
    #version 460 core
    layout(location = 0) in vec3 aPos;

    uniform vec2 uOffset;          // posición dinámica del triángulo

    void main() {
        vec3 pos = aPos;
        pos.x += uOffset.x;
        pos.y += uOffset.y;
        gl_Position = vec4(pos, 1.0);
    }
)glsl";

// ??? CAMBIO 2: se añade uniform uColor para el color dinámico ????????????????
const char* fragmentShaderSrc = R"glsl(
    #version 460 core
    out vec4 FragColor;

    uniform vec4 uColor;           // color dinámico del triángulo

    void main() {
        FragColor = uColor;
    }
)glsl";

unsigned int VAO, VBO;
unsigned int shaderProg;

unsigned int buildShaderProgram() {
    int  success;
    char log[512];

    unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(vs, 1, &vertexShaderSrc, nullptr);
    glCompileShader(vs);
    glGetShaderiv(vs, GL_COMPILE_STATUS, &success);
    if (!success) { glGetShaderInfoLog(vs, 512, nullptr, log); std::cerr << "ERROR VERTEX SHADER:\n" << log << "\n"; }

    unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(fs, 1, &fragmentShaderSrc, nullptr);
    glCompileShader(fs);
    glGetShaderiv(fs, GL_COMPILE_STATUS, &success);
    if (!success) { glGetShaderInfoLog(fs, 512, nullptr, log); std::cerr << "ERROR FRAGMENT SHADER:\n" << log << "\n"; }

    unsigned int prog = glCreateProgram();
    glAttachShader(prog, vs);
    glAttachShader(prog, fs);
    glLinkProgram(prog);
    glGetProgramiv(prog, GL_LINK_STATUS, &success);
    if (!success) { glGetProgramInfoLog(prog, 512, nullptr, log); std::cerr << "ERROR LINKING PROGRAM:\n" << log << "\n"; }

    glDeleteShader(vs);
    glDeleteShader(fs);
    return prog;
}

void setupTriangle() {
    float vertices[] = {
        -0.5f, -0.5f, 0.0f,
         0.5f, -0.5f, 0.0f,
         0.0f,  0.5f, 0.0f
    };
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);

    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);
    glBindVertexArray(0);
}

int main()
{
    if (!glfwInit()) { std::cerr << "Fallo al inicializar GLFW\n"; return -1; }
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    GLFWwindow* mainWindow = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "Triangulo Dinamico", nullptr, nullptr);
    if (!mainWindow) { std::cerr << "Error creando ventana\n"; glfwTerminate(); return -1; }

    int bufferWidth, bufferHeight;
    glfwGetFramebufferSize(mainWindow, &bufferWidth, &bufferHeight);
    glfwSetFramebufferSizeCallback(mainWindow, framebuffer_size_callback);
    glfwMakeContextCurrent(mainWindow);

    if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
        std::cerr << "Fallo al cargar GLAD\n"; return -1;
    }

    glfwSwapInterval(1);
    shaderProg = buildShaderProgram();
    setupTriangle();
    glViewport(0, 0, bufferWidth, bufferHeight);

    // ??? Obtener ubicaciones de los uniforms una sola vez ?????????????????????
    int locColor = glGetUniformLocation(shaderProg, "uColor");
    int locOffset = glGetUniformLocation(shaderProg, "uOffset");

    while (!glfwWindowShouldClose(mainWindow))
    {
        glfwPollEvents();
        processInput(mainWindow);

        // ??? CAMBIO 3: valores dinámicos basados en el tiempo ?????????????????
        float t = (float)glfwGetTime();

        // Color: cicla suavemente por RGB usando funciones trigonométricas
        float r = (sinf(t * 1.0f) + 1.0f) * 0.5f;
        float g = (sinf(t * 1.0f + 2.094f) + 1.0f) * 0.5f; // +120°
        float b = (sinf(t * 1.0f + 4.189f) + 1.0f) * 0.5f; // +240°

        // Posición: el triángulo orbita en un círculo pequeño
        float offsetX = sinf(t * 0.8f) * 0.35f;
        float offsetY = cosf(t * 0.8f) * 0.35f;

        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(shaderProg);

        // ??? Enviar uniforms al shader ????????????????????????????????????????
        glUniform4f(locColor, r, g, b, 1.0f);
        glUniform2f(locOffset, offsetX, offsetY);

        glBindVertexArray(VAO);
        glDrawArrays(GL_TRIANGLES, 0, 3);

        glfwSwapBuffers(mainWindow);
    }

    glfwMakeContextCurrent(mainWindow);
    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteProgram(shaderProg);
    glfwDestroyWindow(mainWindow);
    glfwTerminate();
    return 0;
}
~~~

### Evidencia 1 — Contexto y carga de OpenGL

GLFW debe inicializarse antes que GLAD porque GLFW crea la ventana y el contexto OpenGL, mientras que GLAD solo carga las funciones de OpenGL disponibles para ese contexto; sin un contexto activo, GLAD no puede obtener correctamente los punteros a las funciones. En la secuencia correcta, GLFW se encarga del contexto, la ventana y los eventos, y GLAD permite acceder a la API moderna de OpenGL una vez ese contexto existe.

### Evidencia 2 — Del arreglo al shader

<img width="1267" height="468" alt="Captura de pantalla 2026-04-28 174038" src="https://github.com/user-attachments/assets/8064b4ef-923d-47bd-b547-f7abfdc954da" />

La evidencia mostrada confirma que el arreglo de vértices alimenta correctamente al atributo del *vertex shader*, ya que en el depurador se observa que la variable vertices contiene los nueve valores flotantes correspondientes a las posiciones del triángulo, organizados en grupos de tres que representan cada vértice, y que tanto VAO como VBO tienen identificadores válidos distintos de cero, lo que indica que fueron generados y enlazados correctamente; en el punto de ejecución señalado, la llamada a glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0) establece que esos datos del VBO se interpreten como atributos de posición asociados al índice 0, el cual coincide con layout(location = 0) en el *vertex shader*, demostrando así de forma clara y técnica cómo los datos definidos en el arreglo de vértices del lado CPU son transferidos a la GPU y utilizados directamente como entrada del shader de vértices.

### Evidencia 3 — Uniform y cambio visual

<img width="1105" height="538" alt="Captura de pantalla 2026-04-28 175614" src="https://github.com/user-attachments/assets/2b87760b-255c-4d1b-8676-fae6c4c7a97f" />

La captura demuestra que el cambio visual del triángulo se produce mediante el uso de uniforms, sin necesidad de modificar el contenido del VBO. En el punto de ejecución mostrado, el programa se detiene justo antes de enviar los valores de los uniforms uColor y uOffset al shader, permitiendo observar que estos valores son dinámicos y varían en tiempo real. Los componentes de color (r, g, b) se encuentran dentro del rango válido y cambian continuamente, mientras que los offsets de posición (offsetX, offsetY) presentan valores distintos de cero, lo que provoca el desplazamiento del triángulo en pantalla. Al mismo tiempo, no se realiza ninguna operación sobre el VBO dentro del render loop, por lo que la geometría almacenada en la GPU permanece constante. Esto es posible porque los uniforms permiten modificar el comportamiento del shader en cada frame, afectando cómo se transforman y colorean los vértices durante la etapa de procesamiento en la GPU, sin alterar los datos originales de los vértices. De esta manera, la captura prueba que los uniforms son un mecanismo eficiente para lograr cambios visuales dinámicos sin necesidad de modificar la memoria de vértices.

### Evidencia 4 — Prueba de borde

Por medio del inspector, se pauso el valor de offsetY, en este caso para comprobar que es lo que sucede

<img width="1074" height="256" alt="image" src="https://github.com/user-attachments/assets/3336ba31-fc5f-40d5-bff1-3f517c387d87" />

Aqui se coloco el mismo valor en el X pero negativo, espero que se modifique la rotacion del triangulo y deje de girar con el centro en 0,0 y empiece a girar desde un nuevo eje.
Despues se comprobo que ningun cambio sucede a ojo.

<img width="1131" height="306" alt="image" src="https://github.com/user-attachments/assets/d3cde9c2-51dd-43d5-9c20-04ec560f6857" />

Se llevo ahora el limite maximio, esperando que posiblemente los numeros al estar dentro del rango esperado, fuera que no surtian efecto.
de nuevo, no cambio nada.
Puedo concluir que no se me es posible romper la rotacion sobre el eje que realiza el triangulo modificando sus parametros, pues al todos los calculos ser realizados con coordenadas polares, al ya esperar el siguiente estado pues es ciclico y sigue una funcion matematica, no se puede alterar.

### Evidencia 5 — Responsabilidad del pipeline
## Bitácora de reflexión
