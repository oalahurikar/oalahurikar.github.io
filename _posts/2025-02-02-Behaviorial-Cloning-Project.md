---
layout: mermaid-post
tags:
  - ai
  - project
---


This is project I did on **Behavioral Cloning for Autonomous Driving Using Convolutional Neural Network (CNN)**
if you are interested in code its here [Project github repo](https://github.com/oalahurikar/Self-Driving/tree/master/Term1/3.%20Behaviorial%20Cloning).

## 📝 **Project Overview**

Behavioral cloning is an approach in deep learning where a neural network learns to mimic human behavior—in this case, driving a car based on visual inputs. This project leverages LeNet **Convolutional Neural Networks (CNNs)** architecture to predict steering angles using images captured from a front-facing camera of a self-driving car.

📌 **Goal:** Train a **CNN** to predict steering angles from road images.  
📌 **Tech Stack:** `Keras`, `TensorFlow`, `Python`, `OpenCV`  
📌 **Data:** ~**4825 images** from a simulated environment.

## 📸  Training data
I used images from **three cameras** (center, left, right) to teach the car how to drive.  
To help it learn how to recover when off-track, I added a **steering correction of ±0.4** for left and right images.  
I recorded data while:
- Driving normally in the center of the lane (2 laps)
- Letting the car drift and recover before crashing
- Driving the track in reverse (1 lap)

In total, I collected about **4,825 images** to train the model.

|Center view|Left view|Right view|
|---|---|---|
|[![alt_text-1](https://github.com/oalahurikar/Behaviorial-Cloning/raw/master/Images%20and%20Graphs/Camera%20Center.jpg)](https://github.com/oalahurikar/Behaviorial-Cloning/blob/master/Images%20and%20Graphs/Camera%20Center.jpg)|[![alt_text-2](https://github.com/oalahurikar/Behaviorial-Cloning/raw/master/Images%20and%20Graphs/Camera%20Left.jpg)](https://github.com/oalahurikar/Behaviorial-Cloning/blob/master/Images%20and%20Graphs/Camera%20Left.jpg)|[![alt_text-3](https://github.com/oalahurikar/Behaviorial-Cloning/raw/master/Images%20and%20Graphs/Camera%20Right.jpg)](https://github.com/oalahurikar/Behaviorial-Cloning/blob/master/Images%20and%20Graphs/Camera%20Right.jpg)|

## 🏗 **Project Architecture**

Project's workflow:

<div class="mermaid">
graph TD
  A[Data Collection] --> B[Preprocessing]
  B --> C[Data Augmentation]
  C --> D[CNN Model]
  D --> E[Training with Adam Optimizer]
  E --> F[Evaluation & Validation]
  F --> G[Autonomous Driving Test]
  G -->|Success| H[Model Deployment]
  G -->|Failure| E[Re-train Model]
</div>


## 📊 **Key Components & Features**

| 🔹 **Component**               | 🔍 **Details**                                              |
| ------------------------------ | ----------------------------------------------------------- |
| 📸 **Data Collection**         | 3 Camera views (Left, Center, Right) to improve robustness. |
| ✂ **Preprocessing**            | Cropping sky & hood, normalizing pixel values.              |
| 🎭 **Data Augmentation**       | Flipping, brightness adjustment, adding shadows.            |
| 🏗 **Model Architecture**      | **CNN** → 6 Conv layers + 4 Fully Connected layers.         |
| 🛠 **Training Strategy**       | **Optimizer:** Adam **Loss Function:** MSE                  |
| 🔄 **Overfitting Reduction**   | Dropout layers, pooling, increased dataset.                 |
| 🏁 **Final Model Performance** | Successfully completed lap without drifting!                |

## 🏗 **LeNet CNN Model Architecture**

<div class="mermaid">
graph TD
  A[Input Image 160x320x3] --> B[Lambda Layer - Normalization]
  B --> C[Cropping Layer - Remove sky & hood]
  C --> D[Conv Layer 5x5 - 24 filters, RELU]
  D --> E[Conv Layer 5x5 - 36 filters, RELU]
  E --> F[Conv Layer 5x5 - 48 filters, RELU]
  F --> G[Conv Layer 3x3 - 64 filters, RELU]
  G --> H[Conv Layer 3x3 - 64 filters, RELU]
  H --> I[Flatten]
  I --> J[Fully Connected - 100 neurons, RELU]
  J --> K[Fully Connected - 50 neurons, RELU]
  K --> L[Fully Connected - 10 neurons, RELU]
  L --> M[Output - Steering Angle]
</div>

---
## 📈 **Model Training & Performance**

|**🔹 Training Set Size**|**⚠ MSE Loss**|**🚗 Performance**|
|---|---|---|
|**2048 images**|❌ High|Drifts off track|
|**4825 images**|✅ Lower|Completes lap 🚀|

---

## 🏁 **Final Results**

✅ Model successfully drives autonomously without manual intervention.  
✅ Improved performance using **data augmentation + dropout layers**.  
✅ **MSE Loss reduced significantly** with more training data.

---

## **🚀 Key Observations**

**1️⃣ Small Dataset (2048 samples)**
• 📉 **Training Loss** drops significantly but quickly → **Overfitting** risk! 🚨
• 📈 **Validation Loss** remains flat or slightly increases → **Poor generalization**

**2️⃣ Large Dataset (4825 samples)**
• 📉 **Training Loss** decreases **steadily**
• 📉 **Validation Loss** also decreases → **Better generalization** 🏆

• **Training & Validation Loss converge** more smoothly → **Less overfitting**

| MSE with small data | MSE with Large data (Final solution) |
|:---:|:---:|
| ![MSE with small data](https://github.com/oalahurikar/Behaviorial-Cloning/raw/master/MSE%20Error1.png) | ![MSE with large data](https://github.com/oalahurikar/Behaviorial-Cloning/raw/master/MSE%20Error2.png) |

### **📌 What these results mean** 
✅ **Overfitting Reduction** – Increasing data **reduced the training-validation gap**
✅ **Better Generalization** – Validation loss **decreased**, meaning it performs well on unseen data
✅ **Final Model Stability** – Training and validation loss **align**, meaning the model is **learning correctly**
✅ **Performance Boost** – Initial validation loss ~0.06, final validation loss ~0.045 → **~25% improvement!** 📉

---

## 🔮 **Next Steps**

🔹 Test on **real-world driving datasets** 🚗  
🔹 Optimize with **Reinforcement Learning** 🤖  
🔹 Deploy on **Embedded Hardware (Jetson Nano, Raspberry Pi)**