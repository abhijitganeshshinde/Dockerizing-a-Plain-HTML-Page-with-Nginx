# Dockerizing a Plain HTML Page with Nginx

## Introduction

This repository contains instructions and files to create a Docker image serving a simple HTML page via Nginx and push it to Amazon ECR.

## Prerequisites

Before starting, ensure you have the following:

- Docker installed on your EC2 instance
- AWS CLI configured with access to an ECR repository
- ECR Repository created

## Steps

### 1. Create an EC2 Instance

- Launch an EC2 instance.
- Install Docker and AWS CLI:

    ```bash
    sudo apt update
    sudo apt install docker.io 
    sudo docker --version
    sudo apt install awscli
    ```

### 2. Create Access Key
- Generate an access key in the AWS IAM Console:
    - Go to IAM in the AWS Management Console.
    - Access "Users" and choose the user.
    - In the "Security credentials" tab, create an access key.

### 3. Configure awscli
- Once the key is generated, configure the AWS CLI:
    ```bash
    sudo aws configure
    ```

### 4. Create an ECR Repository
- Navigate to the Amazon ECR service and create a new public repository.

## Steps to Build and Push Docker Image

#### 1. Create HTML, Nginx Configuration Files, and Dockerfile
   
- Create three essential files for the Docker image:

    - index.html: Plain HTML page.
        The `index.html` file is a basic HTML file served by the Nginx web server.
        It is copied into the container's `/usr/share/nginx/html/` directory and will be displayed when accessing the server.

     - nginx.conf: Nginx server configuration.
        
        ```bash
            events {}

            http {
                server {
                    listen       80;
                    server_name  localhost;

                    location / {
                        root   /usr/share/nginx/html;
                        index  index.html;
                    }
                }
            }
        ```
        The `nginx.conf` file contains Nginx server configuration settings.
        It specifies the server to listen on port 80, defines the server name as `localhost`, and sets up the location of the HTML content to serve.

    - Dockerfile: File to build the Docker image.
    
        ```bash
            FROM nginx

            COPY nginx.conf /etc/nginx/nginx.conf
            COPY index.html /usr/share/nginx/html/

            EXPOSE 80
        ```
        The Dockerfile contains instructions to build the Docker image.
        It starts from the official Nginx image, copies the custom `nginx.conf` configuration file, and the `index.html` file into the appropriate directories within the container. Additionally, it exposes port 80 to allow external access.

    Refer to the provided index.html, nginx.conf, and Dockerfile in this repository as examples.

#### 2. Build Docker Image and Verify:
   
- Navigate to the directory containing the files and execute:

    ```bash
        sudo docker build -t {imagename} .
        sudo docker images
    ```
    
#### 3. Run and Verify the Build Locally

```bash
    sudo docker run -d -p 80:80 {imagename}
    sudo docker ps 
```
Check the public URL of the EC2 instance to verify if the HTML page is accessible.

#### 4. Push Image to Amazon ECR
- Ensure the AWS CLI has necessary permissions to access ECR:
    1. Navigate to the Amazon ECR service and select your repository. Click on the "View push commands" button.

    2. Follow these provided commands:

        ```bash
        aws ecr-public get-login-password --region us-east-1 |sudo docker login --username AWS --password-stdin public.ecr.aws/xxxxxxx
    
        docker tag {imagesname}:{tag} public.ecr.aws/xxxxxxx/{imagesname}:{tag}
    
        docker push public.ecr.aws/xxxxxxx/{imagesname}:{tag}
        ```
        Replace `{imagesname}` and `{tag}` with your image name and desired tag.

    3. Access your image in the Amazon ECR Public Gallery:

        - After pushing your image to Amazon ECR, go to the Amazon ECR Public service, then click on "View public listing" to access the Amazon ECR Public Gallery.

        - Once in the gallery, you'll find your Image URI, for example:
        public.ecr.aws/xxxxxxx/sampleplainhtml-nginx:latest

        This URI uniquely identifies your pushed image within the Amazon ECR Public Gallery and allows others to access and utilize your container image.



#### 5. Conclusion

You've successfully Dockerized a plain HTML page using Nginx, built a Docker image, verified it locally, and pushed it to Amazon ECR. Now, your HTML content is accessible through the ECR repository URL.

Feel free to adapt these instructions to suit your specific setup or requirements. For further customization or enhancements, modify the HTML content or Nginx configuration as needed.

For any issues or inquiries, refer to Docker's and AWS's official documentation or seek assistance from the respective communities.

---

**Note:** Ensure proper security measures are taken before exposing any services or pushing images to public repositories.
