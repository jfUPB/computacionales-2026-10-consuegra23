# Unidad 4

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
~~~ c++
#include "ofMain.h"
#include "ofApp.h"

int main() {
    ofSetupOpenGL(1024, 768, OF_WINDOW);
    ofRunApp(new ofApp());
    return 0;
}
~~~
~~~ c++
#include "ofApp.h"
#include <string.h>

// Minimal getopt implementation (C linkage) to satisfy libraries that expect
// getopt/optind/optarg on Windows. This is a small, pragmatic subset of
// functionality (short options, required-argument via ':' in optstring).
extern "C" {
int optind = 1;
char *optarg = NULL;
int opterr = 1;
int optopt = '?';

int getopt(int argc, char * const argv[], const char *optstring) {
    static int optpos = 1; /* position in current argv element */
    optarg = NULL;

    if (optind >= argc) return -1;

    char *arg = argv[optind];
    /* not an option or single '-' */
    if (arg[0] != '-' || arg[1] == '\0') return -1;

    /* -- means end of options */
    if (strcmp(arg, "--") == 0) {
        optind++;
        return -1;
    }

    char opt = arg[optpos];
    const char *p = strchr(optstring, opt);
    if (!p) {
        /* unknown option */
        optopt = opt;
        if (arg[++optpos] == '\0') {
            optind++;
            optpos = 1;
        }
        return '?';
    }

    if (p[1] == ':') {
        /* option requires an argument */
        if (arg[optpos+1] != '\0') {
            /* rest of same argv element */
            optarg = &arg[optpos+1];
            optind++;
        } else if (optind + 1 < argc) {
            /* next argv element */
            optarg = argv[++optind];
            optind++;
        } else {
            /* missing required argument */
            optopt = opt;
            optind++;
            optpos = 1;
            return ':'; /* convention: return ':' when argument missing */
        }
        optpos = 1;
        return opt;
    } else {
        /* no argument for this option */
        if (arg[++optpos] == '\0') {
            optind++;
            optpos = 1;
        }
        return opt;
    }
}
} // extern "C"

// Minimal linked-list queue types used by this app
struct Node {
	float x, y, radius;
	ofColor color;
	float opacity;
	Node * next;
	Node(float _x, float _y, float _r, ofColor _c, float _o)
		: x(_x), y(_y), radius(_r), color(_c), opacity(_o), next(nullptr) { }
};

class BrushQueue {
public:
	Node * front;
	Node * rear;
	int size;
	int maxSize;

	BrushQueue(int _maxSize = 50);
	~BrushQueue();

	void enqueue(float x, float y, float radius, ofColor color, float opacity);
	void dequeue();
	void clear();
	bool isEmpty();
};

// global state used by the app
static BrushQueue strokes(50);
static float backgroundHue = 0;

BrushQueue::BrushQueue(int _maxSize)
	: front(nullptr)
	, rear(nullptr)
	, size(0)
	, maxSize(_maxSize) { }

BrushQueue::~BrushQueue() {
	clear();
}

void BrushQueue::enqueue(float x, float y, float radius, ofColor color, float opacity) {
	Node * newNode = new Node(x, y, radius, color, opacity);
	if (rear == nullptr) {
		front = rear = newNode;
	} else {
		rear->next = newNode;
		rear = newNode;
	}
	size++;
	if (size > maxSize) {
		dequeue();
	}
}

void BrushQueue::dequeue() {
	if (front == nullptr) return;
	Node * temp = front;
	front = front->next;
	if (front == nullptr) rear = nullptr;
	delete temp;
	size--;
}

void BrushQueue::clear() {
	while (!isEmpty()) {
		dequeue();
	}
}

bool BrushQueue::isEmpty() {
	return front == nullptr;
}

//--------------------------------------------------------------
void ofApp::setup() {
	ofBackground(0);
}

//--------------------------------------------------------------
void ofApp::update() {
	backgroundHue += 0.2;
	if (backgroundHue > 255) backgroundHue = 0;
}

//--------------------------------------------------------------
void ofApp::draw() {
	ofColor color1, color2;
	color1.setHsb(backgroundHue, 150, 240);
	color2.setHsb(fmod(backgroundHue + 128, 255), 150, 240);
	ofBackgroundGradient(color1, color2, OF_GRADIENT_LINEAR);

	int index = 0;
	Node * current = strokes.front;
	while (current != nullptr) {
		float ageFade = ofMap(index, 0, strokes.size - 1, 60, 255);
		ofSetColor(current->color, ageFade);
		ofDrawCircle(current->x, current->y, current->radius);
		current = current->next;
		index++;
	}
}

//--------------------------------------------------------------
void ofApp::mouseMoved(int x, int y) {
	float radius = ofRandom(10, 20);
	ofColor color;
	color.setHsb(ofRandom(255), 255, 255);
	float opacity = 255;
	strokes.enqueue(x, y, radius, color, opacity);
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
	if (key == 'c') {
		strokes.clear();
	}
	if (key == 'a') {
		strokes.maxSize = (strokes.maxSize == 50) ? 100 : 50;
	} else if (key == 's') {
		ofSaveFrame();
	}
}

// Stubs for other event handlers declared in ofApp.h but not used in this
// example. Providing empty definitions resolves linker errors.
void ofApp::keyReleased(int key) { }

void ofApp::mouseDragged(int x, int y, int button) { }

void ofApp::mousePressed(int x, int y, int button) { }

void ofApp::mouseReleased(int x, int y, int button) { }

void ofApp::mouseEntered(int x, int y) { }

void ofApp::mouseExited(int x, int y) { }

void ofApp::windowResized(int w, int h) { }

void ofApp::dragEvent(ofDragInfo dragInfo) { }

void ofApp::gotMessage(ofMessage msg) { }
~~~
#### Evidencia_1

Explicación
La imagen muestra que la cola está vacía antes de insertar el primer elemento: front == NULL, rear == NULL y size == 0. También se observa que newNode ya fue creado y tiene valores concretos: x y y corresponden a la posición del mouse al momento del evento, radius está dentro del rango aleatorio definido (10 a 20), color aparece con sus componentes (en VS normalmente como RGBA) y opacity con 255. El cursor de ejecución está exactamente sobre front = rear = newNode;, es decir, a punto de inicializar los extremos de la cola.
Justificación
El estado previo con rear == NULL activa la rama de inicialización del primer nodo; al ejecutar front = rear = newNode se establecen simultáneamente ambos extremos de la cola apuntando al mismo nodo recién creado. Esto demuestra que, partiendo de una cola vacía, la primera inserción deja la estructura en un estado válido con un único elemento accesible por front y rear, y con el siguiente paso (size++) se actualizará el tamaño a 1. Esta secuencia cumple exactamente con la evidencia solicitada de “inserción del primer nodo en una cola vacía (enqueue)”.
#### Evidencia 2
Explicación
En la imagen se observa que la cola no está vacía: front apunta a una dirección válida y rear también apunta a una dirección válida, mostrando que ya existe al menos un nodo en la estructura. Justo antes de ejecutar la instrucción, rear->next aparece como <NULL>, lo que indica que el nodo actualmente señalado por rear es el último de la cola. newNode tiene una dirección válida y ya contiene datos inicializados para el nuevo trazo: coordenadas x = 328 y y = 408, un radius alrededor de 15.49, color con componentes cargados (mostrados en RGBA por el depurador) y opacity = 255. Se aprecia además que el cursor de ejecución está sobre rear->next = newNode; y que, a continuación, se ejecutará rear = newNode;, lo que actualizará el extremo trasero de la cola.
Justificación
El hecho de que rear->next sea <NULL> antes de la instrucción y que front permanezca apuntando al nodo más antiguo confirma que el nuevo elemento se va a insertar por el final, sin alterar el frente. La asignación rear->next = newNode; encadena el nuevo nodo detrás del último, y la línea posterior rear = newNode; avanza el puntero trasero para que ahora apunte al recién insertado. Esta secuencia preserva estrictamente el orden de llegada: los elementos nuevos se agregan al final y el primero insertado sigue accesible desde front. Por tanto, la inserción cumple el comportamiento FIFO durante las operaciones de enqueue, que es exactamente lo solicitado en la evidencia.
#### Evidencia 3.
Explicación
La imagen muestra la ejecución detenida al inicio operativo de BrushQueue::dequeue, en la línea Node* temp = front;. En Inspección se observa que front apunta a un nodo válido con x = 695, y = 312, radius ≈ 11.78, color definido y opacity = 255. front->next apunta al siguiente nodo de la cadena con x = 611 y y = 327, lo que confirma que hay al menos dos elementos en la cola. rear también apunta a un nodo válido (el último actual) y size es 51, indicando que la estructura contiene múltiples elementos. La variable temp toma exactamente la misma dirección que front, y su contenido coincide con el del primer nodo, dejando listo el avance del frente y la posterior liberación de memoria del nodo más antiguo.
Justificación
Al iniciar dequeue(), la asignación temp = front captura el nodo más antiguo, luego front = front->next hará que el frente avance al segundo nodo, y finalmente delete temp liberará la memoria del nodo retirado, evitando fugas. El hecho de que front->next apunte a un segundo nodo y que rear permanezca intacto demuestra que la eliminación ocurre por el frente (FIFO), manteniendo el resto de la cadena inalterada. Además, con size decrementándose al final y la corrección if (front == nullptr) rear = nullptr para el caso de quedar vacía, se garantiza consistencia estructural y ausencia de pérdidas de memoria, cumpliendo completamente la evidencia de eliminación FIFO y liberación segura.
#### Evidencia 4.
Explicación
La imagen muestra la ejecución detenida en la línea de la llamada dequeue(); dentro de if (size > maxSize) en BrushQueue::enqueue. En la ventana Inspección se observa que size vale 101 justo después del size++, mientras que maxSize es 100, por lo que la condición del límite se cumple. También se ve front apuntando a un nodo válido con campos inicializados (por ejemplo x = 651, y = 166, un radius ~11.27, color y opacity = 255), lo que identifica el elemento más antiguo de la cola que será expulsado. El estado capturado evidencia que el inserto reciente hizo rebasar la capacidad y que el flujo va a invocar dequeue() inmediatamente para corregir el exceso.
Justificación
Cuando size supera maxSize, la llamada a dequeue() elimina el nodo señalado por front (el más antiguo), liberando su memoria y avanzando el frente; de este modo, el tamaño efectivo de la estructura vuelve al límite configurado y se preserva la política FIFO, ya que la expulsión siempre ocurre por el frente mientras las inserciones se hacen por la parte trasera. El hecho de observar size == maxSize + 1 en la pausa, seguido de la invocación directa a dequeue(), demuestra el mecanismo de control de capacidad: no permite que la cola permanezca por encima del máximo y garantiza que, ante sobrecupo, el elemento más antiguo es el que se retira. ¿Quieres que hagamos una segunda captura ya dentro de dequeue() en Node* temp = front; y una tercera al volver a enqueue() verificando que size vuelve a 100 y que front cambió al siguiente nodo para cerrar el ciclo de evidencia. 
#### Evidencia 5.
Explicación
La imagen muestra la ejecución detenida dentro de ofApp::draw() en la línea del bucle while (current != nullptr). En la ventana Inspección se observa que strokes.front y strokes.rear apuntan a direcciones válidas distintas, strokes.size vale 100, y current coincide exactamente con strokes.front en la primera iteración. También se ve current->next apuntando al siguiente nodo de la lista, e index con valor 0, lo que confirma que el recorrido empieza por el primer elemento y que existe al menos un segundo elemento disponible para la siguiente iteración. El código dentro del bucle realiza solo lectura de los campos del nodo (color, x, y, radius) para calcular ageFade y dibujar el círculo, sin modificar punteros de la estructura.
Justificación
El estado capturado demuestra que el bucle de draw recorre la cola sin destruirla ni alterarla: current es una variable local que avanza con current = current->next, mientras que los invariantes de la cola permanecen intactos durante la iteración, es decir, strokes.front no cambia, strokes.rear no cambia y strokes.size permanece estable. La ausencia de llamadas a dequeue o delete dentro de draw, junto con la observación de que current inicia igual a strokes.front y progresa hacia current->next con index incrementando desde 0, justifican que el dibujado es un recorrido no destructivo de la estructura. Esto cumple la evidencia solicitada de que la cola se itera para pintar sin modificar ni eliminar sus elementos.
#### Evidencia 6.
Explicación
En la primera captura, tomada antes de invocar la limpieza, la ventana de Inspección muestra que el valor de strokes.size es 50, lo que indica que la cola está poblada con múltiples nodos previamente insertados mediante enqueue durante el movimiento del mouse. En la segunda captura, inmediatamente después de ejecutar clear (disparado desde keyPressed con la tecla c), el mismo panel de Inspección refleja que strokes.size ha pasado a 0. La comparación directa entre ambas imágenes evidencia el cambio de estado de la estructura: de contener elementos a quedar completamente vacía tras la operación de limpieza.
Justificación
El método clear recorre la cola llamando repetidamente a dequeue mientras no esté vacía. Cada dequeue elimina el nodo del frente y decrementa el contador interno size en una unidad; cuando no quedan elementos, front y rear quedan en nullptr y size alcanza exactamente 0. El hecho observable de que size sea mayor que 0 antes de la limpieza y que valga 0 inmediatamente después constituye una verificación suficiente y objetiva de que clear eliminó todos los elementos presentes, restableciendo la estructura a su estado vacío. Esto cumple la evidencia de limpieza total al demostrar el efecto esperado sobre la métrica canónica de capacidad de la cola (size) antes y después de la operación.
## Bitácora de reflexión
