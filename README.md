# AWS ML@Edge With NVIDIA Jetson Nano

In this project I will walkthrough how to create ML@Ede video analytics application. This will be end to end process, from data annotation, model building, training, optimization and then deploying model on edge device.

### Step 1: Data Annotation using Amazon Sagemaker GroundTruth
### Step 2: Model building, training and optimization using Sagemaker notebooks, containers and Neo
### Step 3: Deploy model on Jetson Nano using AWS IoT Greengrass
### Step 4: Visualize and analyse video analytics from the model inference on Jetson Nano

Lets start with Step 1 

### Step 1: Data Annotation using Amazon Sagemaker GroundTruth
In this lab we will use Amazon Sagemaker GroundTruth to label images in a training dataset consisting of lego dinosaurs images. 
You will start with an unlabeled image training data set, acquire labels for all the images using SageMaker Ground Truth private workforce and finally analyze the results of the labeling job.

High Level Steps 

1.	Upload training data into an S3 bucket.
2.	Create a private Ground Truth Labeling workforce.
3.	Create a Ground Truth Labeling job
4.	Label the images using the Ground Truth Labeling portal.
5.	Analyze results

### 1.	Upload training data into an S3 bucket.

In this step you will first create an Amazon S3 bucket where you will store the training data.  You will then download the training data consisting of lego dinosaurs images and then upload to the S3 bucket created. 

#### 1.1	 Create an S3 bucket.

In this step you will create an Amazon S3 bucket where you will store the training data.

* Sign into AWS Management Console.
* Search for and choose S3 to open the Amazon S3 console.
* From the Amazon S3 console dashboard, choose Create Bucket.
* In Create Bucket wizard
    - On the ‘Name and region’ Step
      - Type a bucket name in Bucket Name. (For eg., ground-truth-labelling-job-initials-date; Note that this name should be unique across all AWS)
      - Select ‘US East’ as the region. 
      - Click ‘Next’
  - On the ‘Configure Options’ Step
      - Leave defaults and Click ‘Next’
  - On the ‘Set Permissions’ Step
    Uncheck the four checkboxes on this screen that block public access to the data.
      - Click ‘Next’
      - On the ‘Review’ Step, click 'Next', create bucket.

#### 1.2	 Download the training data.

In this step you will download the training data to your local machine.
I have create my own lego dinosaurus dataset, it has about 388 files of 6 dinosaurus classes. Brachiosaurus, Dilophosaurus, Spinosaurus, Stegosaurus, Triceratops and Unknown dinosaurus.

* Download the training data (lego dinosaurs images) from this link
https://sagemaker-nvidia-webinar.s3.amazonaws.com/lego_dinosaurs_dataset.zip

* Extract the lego_dinosaurs_dataset.zip, if necessary.  You should see “lego_dinosaurs_dataset” folder with about 388 files.


#### 1.3	 Upload training data to the S3 bucket.

In this step you will upload the training data to the Amazon S3 bucket created in Step 1.1.  

* Upload the training data to the S3 bucket.
  -	From the Amazon S3 console, click on the S3 bucket created in the above step.  
  -	Click Upload
  -	In the Upload Wizard
    -	On the first step ‘Select files’
* Drag/Drop the ‘lego_dinosaurs_dataset’ folder from your local machine
  - Click Next
* On the ‘Set Permissions’ step
  - Leave defaults and click ‘Next’
  - On the ‘Set properties’ step
* Leave defaults and click ‘Next’
  - On the ‘Review’ step
* Review and click ‘Upload
* You will see the progress bar for the upload.
* Wait till upload is complete.

#### 1.4 Create a private Ground Truth Labeling Workforce.
In this step, you will create a “private workteam” and add only one user (you) to it. 

To create a private team:

*	Go to AWS Console > Amazon SageMaker > Labeling workforces
  - Click "Private" tab and then "Create private team".
  - Enter the desired name for your private workteam.
  - Enter your own email address in the "Email addresses" section.
  - Enter the name of your organization.
  - Enter contact email to administrate the private workteam.
  - Click "Create Private Team".
* The AWS Console should now return to AWS Console > Amazon SageMaker > Labeling workforces. Your newly created team should be visible under "Private teams". 
  - You should get an email from `no-reply@verificationemail.com` that contains your workforce username and password.
  - Use the link and login credentials from the email to access the Labeling portal.
  - You will be asked to create a new, non-default password

That's it! This is your private worker's interface.
Once the Ground Truth labeling job is submitted in the next step, you will see the annotation job in this portal.


#### 1.5 Create a private Ground Truth Labeling Job.
In this step, you will create a Ground Truth Labeling job and assign it to the private workforce.

* Go to AWS Console > Amazon SageMaker > Labeling jobs
* Click ‘Create labeling job’
* In ‘Specify job details’ step
* Job name : groundtruth-labeling-job-lego_dinosaurs (Note : Any unique name will do)
* Input dataset location 
* Create manifest
  - Entire S3 path where images are located. (Note : should end with /; For eg : s3://<bucketname>/<prefix/foldername>/)
  - Select 'Images' as data type
  - Wait till the manifest creation is complete.
  - Click "Use this manifest"
* Output dataset location : Enter S3 bucket path
  (For eg : s3://<bucketname>/<prefix/foldername>/)
*	IAM Role
  - Select 'Create a new role' from the dropdown.
  - In the “Specific S3 buckets” section, enter the S3 bucket created in Step 1 
  - Click Create
*	Task Type
*	Select 'Image classification'
*	Click Next
  -	In 'Workers' Step
    - Select ‘Private’
    - Select the team created in previous step from the Private teams dropdown.  
    - Examine ‘Additional configuration’ options
    - Leave ‘Automated data labeling’ - ‘Enable’ unchecked.
    - Leave ‘Number of workers per dataset object’ at 1
    - In 'Image classification labeling tool' Step
 
*	Enter "Please classify the images as  Brachiosaurus, Dilophosaurus, Spinosaurus, Stegosaurus, Triceratops and Unknown in the textbox as an instruction to the workforce.
  - Add six Options  Brachiosaurus, Dilophosaurus, Spinosaurus, Stegosaurus, Triceratops and Unknown
  - For Good example and Bad example, add links of the public image urls. This is optional
  -	Submit
*	Go to AWS Console > Amazon SageMaker > Labeling jobs to verify that a labeling job has been created.

#### 1.6 Label the images using the Ground Truth Labeling portal

In this step, you will complete a labeling/annotation job assigned to you from the  Ground Truth Labeling portal.  
*	Login to the Ground Truth Labeling portal using the link provided to you in the email from `no-reply@verificationemail.com`.

Once the annotation job is assigned, you can view the job (similar to the picture below)

![](img_1_7_1.png)
 
*	Click ‘Start working’
*	You will start seeing the images that need to be labeled.  For each image, select  Brachiosaurus, Dilophosaurus, Spinosaurus, Stegosaurus, Triceratops and Unknown in the option and click ‘Submit’

![](img_1_7_2.png)

Note : After labeling a subset of images, the annotation job will be complete.  If the first annotation job did not include all images, you will see a new job in the portal after a few minutes. Repeat the process of labeling images in the jobs as they appear in the portal, till all images are labelled.  You can check the status of the labeling job from the Ground Truth  Labeling Jobs, which will show you the number of images labeled out of the total images.

#### 1.7.	Analyze Results

In this step, you will review the manifest files created during the Ground Truth Labeling process.  The manifest files are in the S3 bucket you created in Step 1.

Input Manifest File

Located in S3 bucket in the prefix : lego_dinosaurs_dataset/dataset-xxxxxx.manifest.

The manifest is a json file that captures information about the training data.

Sample :

{"source-ref":"s3://dino-dataset/lego_dinosaurs_dataset/3_Triceratops_084.jpg"}
{"source-ref":"s3:/dino-dataset/lego_dinosaurs_dataset/5_NoDino_245.jpg"}
{"source-ref":"s3://dino-dataset/lego_dinosaurs_dataset/0_Spinosaurus_111.jpg"}
…


Output Manifest File

Located in S3 bucket in the prefix : <labeling-job-name>/manifests/output.manifest

The manifest is a json file that captures metadata about each labeled image. 

Sample: 

{"source-ref": "s3://dino-dataset/3_Triceratops_084.jpg", "dino-image-classification": 3, "dino-image-classification-metadata": {"confidence": 0.94, "job-name": "labeling-job/dino-image-classification", "class-name": "3_Triceratops", "human-annotated": "yes", "creation-date": "2019-05-25T08:54:54.133410", "type": "groundtruth/image-classification"}}
{"source-ref": "s3://dino-dataset/5_NoDino_245.jpg", "dino-image-classification": 5, "dino-image-classification-metadata": {"confidence": 0.95, "job-name": "labeling-job/dino-image-classification", "class-name": "5_Unknown", "human-annotated": "yes", "creation-date": "2019-05-25T08:37:55.495129", "type": "groundtruth/image-classification"}}
{"source-ref": "s3://dino-dataset/0_Spinosaurus_111.jpg", "dino-image-classification": 0, "dino-image-classification-metadata": {"confidence": 0.68, "job-name": "labeling-job/dino-image-classification", "class-name": "0_Spinosaurus", "human-annotated": "yes", "creation-date": "2019-05-25T08:58:35.374405", "type": "groundtruth/image-classification"}}
{"sourc
….

Along with the other metadata information, the output manifest shows the identified class of the image and confidence.  

Now we need to build model, train model and optimize model.

### Step 2: Model building, training and optimization using Sagemaker notebooks, containers and Neo
Model builing, training and optimization is simiplifed by Sagemaker notebooks, training container and Neo.
All these steps can be done using single notebook. Please follow attached notebook 
[Sagemaker notebook](sagemaker_image_classification.ipynb). Download this notebook and upload it to your Sagemaker environment. To create Sagemaker notebook environment, please follow this [guide](https://github.com/awslabs/amazon-sagemaker-workshop/tree/master/NotebookCreation) 

Beauty of jupyternotebook is that it can contains code as well as comments. I will use the notebook to explain model building, training and optimization.


Now that model is build and optimized, now we can deploy this model on NVIDIA Jetson Nano using AWS IoT Greengrass

### Step 3: Deploy model on Jetson Nano using AWS IoT Greengrass
This step will need
- 3.1 Installing AWS IoT Greengrass 
- 3.2 Setup and configure Inference code using AWS Lambda
- 3.3 Set machine leaning at edge deployment
- 3.4 Deploy machine learning at edge on NVIDIA Jetson Nano
- 3.5 Run model, check inference

#### 
- 3.1 Installing AWS IoT Greengrass 

First setup Setup your Jetson Nano Developer Kit with the SD card image.

Run the following commands on your Nano to create greengrass user and group:

```
$ sudo adduser --system ggc_user
$ sudo addgroup --system ggc_group
```

Setup your AWS account and Greengrass group during this page: https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html
After downloading your unique security resource keys to your Jetson that were created in this step, proceed to #4 below.

Download the AWS IoT Greengrass Core Software (v1.9.1) for ARMv8 (aarch64):

```
$ wget https://d1onfpft10uf5o.cloudfront.net/greengrass-core/downloads/1.9.1/greengrass-linux-aarch64-1.9.1.tar.gz
```

Following this page (starting with step #4 from that page), extract Greengrass core and your unique security keys on your Nano:

```
$ sudo tar -xzvf greengrass-linux-aarch64-1.9.1.tar.gz -C /
$ sudo tar -xzvf <hash>-setup.tar.gz -C /greengrass   # these are the security keys downloaded above
```

Download AWS ATS endpoint root certificate (CA):

```
$ cd /greengrass/certs/
$ sudo wget -O root.ca.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
```

Start greengrass core on your Nano:

```
$ cd /greengrass/ggc/core/
$ sudo ./greengrassd start
```

You should get a message in your terminal "Greengrass sucessfully started with PID: xxx"

#### 3.2 Setup and configure Inference code using AWS Lambda

Go to [AWS Management console](https://console.aws.amazon.com/console/home?region=us-east-1) and search for Lambda

Click 'Create function'

Choose 'Blueprints'

In the search bar, type “greengrass-hello-world” and hit Enter

Choose the python blueprint and click Configure

Name the function: aws-nvidia-video-analysis-your-name
Role: Choose an existing role
[Note: You may need to create new role, give basic execution permissions, choose default)

Click Create Function
Replace the default script with the [inference script](inference-lambda.py)

#### 3.3  Set machine leaning at edge deployment
- Go to [AWS Management console](https://console.aws.amazon.com/console/home?region=us-east-1) and search for Greengrass
- Go to AWS IoT Greengrass console
- Choose the greengrass group you created in step 3.1
- Select lambda, choose lambda function you created in 3.2
- make it the lambda long running per doc ![https://docs.aws.amazon.com/greengrass/latest/developerguide/long-lived.html]
(https://docs.aws.amazon.com/greengrass/latest/developerguide/long-lived.html)
- In memory, set it to 300MB
- In resources, add ML model, Select Sagemaker trained model, select job that you created in Sagemaker build model step

#### 3.4 Deploy machine learning at edge on NVIDIA Jetson Nano
- Go back to AWS IoT Greengrass console
- We will need to send messages from NVIDIA Jetson to cloud. so we need to setup message routing per screenshot below.
Select from device jetson nano, to cloud and message topi is "fromnano"
- Click deploy
- This will take few minus to download and deploy model

#### 3.5 Check inference
- Go to [AWS Management console](https://console.aws.amazon.com/console/home?region=us-east-1) and search for Greengrass
- Go to AWS IoT console
![](img_3_5_1.png)
- Select Test from left menu
![](img_3_5_2.png)
- Add "#" in Subscribe topic, click Subscribe. This will subscribe to all IoT topics comming to Jetson Nano
![](img_3_5_3.png)
- In Subscrition box you will start seeing IoT messages coming from Jetson nano

### Step 4: Visualize and analyse video analytics from the model inference on Jetson Nano
The lambda code running on NVIDIA Jetson Nano device sends IoT messages back to cloud. These messages are sent to AWS CloudWatch. CloudWatch has built in dashboard. We will use the built in dashboard to visualize data coming from the device.

Go to [AWS Management console](https://console.aws.amazon.com/console/home?region=us-east-1) and search for Cloudwatch

Create a dashboard called “aws-nvidia-jetson-nano-dashboard-your-name”

Choose Line in the widget

Under Custom Namespaces, select “string”, “Metrics with no dimensions”, and then select all metrics.

Next, set “Auto-refresh” to the smallest interval possible (1h), and change the “Period” to whatever works best for you (1 second or 5 seconds)

You will see analysis on number of times different dinosaurs detected by NVIDIA Jetson Nano

NOTE: These metrics will only appear once they have been sent to Cloudwatch via the Lambda code running on edge. It may take some time for them to appear after your model is deployed and running locally. If they do not appear, then there is a problem somewhere in the pipeline.


### With this we have come to the end of the session. As part of building this project, you learnt the following:

1.	How to create and annotate dataset for computer vision based model using Amazon Sagemaker GroundTruth
2.  How to build and train and optimize model in Amazon SageMaker
3.	Setup and configure AWS IoT Greengrass 
4.	Deploy the inference lambda function and model on NVIDIA Jetson Nano
5.	Analyze model inference data using AWS CloudWatch
