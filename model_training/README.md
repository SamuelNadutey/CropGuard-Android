## Model Training Pipeline

This directory contains the Python notebook used to train the quantization-aware EfficientNet-B0 model.

**Dataset Sources:**
* Training data aggregated from public repositories:
  * [Cocoa Diseases (ZaldyJr)](https://www.kaggle.com/datasets/zaldyjr/cacao-diseases)
  * [Corn/Maize Leaf Disease (Smaranjitghose)](https://www.kaggle.com/datasets/smaranjitghose/corn-or-maize-leaf-disease-dataset)

**Methodology:**
* Transfer Learning on EfficientNet-B0
* Post-training quantization (INT8) for mobile deployment
