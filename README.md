# **React App Deployment with Google Cloud Run**

## Things you will Learn 🤯

1. How to create a basic react application
2. Learn Docker and How to containerize a React application
    1. Creating Dockerfile
    2. Building DockerImage
    3. Running Docker Container
    4. Docker Commands
3. Build and Run Cloud Run

## **Prerequisites** !

(Things to have before starting the projects)

- [x]  Docker.
- [x]  Google Cloud SDK.
- [x]  Node.js and npx.
- [x]  Google Cloud Project.

# ✨Let’s Start the Project ✨

## **Part 1: Deploying the React application locally**

### **Step 1: Create a React App**

To begin, create a new React app using the create-react-app command:

```
npx create-react-app yourProjectName
cd yourProjectName
npm start
```
Please make sure the app runs without errors by visiting http://localhost:3000.

## **Part 2: Dockerizing the React application**

### **Step 1: Create a Dockerfile**

Next, you'll need a **Dockerfile** to containerize the React app. Here is a simple configuration for serving the React app using serve, a static file server, which supports client-side routing.

```
# Use the official Node.js image as the base image for building the React app
FROM node:20 AS build

# Set the working directory inside the container to /app
WORKDIR /app

# Copy the TypeScript configuration file to the working directory
COPY tsconfig.json ./

# Copy the package.json and package-lock.json files to the working directory
COPY package*.json ./

# Install the application dependencies defined in package.json
RUN npm install

# Copy the rest of the application source code into the container
COPY . .

# Build the React application for production
RUN npm run build

# Use the official Nginx image as the base image for serving the built app
FROM nginx:alpine

# Copy the built React app from the previous stage to the nginx HTML directory
COPY --from=build /app/build /usr/share/nginx/html

# Expose port 80 to allow external access to the application
EXPOSE 80

# Start Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```
## **Part 3: Build and Deploy the Container in Google Cloud Run**
### **Step 1: Build the Docker image**

Use Google Cloud Build to build the container image and push it to Google Container Registry.

```
gcloud builds submit --tag gcr.io/yourProjectId/react-app
```
Replace yourProjectId with your actual Google Cloud project ID.

### **Step 2: Deploy the Container on Cloud Run**

After successfully building the container, deploy it to Google Cloud Run.

```
gcloud run deploy --image gcr.io/yourProjectId/react-app --platform managed
```
You'll be prompted to choose a region and configure some other options. After deployment, you'll receive a service URL similar to:

```
Service [react-app] revision [react-app-00001-luv] has been deployed and is serving 100 percent of traffic at:
https://react-app-wtjtziv4wq-an.a.run.app
```

This will start the Flask server in a Docker container on **`localhost:5000`**. Navigate to [http://localhost:5000/](http://localhost:5000/) on your browser to access the application.

## **Part 4: Access Your App**

Visit the provided URL, and you should see your React app live on the internet! Google Cloud Run will handle scaling and provisioning automatically.

## **Additional Resources**

React Deployment Documentation [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started)

Google Cloud Run Documentation [Cloud Run Documentation](https://cloud.google.com/run/docs)


