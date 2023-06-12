# 1.5 Extra: Firebase Deployment

> This is an optional exercise

## Things to Do

The main aim to create a backend is to deploy and use it. In order to show how it interacts with the frontend, we will be deployment the backend.

## Firebase

### Checkout to the Firebase branch (Required)

For this activity, you are required to checkout to the firebase branch.

```
git checkout checkpoint-firebase
```

## Setup

#### Set up Firebase

- [Create Google Account](https://accounts.google.com/v3/signin)
- [Set up Firebase CLI](https://firebase.google.com/docs/cli)

### CLI installation instructions

#### 1. Install Firebase CLI

Now we will be installing the Firebase CLI. This will be used to authentication and access the Firebase Firestore Database which is used in our project. It also allows you deploy the application onto Firebase Hosting.

Run the following instruction to install the Firebase command:

```
npm install -g firebase-tools
```

Next, ensure that you have signed in using your Google account by running this command:

```
firebase login
```

#### Add Firebase Private Key file to Project

1. Generate a private key file on Firebase: https://firebase.google.com/docs/admin/setup#initialize_the_sdk_in_non-google_environments

2. Rename it to `firestore-private-key.json`

3. Add to `src/config` folder

### Run Locally

Finally, to run this project locally, you may run the following command:

```
npm start
```

### Deployment

To deploy this project onto Firebase Hosting and make it accessible online, you may run the following command:

```
firebase deploy
```
