# Bucket List Translator

This project will be building a chatbot that will translate words or sentences into another language.

## Steps to be Performed

1. Develop a bucket list tracker
2. Initialize a Gihtub repository and connect it to your local repository. Host the frontend on Amplify Hosting
3. Use Amplify Studio/ Amplify CLI and integrate Amplify Authentication providing user authentication for Login/Signup
4. Create a AWS AppSync service for building and managing a GraphQL API, and a GraphQL schema for DynamoDB service integration
5. Deploy the backend on AWS Amplify to handle data storage and server-side logic

## Services that will be used

- AWS Amplify: deployment of frontend and backend services
- AWS AppSync: simplifies building and managing scalable GraphQL APIs
- GraphQL API: allows clients to request only the data the need (API & Schema)
- DynamoDB: DynamoDB is for storing and managing bucket list items (databases)
- S3 Bucket: for storage of user images (storage)

## Set up Amplify Frontend

AWS Amplify provides a Git-based CI/CD workflow that allows you to build, deploy, and host single-page web applications or static sites with serverless backends. It automatically detects the buuild settings for both the frontend and any serverless backend resources when connected to a Git repository. WIth each code commit, Amplify redeploys updates automatically. These steps will help me create a React application, push it to GitHub, and connect it to AWS Amplify for deployment.

## Frontend: Create a React App

- **Step 1:** Make sure nodejs is installed and Execution Policy is set to RemoteSigned and run these commands; a script downloaded will not run if Execution Policy is not set to RemoteSigned or a Bypass is set
```bash
npm create vite@latest bucketlistapp -- --template react

cd bucketlistapp

npm install

npm run dev
```
- **Step 2:** Click the local link provided and it will load the Vite + React application in your browser

## Frontend: Initialize a repository in GitHub

- **Step 1:** Create the repository in GitHub and then use the commands below in the root folder of your local computer

Add the root of the folder for this application to GitHub using these Git commands.
```git
git init
git add .
git commit -m "initial commit"
git remote add origin git@github.com:<your-username>/bucketlistapp.git
git branch -M main
git push -u origin main
```

## Frontend: Install the Amplify Packages

- **Step 1:** Run the following command in the terminal from the app's root folder.
  - **Notes: this will scaffold (automated generation of a project's initial structure, files, and configuration) a lightweight Amplify project in your app directory**
```bash
npm create amplify@latest -y
```
- **Step 2:** Push these change to GitHub using the following commands:
```git
git add . 
git commit -m "installing amplify" 
git push origin main
```

## Frontend: Deploy the App to AWS Amplify

These steps will help you connect your GitHub repository to AWS Amplify, which will enable you to build, deploy, and host the bucket list tracker app

- **Step 1:** Sign in to AWS Management console and go to AWS Amplify
- **Step 2:** Click Create a new App
- **Step 3:** On the Start building with Amplify page, select Github for "Deploy your app" and click next
- **Step 4:** Authenticate with GitHub when prompted, and select the repository and branch (main) created earlier. Click Next.
- **Step 5:** In the service role section click on "Create and use a new service role". Keep the rest as default and click Next
- **Step 6:** Review selections and click "Save and Deploy"

Wait for AWS Amplify to create and deploy the app, and then you can check the website by clicking on the URL given in the Domain section. With this AWS Amplify will now build your source code and deploy your app to a URL. Every time code is pushed to GitHub, Amplify will trigger a new deployment.

## Set up Amplify Backend

AWS Amplify will be used to provision a cloud backend for the bucket list tracker application. This includes setting up authentication, data storage, and file storage which will allow users to manage their buckets lists.

## Backend: Setup Amplify Authentication

