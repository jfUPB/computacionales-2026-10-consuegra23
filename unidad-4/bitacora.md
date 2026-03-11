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

## Bitácora de reflexión
