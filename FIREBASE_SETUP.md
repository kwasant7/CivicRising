# Firebase Setup Guide for CivicRising Events

This guide will help you set up Firebase Firestore for storing event data.

## Step 1: Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project" or "Create a project"
3. Enter project name: `CivicRising` (or your preferred name)
4. Follow the setup wizard (you can disable Google Analytics if you don't need it)
5. Click "Create project"

## Step 2: Register Your Web App

1. In your Firebase project dashboard, click the **Web icon** (`</>`) to add a web app
2. Enter app nickname: `CivicRising Web`
3. Check "Also set up Firebase Hosting" (optional)
4. Click "Register app"
5. **Copy the configuration object** - you'll need this in the next step

The configuration will look like this:
```javascript
const firebaseConfig = {
  apiKey: "AIzaSyXxxx...",
  authDomain: "your-project.firebaseapp.com",
  databaseURL: "https://your-project-default-rtdb.firebaseio.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:xxx"
};
```

## Step 3: Enable Firestore Database

1. In the Firebase Console, go to **Build** > **Firestore Database**
2. Click "Create database"
3. Choose **Start in test mode** for development (you can change security rules later)
4. Select a Firestore location (e.g., `us-central` or closest to your users)
5. Click "Enable"

## Step 4: Update Your Configuration File

1. Open `firebase-config.js` in your project
2. Replace the placeholder values with your actual Firebase configuration:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_ACTUAL_API_KEY",
    authDomain: "your-project.firebaseapp.com",
    projectId: "your-project-id",
    storageBucket: "your-project.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

**Note:** You don't need `databaseURL` for Firestore (only needed for Realtime Database).

## Step 5: Configure Security Rules (Important!)

For development/testing, Firestore starts in test mode with open access. For production, update the rules:

1. Go to **Firestore Database** > **Rules** tab
2. Replace with these rules for basic security:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /events/{eventId} {
      allow read: if true;
      allow write: if true;
    }
  }
}
```

For better security (recommended for production):
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /events/{eventId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```

This allows anyone to read events but only authenticated users to create/update/delete events.

## Step 6: Test Your Setup

1. Open `community-events.html` in a browser
2. Try adding a new event
3. Check Firebase Console > Firestore Database to see if the data appears
4. The data should be stored in the `events` collection

## Step 7: Deploy to GitHub Pages

1. Commit your changes:
```bash
git add .
git commit -m "Add Firebase integration for events"
git push
```

2. Your events will now be stored in Firebase and accessible across all devices!

## Troubleshooting

### Error: "Firebase is not defined"
- Make sure the Firebase SDK scripts are loaded before `firebase-config.js`
- Check the browser console for errors

### Error: "Permission denied"
- Check your Firestore Database security rules
- Make sure the rules allow read/write access

### Events not syncing
- Check your internet connection
- Verify the `projectId` in `firebase-config.js` is correct
- Open browser developer tools and check the Console for errors

## Data Structure

Events are stored in Firestore with this structure:
```
events (collection)
  ├── 1234567890 (document)
  │   ├── id: "1234567890"
  │   ├── title: "Event Title"
  │   ├── date: "2025-11-15"
  │   ├── time: "18:00"
  │   ├── location: "Location Name"
  │   ├── description: "Event description"
  │   ├── category: "Advocacy"
  │   ├── createdAt: Timestamp
  │   └── updatedAt: Timestamp
  └── 1234567891 (document)
      └── ...
```

## Benefits of Firebase Firestore

- **Real-time sync**: Events update instantly across all devices with onSnapshot listeners
- **No server needed**: Firebase handles all backend infrastructure
- **Free tier**: Generous free usage limits (50K reads/day, 20K writes/day)
- **Reliable**: Google's infrastructure ensures 99.95% uptime
- **Scalable**: Automatically scales from zero to millions of users
- **Offline support**: Works offline and syncs when connection is restored
- **Advanced queries**: Supports compound queries and indexes

## Firestore vs Realtime Database

This project uses **Firestore** (not Realtime Database) because:
- Better scalability and performance
- More powerful querying capabilities
- Better structured data with collections and documents
- Automatic indexing
- Better pricing for most use cases

## Next Steps

Consider adding:
- User authentication (Firebase Auth) to secure write operations
- Image uploads (Firebase Storage) for event photos
- Event notifications (Firebase Cloud Messaging)
- Analytics (Firebase Analytics) to track event views
- Event registration with user tracking
