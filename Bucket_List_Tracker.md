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
- **NOTES:make sure bucketistapp is by itself in its own repo or it will not work**

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

This app will allow users to sign up using their email, and upon sign-up they will recieve a verification email.  

By default, the authentication resource is configured in the bucketlistapp/amplify/auth/resource.ts file. Keep the default authentication.

## Backend: Setup Amplify Data

In these steps, a schema will be defined for storing the bucket list items. Each user will be able to create, delete, and list their own bucket list entries, and Amplify will automatically handle the per-user ownership of the data

- **Step 1:** On your local machine, navigate to bucketlistapp/amplify/data/resource.ts file and update it with the following code
- **Step 2:** This code will define a model for the bucket list items, ensruing that only the owner can access their data
```ts
import { type ClientSchema, a, defineData } from '@aws-amplify/backend';


const schema = a.schema({
  BucketItem: a
    .model({
      title: a.string(),
      description: a.string(),
      image: a.string(),
    })
    .authorization((allow) => [allow.owner()]),  // Restrict access to the owner
});


export type Schema = ClientSchema<typeof schema>;


export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: 'userPool',
  },
});
```
- **NOTES: Each bucket list item includes a title, description, and a completed status; the authorization rule ensures that only the user who created the item can access it**

## Backend: Setup Amplify Storage

These steps will help set up a file storage to allow users to uppload images related to ther bucket list items (optional feature)

- **Step 1:** Create a new folder called storage inside the bucketlistapp/amplify folder, and inside that create a file named resource.ts
- **Step 2:** Update the resource.ts file with the following code to configure storage for images
```ts
import { defineStorage } from "@aws-amplify/backend";


export const storage = defineStorage({
  name: "amplifyBucketTrackerImages",
  access: (allow) => ({
    "media/{entity_id}/*": [
      allow.entity("identity").to(["read", "write", "delete"]),
    ],
  }),
});
```

This storage configuration ensures that only the person who uploads the image can access it. The enitiy_id will be replaced with the user's identifier during the file uploads, restricting access to the file.

## Backend: Finalize Backend Setup

Now the authentication, data, and storage resources in the Amplify backend configuration need to be linked.  

On the local machine, navigate to amplify/backend.ts file and update it with the following code
```ts
import { defineBackend } from '@aws-amplify/backend';
import { auth } from './auth/resource';
import { data } from './data/resource';
import { storage } from './storage/resource';


defineBackend({
  auth,
  data,
  storage,
});
```

This ensures that all the backend resources (auth, data, and storage) are properly configured and linked

## Deploy the Amplify Backend in a Cloud Sandbox

These steps will show you how todeploy your Amplify backend resources to a personal cloud sandbox environment, which allows you to rapidly build, test, and iterate on your app

- **Step 1:** In a new terminal, run the following command from your app's root folder
```bash
npx ampx sandbox
```
- **Step 2:** Once the sandbox has been fully deployed, you will see a confirmation message, and an amplify_outputs.json file will be generated and added to your project  

With these steps the bucket list tracker application now has a fully configured backend, including user authentication, data storage, and file storage

## Connecting Frontend and Backend

These steps will help you build the frontend of your bucket list tracker app and connect it to the cloud backend you have already set up.  

You will use AWS Amplify 's UI component library to create a complete user authentication flow and implement the ability to create, update, and delete bucket list items.

Additionally, you will create the frontend of the bucket list tracker, where users can add, update, and delete items on their bucket list. They will also be able to upload images associated with each item.

## Frontend and Backend: Install Amplify Libraries

Open a new terminal window, naviagte to the project folder (bucketlistapp) and run the following command to install the necessary Amplify libraries
```bash
npm install aws-amplify @aws-amplify/ui-react
```

## Frontend and Backend: UI Setup and Styling

- **Step 1:** On the local machine, go to bucketlistapp/src/index.css file
- **Step 2:**: Update it with the following code to style the bucket list UI
```css
:root {
  font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
  line-height: 1.5;
  font-weight: 400;
  color: rgba(255, 255, 255, 0.87);
  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
}


.card {
  padding: 2em;
}


.read-the-docs {
  color: #888;
}


.box:nth-child(3n + 1) {
  grid-column: 1;
}
.box:nth-child(3n + 2) {
  grid-column: 2;
}
.box:nth-child(3n + 3) {
  grid-column: 3;
}
```
- **NOTES: This will set the layout and styles for the bucket list UI making it responsive and visually appealing**
- **Step 3:** On the local machine, go to the bucketlistapp/src/App.jsx file
- **Step 4:** Update the code with this
```jsx
import { useState, useEffect } from "react";
import {
  Authenticator,
  Button,
  Text,
  TextField,
  Heading,
  Flex,
  View,
  Image,
  Grid,
  Divider,
} from "@aws-amplify/ui-react";
import { Amplify } from "aws-amplify";
import "@aws-amplify/ui-react/styles.css";
import { getUrl } from "aws-amplify/storage";
import { uploadData } from "aws-amplify/storage";
import { generateClient } from "aws-amplify/data";
import outputs from "../amplify_outputs.json";
/**
 * @type {import('aws-amplify/data').Client<import('../amplify/data/resource').Schema>}
 */


Amplify.configure(outputs);
const client = generateClient({
  authMode: "userPool",
});


export default function App() {
  const [items, setItems] = useState([]);


  useEffect(() => {
    fetchItems();
  }, []);


  async function fetchItems() {
    const { data: items } = await client.models.BucketItem.list();
    await Promise.all(
      items.map(async (item) => {
        if (item.image) {
          const linkToStorageFile = await getUrl({
            path: ({ identityId }) => `media/${identityId}/${item.image}`,
          });
          console.log(linkToStorageFile.url);
          item.image = linkToStorageFile.url;
        }
        return item;
      })
    );
    console.log(items);
    setItems(items);
  }


  async function createItem(event) {
    event.preventDefault();
    const form = new FormData(event.target);
    console.log(form.get("image").name);


    const { data: newItem } = await client.models.BucketItem.create({
      title: form.get("title"),
      description: form.get("description"),
      image: form.get("image").name,
    });


    console.log(newItem);
    if (newItem.image)
      await uploadData({
        path: ({ identityId }) => `media/${identityId}/${newItem.image}`,
        data: form.get("image"),
      }).result;


    fetchItems();
    event.target.reset();
  }


  async function deleteItem({ id }) {
    const toBeDeletedItem = {
      id: id,
    };


    const { data: deletedItem } = await client.models.BucketItem.delete(
      toBeDeletedItem
    );
    console.log(deletedItem);


    fetchItems();
  }


  return (
    <Authenticator>
      {({ signOut }) => (
        <Flex
          className="App"
          justifyContent="center"
          alignItems="center"
          direction="column"
          width="70%"
          margin="0 auto"
        >
          <Heading level={1}>My Bucket List</Heading>
          <View as="form" margin="3rem 0" onSubmit={createItem}>
            <Flex
              direction="column"
              justifyContent="center"
              gap="2rem"
              padding="2rem"
            >
              <TextField
                name="title"
                placeholder="Bucket List Item"
                label="Bucket List Item"
                labelHidden
                variation="quiet"
                required
              />
              <TextField
                name="description"
                placeholder="Description"
                label="Description"
                labelHidden
                variation="quiet"
                required
              />
              <View
                name="image"
                as="input"
                type="file"
                alignSelf={"end"}
                accept="image/png, image/jpeg"
              />


              <Button type="submit" variation="primary">
                Add to Bucket List
              </Button>
            </Flex>
          </View>
          <Divider />
          <Heading level={2}>My Bucket List Items</Heading>
          <Grid
            margin="3rem 0"
            autoFlow="column"
            justifyContent="center"
            gap="2rem"
            alignContent="center"
          >
            {items.map((item) => (
              <Flex
                key={item.id || item.title}
                direction="column"
                justifyContent="center"
                alignItems="center"
                gap="2rem"
                border="1px solid #ccc"
                padding="2rem"
                borderRadius="5%"
                className="box"
              >
                <View>
                  <Heading level="3">{item.title}</Heading>
                </View>
                <Text fontStyle="italic">{item.description}</Text>
                {item.image && (
                  <Image
                    src={item.image}
                    alt={`Visual for ${item.title}`}
                    style={{ width: 400 }}
                  />
                )}
                <Button
                  variation="destructive"
                  onClick={() => deleteItem(item)}
                >
                  Delete Item
                </Button>
              </Flex>
            ))}
          </Grid>
          <Button onClick={signOut}>Sign Out</Button>
        </Flex>
      )}
    </Authenticator>
  );
}
```

## Frontend and Backend: Launch App Locally

- **Step 1:** Open a terminal window, navigate to the root folder (bucketlistapp), and run the following command to launch the app
```bash
npm run dev
```
- **Step 2:** Open the local host link that appears in your terminal to view the application
- **Step 3:** Choose the "Create Account" tab and use the authentication flow to sign up by entering your email and password. Then, create your account
- **Step 4:** You will recieve a verification code via email. Enter the verification code to log in.
- **Step 5:** Once signed in, you can start adding items to your bucket list and managing them

## Frontend and Backend: Push the changes to GitHub

- **Step 1:** After making the changes to the app, push them to GitHub with the following commands
```git
git add .
git commit -m 'bucket list tracker app'
git push origin main
```
- **Step 2:** Sign in to the AWS Management Console, and open AWS Amplify Console
- **Step 3:** AWS Amplify will automatically build your source code and deploy your app at a URL like https://...amplifyapp.com. On every git push, Amplify will update your deployment.
- **Step 4:** Click on the "Visit the deployed URL" button to see your bucket list tracker live

## Conclusion and Clean-up

- **Step 1:** In the Amplify console, in the left-hand navigation for the bucketlistapp, choose App settings > General settings
- **Step 2:** Under General settings section, choose Delete app

## Final Thoughts

This was probably the coolest project and the most annoying project I have done so far. I got to see how AWS Amplify is used with GitHub, React, and I learned a bit about npm. Scaffolding or rather npm and node.js are both aggravating and helpful tools. The slightest cache error or wrong npm/node.js versioning is enough to break the app and cause errors that prevent me from going forward.  

There were so many errors that I ran in to especially with the node.js version being too new, and npm cache retaining old data and bad files even though I ran the clear cache command. Overall, I feel like I gota a lot of experience here with these tools and I think I am better prepped to deal with these tools issues in the future if needed.  

This could be a fun project to come back to and see what else I can do with it, but for now I will only look at turining it into Terraform code. I think this will be a headache, but am hopefuil that I will learn even more about Terraform. Here is hoping lol.