````md
# Solar Stewart Tracker

<p align="center">
  <img src="docs/images/system_overview.png" alt="System Overview" width="750">
</p>

Real-time embedded C++ software for solar tracking using a **3-RRS Stewart-type parallel mechanism**.

This project implements an event-driven pipeline that detects the sun using a camera, computes control commands, converts them into platform motion through inverse kinematics, and actuates servos via a hardware abstraction layer.

------------------------------------------------------------------------

## Table of Contents

- Project Overview
- System Architecture
- UML Sequence Diagram
- Circuit Diagram
- Repository Structure
- Build Instructions
- Running the System
- Testing
- Latency
- Authors and Contributions
- Acknowledgements

------------------------------------------------------------------------

## Project Overview

The system follows a real-time, event-driven architecture:

**Camera → Vision → Controller → Kinematics → Actuation**

Core design principles:

- blocking I/O wakes threads
- callback-based communication between modules
- bounded thread-safe queues between stages
- separation of hardware-dependent and testable components
- reproducible CMake-based build system

The system supports both:

- hardware execution on Linux / Raspberry Pi
- software-only simulation for development and testing

------------------------------------------------------------------------

## System Architecture

Main components:

- **ICamera**
  - produces frames via callback
  - implementations: `LibcameraPublisher`, `SimulatedPublisher`

- **SystemManager**
  - orchestrates threads and pipeline execution
  - enforces system state machine

- **SunTracker**
  - detects bright target and computes centroid

- **Controller**
  - converts image error into tilt/pan commands
  - applies deadband and clamping

- **Kinematics3RRS**
  - maps tilt/pan into three servo angles

- **ActuatorManager**
  - applies saturation and rate limiting

- **ServoDriver**
  - converts angles to PWM signals
  - supports hardware and log-only modes

- **LatencyMonitor**
  - measures timing across pipeline stages

------------------------------------------------------------------------

## UML Sequence Diagram

<p align="center">
  <img src="docs/images/uml_sequence.png" alt="UML Sequence Diagram" width="900">
</p>

The system operates as follows:

1. Camera produces a frame via callback  
2. Frame is pushed into a bounded queue  
3. Control thread wakes and processes:
   - SunTracker → Controller → Kinematics  
4. Commands are sent to actuator queue  
5. Actuator thread wakes and applies:
   - ActuatorManager → ServoDriver  
6. Latency timestamps are recorded across all stages  

------------------------------------------------------------------------

## Circuit Diagram

<p align="center">
  <img src="docs/images/circuit_diagram.png" alt="Circuit Diagram" width="900">
</p>

The hardware setup connects:

- camera (via libcamera-compatible interface)
- PCA9685 PWM driver over I2C
- three servo motors
- Raspberry Pi acting as central controller

The PCA9685 generates stable PWM signals for the servos, while I2C provides communication between the controller and actuator driver.

------------------------------------------------------------------------

## Repository Structure

```text
include/
src/
tests/
docs/
external/
CMakeLists.txt
Doxyfile
````

---

## Build Instructions

### Linux

```bash
cmake -S . -B build
cmake --build build -j$(nproc)
```

### Windows

```powershell
cmake -S . -B build
cmake --build build --config Release
```

---

## Running the System

### Software-only mode

```bash
./build/solar_tracker_qt
```

Uses:

* simulated camera
* log-only actuator mode

### Hardware mode

Requires:

* libcamera support
* I2C enabled
* PCA9685 connected

System will enter **FAULT** if required hardware is unavailable.

---

## Testing

Run all tests:

```bash
ctest --test-dir build --output-on-failure
```

Or run executables:

```bash
./build/tests/test_core
./build/tests/test_pca9685
./build/tests/test_servodriver
```

Manual hardware test:

```bash
./build/tests/servo_manual_smoketest
```

---

## Latency

Latency is measured across:

* frame acquisition
* vision processing
* control computation
* actuation

Metrics reported:

* average latency
* minimum latency
* maximum latency
* jitter

These are **software-side measurements**, not hard real-time guarantees.

---

## Authors and Contributions

**Fadi Halteh**

* Event-driven pipeline
* SystemManager and state machine
* ThreadSafeQueue
* ActuatorManager
* Core test infrastructure

**Ziming Yan**

* SunTracker (vision module)
* UI components and overlay rendering
* Qt interface

**Tareq A. M. Almzanin**

* Controller implementation
* Trajectory testing
* Manual override mode

**Jichao Wang**

* 3RRS inverse kinematics
* Application configuration system
* System factory and application setup

**Zhenyu Zhu**

* PCA9685 driver
* ServoDriver
* I2C abstraction layer
* Latency monitoring module

(Source: project members document )

---

## Acknowledgements

We would like to thank:

* **Dr. Bernd Porr** for guidance in real-time embedded systems and architecture design
* **Dr. Chongfeng Wei** for software engineering support and project supervision

---

## Last Updated

19 March 2026

```
```
