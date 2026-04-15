# Unidad 6

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 
OfApp.Cpp
~~~ C++
#include "ofApp.h"
#include <algorithm>

// ─────────────────────────────────────────────
//  Subject
// ─────────────────────────────────────────────

void Subject::addObserver(Observer * observer) {
	if (!observer) return;
	if (std::find(observers.begin(), observers.end(), observer) == observers.end()) {
		observers.push_back(observer);
	}
}

void Subject::removeObserver(Observer * observer) {
	if (!observer) return;
	observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
}

void Subject::notify(const std::string & event) {
	for (Observer * observer : observers) {
		observer->onNotify(event);
	}
}

// ─────────────────────────────────────────────
//  Particle
// ─────────────────────────────────────────────

Particle::Particle()
	: state(nullptr) {
	position = ofVec2f(ofRandomWidth(), ofRandomHeight());
	velocity = ofVec2f(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
	size = ofRandom(2.0f, 5.0f);
	color = ofColor(255);

	state = new NormalState();
	state->onEnter(this);
}

Particle::~Particle() {
	if (state) {
		state->onExit(this);
		delete state;
		state = nullptr;
	}
}

void Particle::setState(State * newState) {
	if (state) {
		state->onExit(this);
		delete state;
	}
	state = newState;
	if (state) {
		state->onEnter(this);
	}
}

void Particle::update() {
	if (state) {
		state->update(this);
	}
	keepInsideWindow();
}

void Particle::draw() {
	ofPushStyle();
	ofSetColor(color);
	ofDrawCircle(position, size);
	ofPopStyle();
}

void Particle::onNotify(const std::string & event) {
	if (event == "attract") {
		setState(new AttractState());
	} else if (event == "repel") {
		setState(new RepelState());
	} else if (event == "stop") {
		setState(new StopState());
	} else if (event == "normal") {
		setState(new NormalState());
	} else if (event == "orbit") {
		setState(new OrbitState());
	}
}

void Particle::keepInsideWindow() {
	const float W = static_cast<float>(ofGetWidth());
	const float H = static_cast<float>(ofGetHeight());

	if (position.x < 0.0f) {
		position.x = 0.0f;
		velocity.x *= -1.0f;
	} else if (position.x > W) {
		position.x = W;
		velocity.x *= -1.0f;
	}
	if (position.y < 0.0f) {
		position.y = 0.0f;
		velocity.y *= -1.0f;
	} else if (position.y > H) {
		position.y = H;
		velocity.y *= -1.0f;
	}
}

// ─────────────────────────────────────────────
//  States — existentes
// ─────────────────────────────────────────────

void NormalState::onEnter(Particle * particle) {
	particle->velocity.set(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
}

void NormalState::update(Particle * particle) {
	particle->position += particle->velocity;
}

static void steer(Particle * particle, const ofVec2f & toward,
	float accel, float vmax, float posScale) {
	ofVec2f dir = toward - particle->position;
	float len = dir.length();
	if (len > 1e-6f) {
		dir /= len;
		particle->velocity += dir * accel;
	}
	particle->velocity.limit(vmax);
	particle->position += particle->velocity * posScale;
}

void AttractState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	steer(particle, mouse, 0.05f, 3.0f, 0.2f);
}

void RepelState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	ofVec2f away = particle->position - mouse;
	float len = away.length();
	if (len > 1e-6f) {
		away /= len;
		particle->velocity += away * 0.05f;
	}
	particle->velocity.limit(3.0f);
	particle->position += particle->velocity * 0.2f;
}

void StopState::update(Particle * particle) {
	particle->velocity *= 0.80f;
	if (particle->velocity.lengthSquared() < 1e-4f) {
		particle->velocity.set(0.0f, 0.0f);
	}
	particle->position += particle->velocity;
}

// ─────────────────────────────────────────────
//  OrbitState — agujero negro
// ─────────────────────────────────────────────

void OrbitState::onEnter(Particle * particle) {
	originalColor = particle->color;
	originalSize = particle->size;

	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	ofVec2f delta = particle->position - mouse;
	angle = std::atan2(delta.y, delta.x);
	orbitRadius = std::max(delta.length(), 30.0f);
	angularSpeed = ofMap(orbitRadius, 30.0f, 400.0f, 0.09f, 0.02f, true);
}

void OrbitState::onExit(Particle * particle) {
	particle->color = originalColor;
	particle->size = originalSize;
}

void OrbitState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());

	// Espiral de caída hacia el cursor
	orbitRadius *= 0.997f;
	orbitRadius = std::max(orbitRadius, 8.0f);

	// Velocidad angular reactiva: más rápido cuanto más cerca
	angularSpeed = ofMap(orbitRadius, 8.0f, 400.0f, 0.14f, 0.018f, true);
	angle += angularSpeed;

	// Posición objetivo sobre la espiral
	ofVec2f target(mouse.x + std::cos(angle) * orbitRadius,
		mouse.y + std::sin(angle) * orbitRadius);

	particle->position = particle->position.interpolate(target, 0.18f);
	particle->velocity = target - particle->position;

	// ── Color dinámico por proximidad ──────────────────────────────────
	float dist = particle->position.distance(mouse);
	float t = ofMap(dist, 8.0f, 300.0f, 1.0f, 0.0f, true);

	// lejos → gris azulado | medio → naranja | cerca → blanco caliente
	ofColor cold(60, 60, 120);
	ofColor warm(255, 140, 0);
	ofColor hot(255, 240, 180);

	ofColor finalColor;
	if (t < 0.6f) {
		finalColor = cold.getLerped(warm, t / 0.6f);
	} else {
		finalColor = warm.getLerped(hot, (t - 0.6f) / 0.4f);
	}

	// Tamaño crece al acercarse (compresión por mareas)
	particle->size = ofMap(t, 0.0f, 1.0f, originalSize, originalSize * 2.2f, true);
	particle->color = finalColor;
}

// ─────────────────────────────────────────────
//  ParticleFactory
// ─────────────────────────────────────────────

Particle * ParticleFactory::createParticle(const std::string & type) {
	Particle * particle = new Particle();

	if (type == "star") {
		particle->size = ofRandom(2.0f, 4.0f);
		particle->color = ofColor(255, 0, 0);
	} else if (type == "shooting_star") {
		particle->size = ofRandom(3.0f, 6.0f);
		particle->color = ofColor(0, 255, 0);
		particle->velocity *= 3.0f;
	} else if (type == "planet") {
		particle->size = ofRandom(5.0f, 8.0f);
		particle->color = ofColor(0, 0, 255);
	} else if (type == "nebula") {
		particle->size = ofRandom(8.0f, 14.0f);
		particle->color = ofColor(0, 210, 255, 180);
		particle->velocity *= 0.3f;
	}

	return particle;
}

// ─────────────────────────────────────────────
//  ofApp
// ─────────────────────────────────────────────

ofApp::~ofApp() {
	for (Particle * p : particles) {
		removeObserver(p);
		delete p;
	}
	particles.clear();
}

void ofApp::setup() {
	ofBackground(0);
	particles.reserve(100 + 5 + 10 + 15);

	for (int i = 0; i < 100; ++i) {
		Particle * p = ParticleFactory::createParticle("star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 5; ++i) {
		Particle * p = ParticleFactory::createParticle("shooting_star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 10; ++i) {
		Particle * p = ParticleFactory::createParticle("planet");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 15; ++i) {
		Particle * p = ParticleFactory::createParticle("nebula");
		particles.push_back(p);
		addObserver(p);
	}
}

void ofApp::update() {
	for (Particle * p : particles) {
		p->update();
	}
}

void ofApp::draw() {
	for (Particle * p : particles) {
		p->draw();
	}
}

void ofApp::keyPressed(int key) {
	switch (key) {
	case 's':
		notify("stop");
		break;
	case 'a':
		notify("attract");
		break;
	case 'r':
		notify("repel");
		break;
	case 'n':
		notify("normal");
		break;
	case 'o':
		notify("orbit");
		break;
	default:
		break;
	}
}
~~~
OfApp.h
~~~ C++
#pragma once
#include "ofMain.h"
#include <string>
#include <vector>

class Observer {
public:
	virtual ~Observer() = default;
	virtual void onNotify(const std::string & event) = 0;
};

class Subject {
public:
	void addObserver(Observer * observer);
	void removeObserver(Observer * observer);

protected:
	void notify(const std::string & event);

private:
	std::vector<Observer *> observers;
};

class Particle;

class State {
public:
	virtual ~State() = default;
	virtual void update(Particle * particle) = 0;
	virtual void onEnter(Particle * particle) { }
	virtual void onExit(Particle * particle) { }
};

class Particle : public Observer {
public:
	Particle();
	~Particle() override;
	Particle(const Particle &) = delete;
	Particle & operator=(const Particle &) = delete;

	void update();
	void draw();
	void onNotify(const std::string & event) override;
	void setState(State * newState);

	ofVec2f position;
	ofVec2f velocity;
	float size;
	ofColor color;

private:
	void keepInsideWindow();
	State * state;
};

class NormalState : public State {
public:
	void update(Particle * particle) override;
	void onEnter(Particle * particle) override;
};

class AttractState : public State {
public:
	void update(Particle * particle) override;
};

class RepelState : public State {
public:
	void update(Particle * particle) override;
};

class StopState : public State {
public:
	void update(Particle * particle) override;
};

class OrbitState : public State {
public:
	void onEnter(Particle * particle) override;
	void onExit(Particle * particle) override;
	void update(Particle * particle) override;

private:
	float angle;
	float orbitRadius;
	float angularSpeed;
	ofColor originalColor;
	float originalSize; // ← campo agregado
};

class ParticleFactory {
public:
	static Particle * createParticle(const std::string & type);
};

class ofApp : public ofBaseApp, public Subject {
public:
	~ofApp() override;
	void setup() override;
	void update() override;
	void draw() override;
	void keyPressed(int key) override;

private:
	std::vector<Particle *> particles;
};
~~~
Se agregaron las dos cosas que pedia la actividad, primero fue la particula, Nebula, la cual es azul claro y significativamente mas grande que todas las demas.
Posteriormente se agrego Orbit, como state, el cual hace que las particulas orbiten a su alrededor y entre mas cerca de el estan "brillen" de un color naranja muy intenso.

## Bitácora de reflexión
