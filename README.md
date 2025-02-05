# Biomedical Image Classification group project - Erdos Institute Bootcamp 2023  
Group Members:  
- Boyang Wu  
- Jason Nguyen  
- Shahinde Dogruer  
- Shashwata Moitra  



# NCT-CRC-HE-100K Classifcation with EfficientNet

This repository contains the code for training a Convolutional Neural Network (CNN) on the CRC (Colorectal Cancer) dataset using the EfficientNet architecture. The training script employs cosine learning rate scheduling and supports training on multiple GPUs.

## Training and Validation DataSet
Training Set NCT-CRC-HE-100K:
This is a set of 100,000 non-overlapping image patches from hematoxylin & eosin (H&E) stained histological images of human colorectal cancer (CRC) and normal tissue. All images are 224x224 pixels (px) at 0.5 microns per pixel (MPP). All images are color-normalized using Macenko's method.

Validation Set CRC-VAL-HE-7K:
This is a set of 7180 image patches from N=50 patients with colorectal adenocarcinoma (no overlap with patients in NCT-CRC-HE-100K).

See https://zenodo.org/records/1214456 for further details and ethics statement.

## Requirements

Packages and dependencies as tested:
```
efficientnet-pytorch==0.7.1
matplotlib==3.8.0
numpy==1.26.0
Pillow==10.0.1
torch==2.0.1+cu118
torchvision==0.15.2+cu118
tqdm==4.66.1
```
Hardware: NVIDIA GPUs recommended (for re-training)

Local workstation specs (as tested):
- 2x NVIDIA RTX A6000
- 1x NVIDIA RTX 3080 Ti

## Installation

1. Clone this repository to your local directory.
2. Install the required Python packages from the ```requirements.txt```. It's recommended to use a virtual environment:

   ```bash
   pip install -r requirements.txt
   ```
## Usage

To train the model using NCT-CRC-HE-100K dataset, use the example below to run the training script from command line:



  ```bash
  python training_CRC_cosineLR.py --train_data_dir "path_to_training_data" \
                                  --val_data_dir "path_to_validation_data" \
                                  --model_name "efficientnet-b0" \
                                  --output_dir "path_to_output_directory" \
                                  --resized_dim 224 \
                                  --learning_rate 1e-3
  
  ```
### Arguments
```--train_data_dir```: The directory containing the training data.

```--val_data_dir```: The directory containing the validation data.

```--model_name```: The EfficientNet model version to use (e.g., efficientnet-b0).

```--output_dir```: The directory where the training logs and model checkpoints will be saved.

```--resized_dim```: The dimensions to which the input images will be resized (default is 224x224 pixels).

```--learning_rate```: The learning rate for the Adam optimizer.

### Input Data Structure
The training and validation data directories should have the following structure (as provided by authors of NCT-CRC-HE-100K dataset (insert citation link here):
  ```bash
  data_directory/
    ADI/
        image1.tif
        image2.tif
        ...
    BACK/
        image1.tif
        ...
    ...
  ```

### Training Output
The script will save model checkpoints and a training log CSV file in the specified output directory. The CSV file contains epoch-wise training and validation accuracy and loss.

## Model Evaluation

To evaluate the trained models using the CRC dataset, follow these steps to run the model_eval.py script from the command line:

### Usage
```bash
python model_eval.py --val_data_dir "path/to/validation/data" \
                     --model_input_dir "path/to/model/checkpoints" \
                     --model_name "efficientnet-b0" \
                     --output_dir "path/to/output/directory" \
                     --resized_dim 224

```
### Arguments
```--val_data_dir```: Path to the directory containing validation data.

```--model_input_dir```: Directory containing saved model checkpoints.

```--model_name```: The EfficientNet model used during training (e.g., efficientnet-b0).

```--output_dir```: Directory where evaluation results will be saved.

```--resized_dim```: The dimensions to which the input images will be resized for evaluation (default is 224x224 pixels).
### Evaluation Output
The script evaluates all model checkpoints found in --model_input_dir and selects the best model based on the overall validation accuracy, 'STR' and 'TUM' class F1 scores and Fbeta scores (beta=1.2). The results include:

* A confusion matrix for the best-performing models.
  
* Classification report detailing precision, recall, F1-score for each class, and overall accuracy/F1 score/Fbeta score.

* ROC curve and AUC for the 'STR' and 'TUM' classes.

* Specificity and sensitivity metrics for the 'STR' and 'TUM' classes.

* The output is saved in the user-defined ```--output_dir``` as visual plots (confusion matrix, ROC curve) and textual information (CSV and TXT files).

## Web Application for Demo
```bash
pip install spams-bin
streamlit run app.py
```
We implemented the Macenko Method for prenormalizing the data writen by https://github.com/wanghao14/Stain_Normalization. We created the Web App by using Streamlit: https://streamlit.io/.
Due to file size limitation on github, here is the link to the pre-trained best checkpoints: https://drive.google.com/drive/folders/11lm2bOeSbNEINklbFIINnWya2lrbNZ9_?usp=sharing
![](webapp_demo.gif)
