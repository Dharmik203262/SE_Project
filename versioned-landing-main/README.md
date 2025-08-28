# Versioned - Version Control Application

Versioned is a modern web application for version control that allows users to create and manage repositories, upload files, and track versions.

## Firebase Setup Guide

For the application to work properly, you need to set up Firebase correctly:

### 1. Create Firebase Project

1. Go to the [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project" and follow the setup wizard
3. Enable Google Analytics if desired

### 2. Configure Firebase Services

#### Firestore Database
1. In Firebase Console, go to "Firestore Database"
2. Click "Create database"
3. Choose either "Production mode" or "Test mode" (Test mode is easier for development)
4. Select a location closest to your users
5. Set up security rules in the "Rules" tab:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Allow authenticated users to read and write their own repositories
    match /repositories/{repositoryId} {
      allow create: if request.auth != null;
      allow read, update, delete: if request.auth != null && (resource.data.ownerId == request.auth.uid || 
                                    request.auth.uid in resource.data.collaborators);
    }
    
    // Allow authenticated users to read and write their own user profiles
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

#### Firebase Storage
1. In Firebase Console, go to "Storage"
2. Click "Get started" and follow the setup wizard
3. Choose a location closest to your users
4. Set up security rules in the "Rules" tab:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /repositories/{repositoryId}/{allPaths=**} {
      allow read: if request.auth != null;
      allow write: if request.auth != null;
    }
  }
}
```

#### Authentication
1. In Firebase Console, go to "Authentication"
2. Click "Get started"
3. Enable the Email/Password provider and Google provider
4. In "Settings" > "Authorized domains", add your deployment domain (e.g., localhost for development)

### 3. Configure Firebase in Your Application

1. In Firebase Console, go to Project settings (gear icon)
2. Under "Your apps", click the web app icon (</>) to register a web app if you haven't already
3. Copy the Firebase configuration object
4. Create a `.env` file in the root of your project with the following values:

```
VITE_FIREBASE_API_KEY=your_api_key
VITE_FIREBASE_AUTH_DOMAIN=your_auth_domain
VITE_FIREBASE_PROJECT_ID=your_project_id
VITE_FIREBASE_STORAGE_BUCKET=your_storage_bucket
VITE_FIREBASE_MESSAGING_SENDER_ID=your_messaging_sender_id
VITE_FIREBASE_APP_ID=your_app_id
```

### 4. Troubleshooting Repository Creation

If you can create a repository but can't navigate to its details page, check the following:

1. Open the browser console (F12) for error messages
2. Verify the repository was created in Firestore by checking the "repositories" collection
3. Ensure the route `/repository/:id` is correctly configured in your app
4. Check that the repository ID is being properly passed to the navigation function

## Getting Started with Development

```bash
# Install dependencies
npm install

# Start development server
npm run dev
```

## Building for Production

```bash
# Build for production
npm run build

# Preview production build
npm run preview
```

## Features

- User authentication (sign up, login, profile management)
- Repository creation and management
- File uploads and version tracking
- Collaboration features
- Public/private repository settings
