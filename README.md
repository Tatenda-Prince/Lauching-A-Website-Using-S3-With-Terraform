# Lauching-A-Website-Using-S3-With-Terraform
"Terraforming with S3"

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/a919579493487ea74996fdd25dacc0e855a8f2fd/img/Screenshot%202025-01-26%20100923.png)

# Introduction

Hello everyone! I'm still motivated to improve my skills with AWS, and today I decided to give S3 a try at establishing a website. For those who do not know, S3 is a storage service that provides security, scalability, and data availability. Any company, regardless of size, may utilise S3 to safeguard its data for any use case, including data lakes, cloud-native apps, and mobile apps. Furthermore, it provides many storage classes, like Infrequent Access, Intelligent-Tiering, and Glacier, to mention a few.

In this project, I used Terraform and Amazon Web Services (AWS) S3 to set up a straightforward and effective method for hosting a modest website. This project's primary goal is to demonstrate how to easily and automatically develop and launch a website.

I built a blueprint for the AWS S3 architecture required to host the website using Terraform. This made it simple for me to organise and automate the setup and management of all required resources.

Now that we have some background on what S3 is let’s launch our website.

# Prerequisites:

1.AWS Account: You need an AWS account to access AWS services, including S3.

2.Terraform Installed: Ensure Terraform is installed on your local machine to define and provision AWS resources.

3.AWS CLI Installed and Configured: Install the AWS Command Line Interface (CLI) and configure it with your AWS credentials.

4.Static Website Files: Prepare the static website files (HTML, CSS, JavaScript, images, etc.) that you want to host.

5.Basic Understanding of Terraform and AWS: Familiarize yourself with Terraform basics and have a basic understanding of AWS services, particularly S3.

6.IDE: In my opinion, the VS Code Editor stands out as the top pick for an Integrated Development Environment (IDE).


## Step 1: To create the content for your static website

Copy and paste the link and clone the repository and copy all the file to your local machine.
 
```language
git clone https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform.git
```

1.Prepare HTML Files: Create the HTML files for your website content. Place these files in the same directory where your Terraform configuration files are located.

2.Main HTML File: Name your main HTML file “index.html”. This file will be the entry point for your website. Ensure it contains the necessary content and structure for your homepage.

![image_alt]()


## Step 2: Terraform Configuration File Syntax

1.File Extension: Terraform configuration files should have the “.tf” extension. For example, you can name your main configuration file “main.tf”


2.File Structure: Inside the “.tf” file, you’ll define the resources and configurations needed for your infrastructure. The syntax follows HashiCorp Configuration Language (HCL) or optionally, JSON format




