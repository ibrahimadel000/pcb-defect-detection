# PCB Defect Detection Using YOLO11
​
An end-to-end computer vision project for detecting and classifying printed circuit board (PCB) defects using the **YOLO11s object detection model**.
​
The model identifies the defect category and localizes each defect with a bounding box. The project was developed and executed on Kaggle using GPU acceleration.
​
[![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Ultralytics](https://img.shields.io/badge/Ultralytics-YOLO11-111F68)](https://github.com/ultralytics/ultralytics)
[![Kaggle](https://img.shields.io/badge/Run%20on-Kaggle-20BEFF?logo=kaggle&logoColor=white)](https://www.kaggle.com/code/ibrahimtwice/pcb-defect-project)
​
## Project Overview
​
Automated visual inspection is an important application of computer vision in electronics manufacturing. Manual PCB inspection can be slow, repetitive, and vulnerable to human error. This project explores an automated detection pipeline that can locate multiple defects in a single PCB image.
​
### Objectives
​
- Detect multiple defects in PCB images.
- Classify each detected defect into one of six defect categories.
- Evaluate detection quality using object-detection metrics.
- Produce annotated prediction images for visual inspection.
​
## Defect Classes
​
The model detects the following six classes:
​
| Class | Description |
|---|---|
| `Missing_hole` | A required hole is missing from the PCB. |
| `Mouse_bite` | An irregular bite-like edge or cut in the PCB. |
| `Open_circuit` | A broken or disconnected conductive path. |
| `Short` | An unintended connection between conductive paths. |
| `Spur` | An unwanted copper extension or projection. |
| `Spurious_copper` | Unwanted copper remaining in an area where it should not exist. |
​
## Dataset
​
This project uses the [PCB Defect Detection Dataset](https://www.kaggle.com/datasets/liuxiaolong1/pcb-defect-detection-dataset), specifically the **DeepPCB** subset.
​
The data is organized into three independent splits:
​
| Split | Images |
|---|---:|
| Training | 1,200 |
| Validation | 150 |
| Test | 150 |
​
The test split contains **984 annotated defect instances**.
​
> Dataset ownership and licensing belong to the original dataset authors. Please review the dataset page before redistributing the data.
​
## Methodology
​
### 1. Label Conversion
​
The source annotations are converted into YOLO format. Bounding-box coordinates are normalized to the range `[0, 1]` and class IDs are mapped to zero-based indices.
​
Each YOLO annotation follows this format:
​
```text
<class_id> <center_x> <center_y> <width> <height>
```
​
### 2. Model
​
- **Architecture:** YOLO11s
- **Task:** Object detection
- **Initialization:** Pretrained weights
- **Input size:** 640 × 640 pixels
- **Batch size:** 16
- **Maximum epochs:** 100
- **Early stopping patience:** 20 epochs
- **Random seed:** 42
- **Hardware:** NVIDIA Tesla T4 GPU
​
### 3. Evaluation
​
The best checkpoint is selected using the validation split. Final performance is then measured on the independent test split.
​
The project reports:
​
- **Precision:** How many predicted detections are correct.
- **Recall:** How many real defects are detected.
- **mAP@0.5:** Mean average precision using an IoU threshold of 0.50.
- **mAP@0.5:0.95:** Mean average precision averaged across IoU thresholds from 0.50 to 0.95.
​
## Results
​
### Overall Test Performance
​
| Metric | Score |
|---|---:|
| Precision | **97.41%** |
| Recall | **95.30%** |
| mAP@0.5 | **98.21%** |
| mAP@0.5:0.95 | **75.80%** |
​
> **Important:** `mAP@0.5` is an object-detection metric, not traditional classification accuracy.
​
### Per-Class Performance
​
| Class | Precision | Recall | mAP@0.5 | mAP@0.5:0.95 |
|---|---:|---:|---:|---:|
| Missing hole | 0.980 | 0.963 | 0.983 | 0.695 |
| Mouse bite | 0.975 | 0.909 | 0.955 | 0.687 |
| Open circuit | 0.977 | 0.948 | 0.991 | 0.745 |
| Short | 0.987 | 0.978 | 0.985 | 0.737 |
| Spur | 1.000 | 0.963 | 0.995 | 0.850 |
| Spurious copper | 0.927 | 0.956 | 0.984 | 0.834 |
​
### Interpretation
​
- The model detects PCB defects with high overall precision and recall.
- `Spur` achieved the strongest localization performance.
- `Mouse_bite` is the most challenging class, particularly under the stricter mAP@0.5:0.95 metric.
- The difference between mAP@0.5 and mAP@0.5:0.95 indicates that detection is strong, while exact bounding-box localization can still be improved.
​
## Repository Structure
​
```text
pcb-defect-detection/
├── PCB_Defect_project.ipynb   # Main Kaggle notebook
├── README.md                   # Project documentation
├── requirements.txt            # Python dependencies
└── .gitignore                  # Ignored datasets, outputs, and model files
```
​
## Installation
​
Create a Python environment and install the required packages:
​
```bash
python -m venv .venv
source .venv/bin/activate       # macOS/Linux
# .venv\\Scripts\\activate      # Windows
​
pip install -r requirements.txt
```
​
Example `requirements.txt`:
​
```text
ultralytics==8.4.155
pillow
numpy
opencv-python
matplotlib
```
​
## Running the Project
​
### Kaggle
​
The original notebook can be opened and executed on Kaggle:
​
[Open the notebook on Kaggle](https://www.kaggle.com/code/ibrahimtwice/pcb-defect-project)
​
The notebook requires the PCB dataset as an input source and uses a GPU runtime for practical training time.
​
### Local Execution
​
Open and run:
​
```text
PCB_Defect_project.ipynb
```
​
Before running locally, update the dataset paths. The original notebook uses Kaggle-specific paths such as:
​
```text
/kaggle/input/...
/kaggle/working/...
```
​
These paths must be replaced with local paths when running outside Kaggle.
​
## Output Files
​
The training process generates typical Ultralytics output files, including:
​
- Training and validation curves.
- Precision–Recall curves.
- F1-score curves.
- Confusion matrix visualizations.
- Label distribution plots.
- The best model checkpoint: `best.pt`.
- Annotated prediction images.
​
The prediction stage uses a confidence threshold of `0.25` and saves annotated test images for visual review.
​
## Limitations
​
- The dataset is relatively small for a deep-learning object-detection task.
- Performance may vary on PCB images from different cameras, lighting conditions, resolutions, or manufacturing processes.
- `Mouse_bite` and `Missing_hole` require further improvement in precise localization.
- The reported results are based on the selected dataset and should not be interpreted as universal industrial performance.
- The notebook currently depends on Kaggle directory paths unless they are updated for local execution.
​
## Future Improvements
​
- Add more PCB images from different production environments.
- Use stronger and more diverse data augmentation.
- Tune the confidence and IoU thresholds for the target inspection scenario.
- Compare YOLO11s with larger or alternative detection architectures.
- Perform cross-validation or repeated experiments with multiple random seeds.
- Add inference-speed and memory benchmarks.
- Evaluate the model on a completely external dataset.
- Export the model to an optimized deployment format such as ONNX or TensorRT.
​
## Reproducibility Notes
​
- The training seed is set to `42`.
- The best model is selected based on validation performance.
- The test set is reserved for final evaluation.
- The Ultralytics version used in the original run was `8.4.155`.
- The original experiment ran with a Tesla T4 GPU.
​
## Citation
​
If you use this project in an academic report or presentation, cite the original dataset and the Ultralytics YOLO framework in addition to this repository.
​
## Author
​
**Ibrahimadel**
​
- Kaggle: [ibrahim_twice](https://www.kaggle.com/ibrahimtwice)
- Project notebook: [PCB Defect Project](https://www.kaggle.com/code/ibrahimtwice/pcb-defect-project)
​
