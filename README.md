# Lauching-A-Website-Using-S3-With-Terraform
"Terraforming with S3"

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/a919579493487ea74996fdd25dacc0e855a8f2fd/img/Screenshot%202025-01-26%20100923.png)

# Introduction

Hello everyone! I'm still motivated to improve my skills with AWS, and today I decided to give S3 a try at establishing a website. For those who do not know, S3 is a storage service that provides security, scalability, and data availability. Any company, regardless of size, may utilise S3 to safeguard its data for any use case, including data lakes, cloud-native apps, and mobile apps. Furthermore, it provides many storage classes, like Infrequent Access, Intelligent-Tiering, and Glacier, to mention a few.

In this project, I used Terraform and Amazon Web Services (AWS) S3 to set up a straightforward and effective method for hosting a modest website. This project's primary goal is to demonstrate how to easily and automatically develop and launch a website.

I built a blueprint for the AWS S3 architecture required to host the website using Terraform. This made it simple for me to organise and automate the setup and management of all required resources.

Now that we have some background on what S3 is let’s launch our website.

# Prerequisites:

1. AWS Account: You need an AWS account to access AWS services, including S3.

2. Terraform Installed: Ensure Terraform is installed on your local machine to define and provision AWS resources.

3. AWS CLI Installed and Configured: Install the AWS Command Line Interface (CLI) and configure it with your AWS credentials.

4. Static Website Files: Prepare the static website files (HTML, CSS, JavaScript, images, etc.) that you want to host.

5. Basic Understanding of Terraform and AWS: Familiarize yourself with Terraform basics and have a basic understanding of AWS services, particularly S3.

6. IDE: In my opinion, the VS Code Editor stands out as the top pick for an Integrated Development Environment (IDE).


## Step 1: To create the content for your static website

Copy and paste the link and clone the repository and copy all the file to your local machine.
 
```language
git clone https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform.git
```

1.Prepare HTML Files: Create the HTML files for your website content. Place these files in the same directory where your Terraform configuration files are located.

2.Main HTML File: Name your main HTML file “index.html”. This file will be the entry point for your website. Ensure it contains the necessary content and structure for your homepage.


![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/74abdd0ea7ba75516b9efccb3db9eb543f3dba9a/img/Screenshot%202025-01-26%20104444.png)


## Step 2: Terraform Configuration File Syntax


1.File Extension: Terraform configuration files should have the “.tf” extension. For example, you can name your main configuration file “main.tf”


2.File Structure: Inside the “.tf” file, you’ll define the resources and configurations needed for your infrastructure. The syntax follows HashiCorp Configuration Language (HCL) or optionally, JSON format

For all my terraform files I decided to create a directory where I will store all of my files, just to keep my code nice and tidy.


```language

mkdir Terraform_S3_Web_Hosting

cd Terraform_S3_Web_Hosting

touch providers.tf

touch variables.tf

touch main.tf 

touch output.tf

```

## Step 3: Customize your environment by defining your configuration settings within the IDE’s dedicated files.

1.Define Provider Configuration: Create a file named “provider.tf” in your project directory and add the following code to specify the AWS provider and desired region:

```langauge

terraform {
  required_providers {
    aws = {
        source = "hashicorp/aws"
        version = "~>4.0"
    }
  }
}

provider "aws" {
    region = var.aws_region
}
```


2.This Terraform configuration sets up a static website hosted on AWS S3. It creates an S3 bucket with a public-read ACL, configures a bucket policy to allow public access to its objects, and enables static website hosting with "index.html" as the default page. 

The aws_s3_object resource uploads website files retrieved from a specified module (hashicorp/dir/template) into the bucket. 

The configuration ensures the site is accessible by defining the necessary policies and permissions. However, minor adjustments, such as correctly using either source or content in aws_s3_object, improving bucket policy references for consistency, and optionally adding an error document, can enhance functionality. Overall, it efficiently provisions the infrastructure for static website hosting while leveraging modular design for scalability and reuse.

```language
module "template_files" {
    source = "hashicorp/dir/template"

    base_dir = "${path.module}/web"
  
}

resource "aws_s3_bucket" "hosting_bucket" {
    bucket = var.bucket_name
  
}

resource "aws_s3_bucket_acl" "hosting_bucket_acl"{
    bucket = aws_s3_bucket.hosting_bucket.id
    acl = "public-read"
}

resource "aws_s3_bucket_policy" "hosting_bucket_policy" {
    bucket = aws_s3_bucket.hosting_bucket.id
    policy = jsonencode({"Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::${var.bucket_name}/*"
            }
        ]

    })
}

resource "aws_s3_bucket_website_configuration" "hosting_bucket_website_configuration" {
    bucket = aws_s3_bucket.hosting_bucket.id

    index_document{
        suffix = "index.html"

    }

  
}

resource "aws_s3_object" "hosting_bucket_files" {
    bucket = aws_s3_bucket.hosting_bucket.id

    for_each = module.template_files.files

    key = each.key
    content_type = each.value.content_type

    source  = each.value.source_path
    content = each.value.content

    etag = each.value.digests.md5
  
}
```

3.This code snippet defines an output block in Terraform, which is used to display or export specific values from a Terraform configuration. The output "website_url" block is named "website_url" and has a description explaining that it represents the URL of the website.

The value assigned to this output is derived from the website_endpoint attribute of the aws_s3_bucket_website_configuration resource. This resource likely configures an S3 bucket for static website hosting on AWS. When Terraform applies this configuration, it retrieves the S3 bucket's website endpoint URL (the public URL where the static website can be accessed) and outputs it.

This is useful for referencing the website's URL in other Terraform configurations or for providing a convenient way to view the endpoint in the Terraform output.

```language
output "website_url" {
    description = "URL of the website"
    value = aws_s3_bucket_website_configuration.hosting_bucket_website_configuration.website_endpoint
}
```

4.The terraform.tfvars file sets values for variables used in your Terraform configuration. In this example, aws_region = "us-east-1" specifies the AWS region (North Virginia) where resources will be deployed, and bucket_name = "tatenda-web-hosting-bucket" defines the name of an S3 bucket, likely used for web hosting.

```language

aws_region = "us-east-1"
bucket_name = "tatenda-web-hosting-bucket"
```

5.This variable.tf code defines two input variables for a Terraform configuration. The first variable, aws_region, is a string used to specify the AWS region where resources will be deployed. The second variable, bucket_name, is also a string and represents the name of an AWS S3 bucket. Both variables have descriptions to clarify their purpose, and their types are explicitly set as string. This makes the Terraform configuration more flexible by allowing users to provide these values dynamically.

```language
variable "aws_region" {
    description = "Aws Region"
    type = string
  
}

variable "bucket_name" {
    description = "Name of the bucket"
    type = string
  
}
```

## Step 4: Run Terraform workflow to initialize, validate, plan then apply

In your IDEterminal, to initialize the necessary providers, execute the following command in your IDE terminal —

```language
terraform init

```
Upon completion of the initialization process, a successful prompt will be displayed, as shown below.

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/1b4f50f53e3ca398228ddf080372f00c76bc6330/img/Screenshot%202025-01-26%20112352.png)

Next, let’s ensure that our code does not contain any syntax errors by running the following command —

```language
terraform validate

```
The command should generate a success message, confirming that it is valid, as demonstrated below.

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/fe31e8f8bf43675d350176c9225a03188eba904f/img/Screenshot%202025-01-26%20112638.png)


Let’s now execute the following command to generate a list of all the modifications that Terraform will apply. —

```language
terraform plan

```

The list of changes that Terraform is anticipated to apply to the infrastructure resources should be displayed. The “+” sign indicates what will be added, while the “-” sign indicates what will be removed.

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/26c4a4b15f92e0f27684462a9da3d79491ba2d10/img/Screenshot%202025-01-26%20112828.png)


Now, let’s deploy this infrastructure! Execute the following command to apply the changes and deploy the resources.

Note — Make sure to type “yes” to agree to the changes after running this command

```langauage
terraform apply
```

Terraform will initiate the process of applying all the changes to the infrastructure. Kindly wait for a few seconds for the deployment process to complete.

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/a7f6d7adc2102008743b8e59e9b387fc2ad65c76/img/Screenshot%202025-01-26%20113217.png)


## Success!

The process should now conclude with a message indicating “Apply complete”, stating the total number of added, modified, and destroyed resources, accompanied by several resource outputs.

Please copy and save the Website URL, which will be required to access the web page from the browser.


![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/e1b3eb9a5950dea16725cc304f7a15e36660686a/img/Screenshot%202025-01-26%20113558.png) 







## Step 5: Verify creation of our website and the S3 bucket 

In your local web browser, paste that output website URL and verify that the website is working properly.

Note — Make sure to use the “http://” protocol and not https:// to reach the website.



```language
 http://tatenda-web-hosting-bucket.s3-website-us-east-1.amazonaws.com

```


![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/43d4c42e9923114e723374b4f40fb607d7349d4a/img/Screenshot%202025-01-26%20114413.png)




 ## AWS Console
 
In the AWS Management Console, Navigate to S3 and check if your bucket was successful created.



1.Bucket Section created successfully


![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/cf8b9f584be5ed9a120810750fbdec4528849a83/img/Screenshot%202025-01-26%20115222.png)


2.Static Website Hosting Section

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/e31fd980c1d6e92a9c39583be335ea16d64f5e87/img/Screenshot%202025-01-26%20115258.png)



2. Buckey Policy Section

![image_alt](https://github.com/Tatenda-Prince/Lauching-A-Website-Using-S3-With-Terraform/blob/c0d9c0dd1b4fa246a3a60bb3018cb638061a3d39/img/Screenshot%202025-01-26%20115315.png)


# Congratulations!


You’ve successfully completed “Terraforming with S3”. You’ve learned how to host a static webiste on amazon S3 using terraform for automation which makes it easy to build up and destroy aws infrastructure effectively. 

# Clean up 


## Destroy infrastructure

```language

terraform destroy

```

Wait for it to complete. At the end, you should receive a prompt stating Destroy complete along with how many resources were destroyed.









