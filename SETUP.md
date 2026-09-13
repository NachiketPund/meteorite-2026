# METEORITE 2026 — Real Payment Screenshot + Admin Verification

This version is wired for Firebase. The only things you must add are your Firebase project configuration and official payment details.

## 1. Create Firebase project
Create a Firebase project and add a Web App.

Enable:
- Authentication → Email/Password
- Authentication → Anonymous
- Firestore Database
- Storage

## 2. Add Firebase config
In BOTH `index.html` and `admin.html`, replace:
YOUR_API_KEY
YOUR_PROJECT
YOUR_PROJECT_ID
YOUR_SENDER_ID
YOUR_APP_ID

with the Firebase Web App config.

## 3. Create the organizer admin
In Firebase Authentication, create the organizer's email/password account.

Copy that account's UID.

In Firestore create:
Collection: `admins`
Document ID: `<organizer UID>`

You can leave the document empty.

This is important: the admin dashboard is not protected by a password hard-coded into the website. Firebase Authentication + the `admins` document + security rules control access.

## 4. Publish security rules
Copy the Firestore rules and Storage rules from `firebase-rules.txt` into the Firebase console.

The rules ensure:
- participants can create only their own pending registration;
- participants cannot read other registrations;
- payment screenshots are not publicly readable;
- only an authorized admin can read screenshots;
- only an authorized admin can change `pending` to `confirmed`/`rejected`.

## 5. Participant flow
Participant:
1. Opens an event.
2. Clicks Register.
3. Enters name, college, department, mobile and email.
4. Selects event.
5. Pays using the official payment QR/UPI.
6. Uploads the payment screenshot.
7. Submits.

The registration is stored in Firestore and the screenshot is stored in Firebase Storage.

Initial status = `pending`.

## 6. Admin flow
Organizer opens `admin.html`:
1. Signs in with organizer Firebase account.
2. Sees registration list.
3. Opens payment screenshot.
4. Checks payment reference/proof.
5. Clicks Confirm or Reject.

Status is then stored as:
- `pending`
- `confirmed`
- `rejected`

## 7. Payment QR / fee
The registration form now shows the two payment QR codes and per-event fees taken from the organizer's Google Form (PhonePe — Kapadne Sharvil Manoj, and Google Pay — Aayush Tapse, UPI aayushtapse05@oksbi). Both are shown as accepted methods; the participant can pay with either.

The QR images live in `assets/qr-phonepe.png` and `assets/qr-googlepay.png`. Before publishing:
- Confirm both QR codes/UPI IDs are still the correct ones to use for this event.
- Double check each event's fee in `index.html` (search for the `events` array) against your latest pricing.
- "Quiz Competition" was added to Day 18 with a placeholder venue ("Venue TBA") since it wasn't on the original poster — update its day/venue if needed.

The selected event's fee is now shown automatically in the registration form.

## 8. Deploy
You can deploy the files using Firebase Hosting or another HTTPS host.

Recommended final files:
- index.html
- admin.html
- meteorite-poster.jpg
- firebase-rules.txt

## Important
Do not make the Firebase Storage bucket public just to make screenshots easier to view. The admin dashboard should obtain temporary authenticated download access through Firebase Storage rules.
