# Resume Host on AWS EC2 with CI/CD Using GitHub Actions

This guide provides a structured approach to automate code deployment from GitHub to an AWS EC2 instance. With GitHub Actions, you can streamline the CI/CD process, ensuring that updates to your resume are instantly live with each commit.

## Prerequisites

- An AWS account
- Basic knowledge of Git and GitHub
- An existing GitHub repository for your resume

## Step 1: Create an EC2 Instance and Download the Key Pair

1. In the AWS Console, navigate to **EC2** and launch a new instance.
2. Choose an Amazon Machine Image (AMI) like Ubuntu, configure your instance settings, and select an instance type.
3. Under **Key pair (login)**, create a new key pair and download it (e.g., `resume_key.pem`). This will enable secure access to your EC2 instance.

## Step 2: Create Secrets in GitHub for the Repository

1. In your GitHub repository, go to **Settings** > **Secrets and variables** > **Actions** > **New repository secret**.
2. Add the following secrets:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `EC2_INSTANCE_IP`
   - `EC2_USER`

These secrets will be used in your workflow for secure access to the AWS instance.

## Step 3: Creating Your First Workflow

1. Inside your GitHub repository, navigate to `.github/workflows/` and create a new YAML file, e.g., `deploy.yml`.
2. Define your GitHub Actions workflow. Below is a sample workflow snippet to get you started:

   ```yaml
    name: Push-to-EC2

    # Trigger deployment only on push to main branch
    on:
      push:
        branches:
          - main
    
    jobs:
      deploy:
        name: Deploy to EC2 on master branch push
        runs-on: ubuntu-latest

    steps:
      - name: Checkout the files
        uses: actions/checkout@v2

      - name: Deploy to Server 1
        uses: easingthemes/ssh-deploy@main
        env:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          TARGET: ${{ secrets.TARGET_DIR }}

      - name: Executing remote ssh commands using ssh key
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST_DNS }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            sudo apt-get -y update
            sudo apt-get install -y apache2
            sudo systemctl start apache2
            sudo systemctl enable apache2
            cd home
            sudo mv * /var/www/html
   ```

## Step 4: Creating Your First Workflow

1. Commit changes to your GitHub repository to trigger the workflow.
2. Verify the deployment by accessing your EC2 instance's IP address to see if your resume is live.
