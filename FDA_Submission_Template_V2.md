# FDA  Submission
Jay Veezhinathan

Pneumonia Diagnostic Assitant

## Algorithm Description 

### 1. General Information

**Intended Use Statement:** 
To assitant doctors in detecting pneumonia from chest X-rays

**Indications for Use:**
1) To be used for assisting doctors in chest X-ray studies
2) To be used for men and women in the ages ???10 and 90??? 

**Device Limitations:**
1)  The software assistant has not been tested and evalauated for patienst with 
    previous history of Pneumonia
2)  The software may not work effectively due to slow performance on hadware without GPU.

**Clinical Impact of Performance:**
Since the purpose of the software assitant is to assitant in screening for Pneumonia, the theshold for 
detecting Penumonia has been set to maximize recall,  Hense Falls Negatives(FNs) has been the primary concern and not 
Falls Positives (FPs)

### 2. Algorithm Design and Function

<< Insert Algorithm Flowchart >>
![X-Ray Image Analysis Flow Chart](FlowchartDICOM.png?raw=true)

**DICOM Checking Steps:**
As we read each image from the disk we check the following attributes of the DICOM image to check if the 
image is valid:

1) The body part examined is "Chest"
2) The modality is "DX"
3) The body position is "PA" or "AP"

**Preprocessing Steps:**
The pixel array obtained from the DICOM image is preprocessed.  The usual standardization of the pixel values (i.e., mean zero standard deviation = 1) is not done here since we did not do this during the preprocessing step of the training.  Instead we divided teh pixel values by 255 to scale the values to be between 0 and 1.  We followed teh same here.  

**CNN Architecture:**
The Convolutional Neural Network(CNN) architecture adopted for this project is the VGG16 CNN and we used transfer learning 
to fine tune the last few layers.  We loaded the pretrained network upto and including 'block5_pool' layer.  These layers were forzen from further training.  Then we flattened and added two dense layers and final output layer.  

### 3. Algorithm Training

**Parameters:**
* Types of augmentation used during training
* Batch size
* Optimizer learning rate
* Layers of pre-existing architecture that were frozen
* Layers of pre-existing architecture that were fine-tuned
* Layers added to pre-existing architecture

We used Adam optimizer and minimized binary crossentropy with a learning rate of 0.0001 and metrics = 'binary_accuracy'.

The following image augmentation were used to create additional training images during training:
    1) horizontal_flip = True 
    2) vertical_flip = False
    3) height_shift_range = 0.1
    4) width_shift_range = 0.1
    5) rotation_range  = 20
    6) zoom = 0.1
    7) shear = 0.1
    
 A few examples of augmented images are shown below:
 
![Examples of Augmented Images](augmented_images.png, raw=true)

 
 Batch Size of 32 and learning rate of 1e-4 alonng with Adam optimizer were used during teh training process.
 
![Training Loss and Accuracy](Training_Loss_Accuracy.png, raw=true)

<< Insert algorithm training performance visualization >> 

![Training Recall Performance](recall.png, raw=true)

<< Insert P-R curve >>

**Final Threshold and Explanation:**
For a threshold of 0.5, the F1 score and recall were maximum at 0.71 and ).70 respectively.  This recall score is better than precison as we want higher recall because of teh screening application of this software.  

### 4. Databases
 (For the below, include visualizations as they are useful and relevant)

**Description of Training Dataset:** 
Our training dataset has 2290 rows of data equally divided between pneumonia positive cases and negative cases.  The gender is distributed with more males than females as follows:

![Training Dataset Gender Distribution](train_gender_dist.png, raw=true)

The distribution of Patient Age is as follows with most cases between 10 and 80 in the training set.

![Training Dataset Age Distribution](train_age_dist.png, raw=true)

**Description of Validation Dataset:** 

We have 1430 rows in the validation dataset with following distributions of Patient Gender and Age. From these  we see similar distributions of gender and age between training and validation datasets.  

![Validation Dataset Gender Distribution](train_gender_dist.png, raw=true)


![Validation Dataset Age Distribution](train_age_dist.png, raw=true)


### 5. Ground Truth

The ground thruth was obtained from the NIH X-ray dataset using NLP derived labels.  The NIH X-ray is an imbalanced dataset between patients with Pnuemonia and no Pneumonia and other diseases.  The NLP technique is not teh best approach for groud truth labels since it identified multiple labels in many cases and so there are more patients with other diseases than just Pneumonia.  This imbalanced distribution of Pneumonia versus other diseses could affect the clinical performance of the algorithm.    

### 6. FDA Validation Plan

**Patient Population Description for FDA Validation Dataset:**
The patient polulation for validation must be in the age between 10 and 80 with no previous history of Pneumonia. The prevalence of Pneumonia along with other diseases must also roughly similar to the distribution as given above for patients' gender and age.  The x-ray should be of chest and modality must be 'DX' and patient position must be "PA or 'AP.   

**Ground Truth Acquisition Methodology:**
For obtaining the ground truth for validation, the Silver Standard approach would be recommended with multiple radiologists voting on the on the presence and absence Pneumonia based on X-ray reading.  

**Algorithm Performance Standard:**
Since the software will be used for screening for Pneumonia assiting radiologist, we must achieve a recall performance of atleast ).70.  The F1 Score achieved in the validation dataset was ???? which is better than what has been reported as the average F1 Score obtained from  radiologist ???? (reference ??)