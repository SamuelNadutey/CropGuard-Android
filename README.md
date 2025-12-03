# CropGuard: Offline Edge-AI Agricultural Diagnostics 🇬🇭

![Status](https://img.shields.io/badge/Status-Field%20Prototype-success) 
![Platform](https://img.shields.io/badge/Platform-Android-blue) 
![AI Model](https://img.shields.io/badge/Model-EfficientNet--Lite0-orange) 
![Connectivity](https://img.shields.io/badge/Connectivity-100%25%20Offline-green)

📄 **[Read the Full Technical Report](./TECHNICAL_REPORT.md)** (Architecture, Protocols & Verification)

**CropGuard** is an offline-first Android application designed to diagnose cocoa and maize diseases in resource-constrained farming communities in Ghana. 

It serves as the **software application layer** for my research into **Systolic Array Acceleration**. It demonstrates the real-world latency bottleneck of mobile CPUs (~150ms) and validates the need for the custom hardware accelerator I designed (~12ms target).

👉 **[View the Hardware Accelerator Research (SystemVerilog)](https://github.com/SamuelNadutey/systolic-array-accelerator)**

---

## 📱 Download & Test
You can test the application on any Android device. **No internet connection is required.**

### **[Download Latest APK (v1.1)](../../releases/latest)**

*(Note: Enable "Install from Unknown Sources" in Android settings to install this research prototype.)*

---

## 🎥 System Demo

| Crop Selection & Zoom | Image Acquisition | Engineering Analysis |
| :---: | :---: | :---: |
                             



https://github.com/user-attachments/assets/c2dd3900-387c-4c28-bfa7-3bc1e57c87ae



   
| *Demonstrates UI interactivity and Glassmorphism* | *Shows split logic for Camera vs. Gallery upload* | *Visualizes CPU vs. FPGA latency gap* |

---

## 🚀 Key Engineering Features

###  AI Model Engineering
* **[View Training Notebook](./model_training/cropGuard_training.ipynb)**: Full Python pipeline using TensorFlow Lite Model Maker.
* **Architecture**: EfficientNet-B0 (Quantized to INT8).
* **Dataset**: Curated from open-source agricultural repositories(Kaggle) and validated against local field samples.

### 1. Zero-Latency Offline Inference
Instead of relying on cloud APIs (which fail in rural Ghana), CropGuard runs a quantized **EfficientNet-B0** model directly on the device using the **TensorFlow Lite Interpreter**.
* **Input Resolution:** 224x224
* **Model Size:** < 4MB (INT8 Quantized)
* **Target Accuracy:** 94% on validation set

### 2. Hardware Simulation Dashboard
The app includes a "Hardware Engine" view in the results screen that visualizes the performance gap between general-purpose computing and my proposed FPGA accelerator:
* **Current Reality (Mobile CPU):** ~150ms inference time (High power consumption).
* **Target Reality (FPGA/ASIC):** ~12ms inference time (Low power, high throughput).

### 3. Verified Treatment Protocols
All diagnostic advice is hardcoded based on verified technical sheets from:
* **COCOBOD (CODAPEC):** For Cocoa Black Pod, Mirids, and Borers.
* **MOFA (PPRSD):** For Maize Blight, Rust, and Fall Armyworm.

---

## 🛠️ Tech Stack
* **Language:** Java (Android Native)
* **ML Engine:** TensorFlow Lite (Interpreter API)
* **Architecture:** Hardware-Software Co-Design Simulation
* **UI:** Material Design with Glassmorphism & Custom Vector Assets

---

## 👨‍💻 Author
**Partey Samuel Nadutey**
*Researching Hardware Acceleration for Edge AI*
* [Email Me](mailto:samuelnadutey7@gmail.com)
