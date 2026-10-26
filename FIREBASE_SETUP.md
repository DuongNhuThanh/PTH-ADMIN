# Firebase shared sync setup

The dashboard works locally until Firebase is configured. To share the latest Excel file, production data, and IPQC entries:

1. Create a Firebase project and register a Web app.
2. In **Authentication**, enable **Email/Password** sign-in. The dashboard sends verification links to new accounts.
3. Create a **Realtime Database** and copy its database URL.
4. Replace the placeholder values in `firebaseConfig` near the end of `index.html` with the Web app config from Firebase. The Firebase web config is public; never put a service-account key in this file.
5. In **Realtime Database > Rules**, publish the contents of `database.rules.json`.
6. Host the page on an authorized HTTPS domain. Add that domain under Firebase Authentication's authorized domains.
7. Users can create accounts from the dashboard and verify their email. In **Realtime Database**, an administrator must add each user's Firebase Authentication UID under `authorizedUsers` with the value `true`, for example:

   ```json
   {
     "authorizedUsers": {
       "FIREBASE_AUTH_UID": true
     }
   }
   ```

   Do not add a broad rule that grants access to every signed-in user. Accounts not listed in `authorizedUsers` cannot read or modify shared data.
8. After an authorized user signs in, the dashboard initializes the shared database with the built-in sample if it is empty. Existing browser-only data is not uploaded automatically; import the current Excel file once to publish it. The newest HTML-exported `.xls` file is stored at `data/latestExcel` in Realtime Database, with the parsed dashboard data and IPQC records synchronized alongside it. Every successful upload overwrites the same `data/latestExcel` entry: the previous file is replaced, no old Excel files are kept, and all connected users switch to the new version in real time. Use **Cập nhật dữ liệu** to explicitly fetch and apply the current latest file again. Files are limited to 5 MB.

The rules require both a verified email and an administrator-approved UID to read or write shared data and the latest Excel file. Until Firebase is configured, the page clearly indicates that data is saved on the current device only.
