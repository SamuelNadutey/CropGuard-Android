# Project Technical Report: CropGuard & Systolic Edge-AI Accelerator
**Author:** Partey Samuel Nadutey
**Target Platform:** Android (Software) & Zynq-7000 FPGA / 28nm ASIC (Hardware)

---

## 1. Problem Statement & Motivation
Current mobile AI solutions for agriculture rely on cloud connectivity or general-purpose CPUs. In rural Ghana, this presents two critical bottlenecks:
1.  **Connectivity:** Farmers lack consistent internet access for cloud inference.
2.  **Power & Latency:** Running Convolutional Neural Networks (CNNs) on mobile CPUs (Von Neumann architecture) consumes excessive battery due to high memory bandwidth requirements (`150ms+` latency per inference).

**Solution:** A Hardware-Software Co-Design approach.
* **Software:** An offline-first Android application for immediate deployment.
* **Hardware:** A custom Weight-Stationary Systolic Array Accelerator designed to offload matrix multiplication, targeting **12ms latency** and **10x energy efficiency**.

---

## 2. Phase I: The AI Model Engineering
The system is built around a quantized computer vision model optimized for edge deployment.

* **Architecture:** **EfficientNet-Lite0**
    * *Reasoning:* Selected over MobileNetV2 for its superior accuracy-to-parameter ratio and mobile-friendly architecture (swish replaced with ReLU6, SE blocks removed).
* **Input Specification:** 224x224 RGB Images.
* **Training Pipeline:**
    * **Dataset:** Custom labeled dataset of Cocoa (Black Pod, Healthy, Pod Borer) and Maize (Blight, Rust, Armyworm).
    * **Transfer Learning:** Utilized TensorFlow Lite Model Maker to retrain the EfficientNet backbone.
* **Optimization (Quantization):**
    * Converted the 32-bit Floating Point (FP32) weights to **8-bit Integers (INT8)** using Post-Training Quantization.
    * *Impact:* Reduced model size to <4MB, enabling it to fit entirely within on-chip SRAM (minimizing DRAM access).

---

## 3. Phase II: The Software Application (Android)
The `CropGuard` Android app serves as the user interface and the "Software Baseline" for performance benchmarking.

### A. Architecture (MVC Pattern)
* **View (XML Layouts):** Implemented a modern Glassmorphism UI using `CardView` masks and alpha-blended gradients to ensure high usability in outdoor lighting.
* **Controller (MainActivity.java):** Manages the state machine between *Home*, *Acquisition* (Camera/Gallery), and *Analysis* modes.

### B. The Inference Engine
Instead of sending data to a server, the app hosts the "Brain" locally:
1.  **Initialization:** `FileUtil.loadMappedFile()` loads the `cropguard_model.tflite` into memory.
2.  **Preprocessing:** A `ImageProcessor` pipeline resizes the raw bitmap to 224x224 and normalizes pixel values.
3.  **Execution:** `tflite.run()` executes the INT8 graph on the device's ARM CPU.

### C. Domain Logic (Agricultural Protocols)
To ensure agronomic validity, the app integrates hardcoded treatment logic verified by local authorities:
* **Cocoa:** Protocols sourced from **COCOBOD (CODAPEC)** (e.g., Ridomil Gold dosage).
* **Maize:** Protocols sourced from **MOFA PPRSD** (e.g., Eradicoat for Fall Armyworm).

---

## 4. Phase III: The Hardware Accelerator (Research Core)
This is the novel engineering contribution. To solve the CPU inefficiency, a domain-specific accelerator was designed in **SystemVerilog**.

### A. Architecture: Weight-Stationary Systolic Array
Unlike a CPU that fetches data for every operation, this architecture fetches weights *once* and reuses them.

* **Topology:** 2D Mesh Grid ($N \times N$) of Processing Elements (PEs).
* **Dataflow:**
    * **Weights:** Loaded vertically and latched (stationary).
    * **Inputs (Pixels):** Streamed horizontally (systolic flow).
    * **Partial Sums:** Accumulated vertically.

### B. Module Breakdown
1.  **`mac_pe.sv` (The Processing Element):**
    * Implements a **Multiply-Accumulate (MAC)** unit tailored for INT8 math.
    * Uses registered outputs to pipeline the logic, enabling a target frequency of **850 MHz** on 28nm silicon.
    * *Key Feature:* Dual-mode control logic (Load vs. Compute) to handle weight stationary behavior.

2.  **`skew_buffer.sv` (Data Shaping):**
    * A hardware FIFO array that delays input rows by $0, 1, 2... N$ cycles.
    * *Why:* It aligns the "flat" memory data into a "diagonal wavefront" required for systolic computation, removing the burden of data reshaping from the CPU.

3.  **`accelerator_top.sv` (System Integration):**
    * Wraps the Grid and Skew Buffers into a single IP Core.
    * Exposes a standard memory interface for easy integration with AXI buses.

### C. Verification
* **Methodology:** Self-checking Testbench (`tb_accelerator.sv`) comparing hardware output against a software "Golden Model."
* **Status:** Functional verification complete; dataflow validated in Vivado XSim.

---

## 5. System Evaluation & Results

### Benchmarking Strategy
The project performs a hybrid performance analysis:
1.  **Measured Baseline:** The Android app measures actual CPU inference time (~150ms).
2.  **Simulated Acceleration:** Based on the RTL throughput (1 MAC/cycle/PE), the hardware accelerator projects a speedup to **~12ms**.

### Conclusion
This project successfully demonstrates a "Full-Stack" solution to the Edge AI problem in African agriculture.
1.  **Immediate Impact:** The Android app delivers verified, offline diagnosis to farmers today.
2.  **Future Scalability:** The SystemVerilog design provides the blueprint for a low-cost, energy-efficient chip that will make this technology viable for millions of farmers tomorrow.
