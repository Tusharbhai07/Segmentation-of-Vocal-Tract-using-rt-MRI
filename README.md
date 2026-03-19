# 🧠 Segmentation of Vocal Tract using Real-Time MRI (rtMRI)

## 📌 Project Overview
This project focuses on the automatic segmentation of vocal tract structures from real-time Magnetic Resonance Imaging (rtMRI) data. The goal is to develop a robust and scalable framework for identifying key articulatory regions such as the tongue and airway, enabling improved analysis of speech production and supporting applications in speech technology and medical imaging.

---

## 🎯 Motivation
Understanding vocal tract dynamics is essential for advancing speech processing, articulatory modeling, and clinical diagnostics. However, manual segmentation of rtMRI data is time-consuming, subjective, and not scalable.

This project aims to address these challenges by leveraging modern segmentation techniques, including prompt-based foundation models, to enable efficient and automated analysis of vocal tract motion during continuous speech.

---

## 🧪 Objectives
- Develop an automated pipeline for vocal tract segmentation from rtMRI videos  
- Segment key articulators such as **tongue** and **airway regions**  
- Evaluate segmentation performance using standard metrics (Dice Coefficient, IoU)  
- Explore prompt-based segmentation using **Segment Anything Model (SAM)**  
- Compare results with traditional and deep learning-based approaches  

---

## 🏗️ Methodology
The proposed approach follows a hybrid segmentation framework:

1. **Input Data**  
   - Real-time MRI video sequences of the vocal tract  

2. **Preprocessing**  
   - Frame extraction and normalization  
   - Noise handling and resizing  

3. **Segmentation Model**  
   - First-frame segmentation using **SAM (Segment Anything Model)**  
   - Prompt-based segmentation using manually defined points  
   - Temporal propagation using **SAM3 Video Predictor**  

4. **Post-processing**  
   - Mask refinement and overlay visualization  
   - Generation of annotated output videos  

5. **Evaluation**  
   - Dice Coefficient  
   - Intersection over Union (IoU)  

---

## 📊 Current Progress
- ✅ Implemented SAM-based first-frame segmentation  
- ✅ Defined prompt points for tongue and airway  
- ✅ Integrated SAM3 for video mask propagation  
- ✅ Generated segmented overlay videos  
- 🔄 Working on evaluation metrics (Dice, IoU)  
- 🔄 Improving robustness across frames and speakers  

---

## 📂 Repository Structure
Segmentation-of-Vocal-Tract-using-rt-MRI/
│
├── proposal/          # Final proposal document and planning materials
├── src/               # Python scripts for segmentation
├── notebooks/         # Experimental notebooks
├── outputs/           # Segmentation results (images/videos)
├── docs/              # Methodology and documentation
├── requirements.txt   # Project dependencies
└── README.md          # Project documentation
---

## 🛠️ Tools & Technologies
- Python  
- OpenCV  
- NumPy  
- Matplotlib  
- Ultralytics SAM / SAM3  
- ImageIO  

---

## 📈 Evaluation Metrics
Segmentation performance will be assessed using:

- **Dice Coefficient** – measures overlap between predicted and ground truth segmentation  
- **Intersection over Union (IoU)** – evaluates accuracy of predicted regions  

These metrics are widely used in medical image segmentation literature.

---

## 📚 Key References
- Bresch, E., & Narayanan, S. (2009) – Frequency-domain segmentation of vocal tract MRI  
- Silva, S., & Teixeira, A. (2015) – Unsupervised AAM-based segmentation  
- Ronneberger et al. (2015) – U-Net for biomedical segmentation  
- Taha & Hanbury (2015) – Evaluation metrics for segmentation  

---

## 🚀 Future Work
- Implement deep learning baseline models (e.g., U-Net)  
- Improve segmentation generalization across speakers  
- Incorporate temporal consistency constraints  
- Extend segmentation to additional articulators (velum, lips)  
- Deploy real-time inference pipeline  

---

## 👨‍🎓 Author
**Tushar Manish Khupte**  
Master of Professional Engineering (Electrical)  
University of Sydney  

---

## ⭐ Acknowledgements
This project builds upon recent advances in speech processing, medical imaging, and foundation models for computer vision.

---
