# Handwriting to Text Challenge

## How to Use

1. **Attach Arduino Nano BLE-33 to Marker**:
   - Use tape to securely attach the Arduino Nano BLE-33 device to a marker. Ensure it is glued towards the nib of the marker, not at the end.

2. **Open Arduino IDE and Run the Model**:
   - Launch the Arduino IDE and upload/run our model onto the Arduino Nano BLE-33 device.

3. **Start Writing the capital Alphabets**:
   - Begin writing (only capital Alphabets) with the marker. The model will capture acceleration and gyroscope values to predict the letter you are writing.

## How to Write

1. **Initiate Data Capture**:
   - Our model is programmed to start capturing values when the marker is suddenly brought down. Move the pen swiftly in the z-direction from above to down onto the paper.
<img src="https://github.com/karthikpeddi14/Handwriting-to-Text-Challenge/blob/main/Screenshot%202024-03-24%20182240.png" alt="Alt text" width="200"/> 

2. **Write Alphabet**:
   - Once the data capture starts, write the desired alphabet with the marker.
     
<img src="https://github.com/karthikpeddi14/Handwriting-to-Text-Challenge/blob/main/pen%20writing.png" alt="Alt text" width="200"/> 


3. **Stop Data Capture**:
   - After completing the writing, repeat the process in reverse by swiftly bringing the pen upwards. This action will stop the program from recording values.
     


<img src="https://github.com/karthikpeddi14/Handwriting-to-Text-Challenge/blob/main/Screenshot%202024-03-24%20182240.png" alt="Alt text" width="200"/> 

## Viewing Output

- If you followed the steps in the correct order, you can view the output in the serial monitor of the Arduino IDE.
-------------------------------------------------


## Overview of our Approach

This project aims to convert handwritten alphabet characters into digital text using accelerometer and gyroscope data. It is divided into three main parts: Data generator, Preprocessing, and Model.

### 1. Data Generator
#### a) Serial Library Usage
We utilized the Serial Library to capture the output from the Serial monitor of the Arduino IDE, where our Nano BLE – 33 was attached to a marker. This data was then converted into float values and saved into a CSV file named "letter_data.csv" for training our model.

### 2. Preprocessing
#### a) Interpolation
We converted the accelerometer and gyroscope data, initially in string form, into arrays of floats. To standardize the lengths of recordings, we employed linear interpolation. This ensured uniformity in the number of timestamps for each letter, aiming for 60 timestamps per letter.

#### b) Down-Sampling
For letters with more than 60 timestamps, we employed down-sampling to uniformly select 60 timestamps.

#### c) Concatenation and Dimensionality Reduction
Accelerometer and gyroscope values were combined into a 3-D array, which was then transformed into a 2-D array of shape (number of samples, 360).

#### d) Feature Scaling
We standardized the data using the Z-score method, ensuring consistency and comparability in the features.

#### e) Target Variables
Categorical alphabet variables were converted into numerical values using Label Encoder, facilitating model training.

#### f) Train-Test Split
The dataset was split into training and test sets in an 80:20 ratio, respectively. The training data was used to fit the model.

### 3. Model
We constructed a TensorFlow Sequential model comprising Convolutional and Dense layers. The model architecture is as follows:

```python
model = Sequential([
    Conv1D(filters=64, kernel_size=12, activation='relu', input_shape=input_shape),
    MaxPooling1D(pool_size=6),
    Conv1D(filters=128, kernel_size=12, activation='relu'),
    MaxPooling1D(pool_size=6),
    Flatten(),
    Dense(100, activation='relu'),
    Dropout(0.5),
    Dense(11, activation='softmax')  
])
```
![Alt text](https://github.com/karthikpeddi14/Handwriting-to-Text-Challenge/blob/main/modelphoto.jpg)

We compiled the model using the Adam optimizer and sparse_categorical_crossentropy loss function. The model was trained for 100 epochs with a validation split of 0.2.
