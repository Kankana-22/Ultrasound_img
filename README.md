<<br><br><br><br><br><br><br>


<div align="center">
  <h1><strong>Internship Report</strong></h1>

  <br><br>

  <h2><em>Ultrasound Imaging: B-mode Implementation, Elastography Visualization, and Image Quality Evaluation</em></h2>

  <br><br><br><br>

  <p><strong>Submitted by:</strong><br>
  <strong>Kankana Das</strong><br>
  Department of Electronics and Communication Engineering<br>
  NIT Silchar<br>
  03 July, 2025</p>

  <br><br>

  <p><strong>Under the guidance of:</strong><br>
  <strong>Dr. Manish Bhatt</strong><br>
  Department of Electronics & Electrical Engineering<br>
  IIT Guwahati</p>
</div>

<div style="page-break-after: always;"></div>


### **1.  Installation of Ultrasound Machine**

To set up the system environment for ultrasound data acquisition and processing, the following files and resources were essential:
* Main Software
  - File Name: k32v-elasto-R5.zip
  - Purpose: Contains the core application for ultrasound signal acquisition and processing, including interface scripts and configuration files.
* Driver Installer
  - File Name: hfrUsc.exe
  - Purpose: Installs the necessary USB driver to enable communication between the host PC and the ultrasound probe.
* Installation Video
  - Description: A video tutorial providing a detailed walkthrough of the installation process to ensure proper setup.  
    https://drive.google.com/file/d/1UXWY6-xI2PMXhA6JNui44EJvC6uhnuaP/view?usp=drive_link

 * Step-by-Step Installation Process
   - Download the file k32v-elasto-R5.zip.
   - Right-click on the file and select “Extract All”.
   - Recommended extraction path
   - Run the driver installer: hfrUsc.exe.
   - If prompted by User Account Control (UAC), grant administrator permissions.
   - Follow the on-screen instructions to complete the installation of the USB driver.
   - Once the installation is complete, restart the system to apply changes.
   - After the system has restarted, connect the ultrasound probe to a USB port.
   - Allow Windows to automatically recognize the device.


### **2.  Acquiring the B-mode Image**

* Launch Software:  
   - Open the ultrasound imaging software in Windows.
  
* Configure Imaging Mode:  
  - Select B-mode (Brightness mode) to ensure 2D grayscale image acquisition.

* Verify settings:  
  - Focus: Adjust the focus manually.  
  - Gain: Adjust to optimize High/Low gain.

* Position Probe & Scan Subject:  
  - Apply ultrasound gel to the probe to eliminate air gaps and ensure efficient sound transmission; without it, image quality is reduced and the probe surface may get damaged 
  - Place the transducer probe on the target region.  
  - Adjust probe orientation to obtain a clear image.

* Capture & Save Image:
  - Start real-time scanning using the ultrasound software with the probe properly connected and gel applied.  
  - Capture the image using the record option provided in the software interface.
  - The captured image is automatically saved in a .BIN format (raw binary data).
  - Open the corresponding folder where the .BIN file is stored.
  - Use MATLAB to read and convert the .BIN file to PNG or JPEG for further processing and visualization.
  - Save the converted image for tasks such as filtering, PSNR/CNR analysis, or elastography display.


### **3.  Image Filtering Process**

![filtered_image](https://github.com/user-attachments/assets/cf681c95-1ca0-488b-acc5-f2dcefa7c102)

The Matlab code is:

<mark>% Step 1: Read the image</mark>  
img = imread('C:\Users\kanka\Downloads\ultrasound images\b-mode2.png');  
<mark>% Step 2: Convert to grayscale (in case it's RGB)</mark>  
if size(img, 3) == 3  
    &nbsp;&nbsp;&nbsp;&nbsp;gray_img = rgb2gray(img);  
else  
   &nbsp;&nbsp;&nbsp;&nbsp;gray_img = img;  
end  
<mark>% Step 3: Apply Gaussian filter</mark>  
sigma = 2; % You can adjust the standard deviation  
filtered_img = imgaussfilt(gray_img, sigma);  
<mark>% Step 4: Display original and filtered images</mark>  
figure;  
subplot(1,2,1);  
imshow(gray_img);  
title('Original Image');

*Steps:*  
* Convert to grayscale if necessary.
* Apply a Gaussian filter to remove noise and smooth the image
  

### **4.  PSNR (Peak Signal-to-Noise Ratio) Calculation**

PSNR is used to measure the quality of a processed image relative to its original (reference)  image. It is defined as:

![Screenshot 2025-06-24 145021](https://github.com/user-attachments/assets/89dfe14c-e5f6-40af-9139-3d46824efc97)

The Matlab code is:  

original = imread('C:\Users\kanka\Downloads\ultrasound images\b-mode2.png');  
<mark>% Convert to grayscale if not already</mark>  
if size(original, 3) == 3  
    &nbsp;&nbsp;&nbsp;&nbsp;original_gray = rgb2gray(original);  
else  
    &nbsp;&nbsp;&nbsp;&nbsp;original_gray = original;  
end  
<mark>% Apply Gaussian filter</mark>  
filtered = imgaussfilt(original_gray, 2);  
<mark>% Convert both to double</mark>  
original_double = im2double(original_gray);  
filtered_double = im2double(filtered);  
<mark>% Calculate PSNR</mark>    
psnr_value = psnr(filtered_double, original_double);  
<mark>% Display result</mark>  
fprintf('PSNR between original and filtered image: %.2f dB\n', psnr_value);

*Steps:*
* Input Image Preparation:
  - The original B-mode ultrasound image is loaded and converted to grayscale.
* Noise Reduction:
  - A Gaussian filter (σ = 2) is applied to suppress noise while preserving edges.
* Data Conversion:
  - Both original and filtered images are converted to double-precision format for accurate 
calculations.
* PSNR Calculation:
  - PSNR is calculated to quantify the similarity, with higher values indicating better quality.


### **5.  CNR (Contrast-to-Noise Ratio)** 

CNR is computed by selecting signal and background regions from the grayscale image.

![Screenshot 2025-06-24 154636](https://github.com/user-attachments/assets/dc506b9e-a80f-4e1d-a5ad-78cad814976f)

The Matlab code is:

 <mark>% Read the grayscale B-mode image</mark>  
img = imread('C:\Users\kanka\Downloads\ultrasound images\b-mode2.png');  
<mark>% Convert the image to double for processing</mark>    
img = double(img);  
<mark>% Threshold to define signal and background (adjust threshold as needed)</mark>  
signal_mask = img > 200;  % Signal = bright regions  
background_mask = ~signal_mask;  % Background = dark regions  
<mark>% Extract signal and background pixel values</mark>  
signal_pixels = img(signal_mask);  
background_pixels = img(background_mask);  
<mark>% Compute mean and std dev</mark>  
mean_signal = mean(signal_pixels);  
mean_background = mean(background_pixels);  
std_background = std(background_pixels);  
<mark>% Compute CNR</mark>  
CNR = (mean_signal - mean_background) / std_background;  
<mark>% Display result</mark>  
fprintf('Contrast-to-Noise Ratio (CNR): %.2f\n', CNR);

*Steps:*
* Image Preprocessing:
  - Convert input image to grayscale using luminosity weights 
* Region of Interest (ROI):
   - Manually or algorithmically select the target area using Bounding box coordinates.
* Statistical Analysis:
  - Compute for both Mean Intensity and Standard Deviation.
* CNR Calculation:
  - CNR is given by the absolute difference of means divided by the combined standard deviation. A higher CNR indicates better contrast between the target and background.

### **6. Elastography**

Elastography is an advanced ultrasound imaging technique used to visualize the stiffness or elasticity of soft tissues. 

![elastography1_color](https://github.com/user-attachments/assets/764bf1f7-f47b-4da4-ad11-9b91f78a93a4)

The Matlab code is:

<mark>% Load elastography image</mark>  
img = imread('C:\Users\kanka\Downloads\ultrasound images\elastography-1.png');  
<mark>% Display the image</mark>  
imshow(img, []);  
title('Elastography Image');  
colorbar;

*Steps:*
* The elastography image is loaded and displayed using MATLAB.
* It visualizes tissue stiffness, with color intensity indicating elasticity.
* A colorbar is included to aid interpretation of stiffness values.
* This aids in identifying abnormal or stiffer regions in the tissue.
