# Amar Hisab — উন্নত HTML সংস্করণ

## কী আছে
- মোবাইল-ফার্স্ট নতুন UI, ডার্ক মোড
- SAR/BDT প্রদর্শন, মাস ফিল্টার, বেতন/খরচ, রেমিট্যান্স ও ধার-দেনা
- JSON ব্যাকআপ ডাউনলোড ও রিস্টোর
- লোকাল ডেটা `localStorage`-এ সংরক্ষণ
- Firebase ক্লাউড ব্যাকআপের জন্য প্রস্তুত UI/কনফিগারেশন স্থান

## গুরুত্বপূর্ণ সতর্কতা
এই ZIP-এ Firebase ক্লাউড সিঙ্ক সম্পূর্ণভাবে সক্রিয় নয়। বাস্তব অনলাইন ব্যাকআপ চালু করতে নিজের Firebase project-এর config, Email/Password Authentication, Firestore SDK handler এবং নিরাপদ Firestore rules যোগ করতে হবে। README-তে Firebase config বসালেই একা ক্লাউড সিঙ্ক কাজ করবে না। আর্থিক তথ্যের জন্য public read/write rules ব্যবহার করবেন না।

## Firebase অনলাইন ব্যাকআপ চালু করার ধাপ
1. Firebase Console-এ নতুন project তৈরি করুন।
2. Authentication → Sign-in method থেকে Email/Password চালু করুন।
3. Firestore Database তৈরি করুন।
4. Authentication-এ নিজের অ্যাকাউন্ট তৈরি করুন; অন্য কারও অ্যাকাউন্টে আপনার হিসাব রাখা উচিত নয়।
5. `index.html`-এর `FIREBASE_CONFIG`-এ Firebase Web App config বসান।
6. Firebase modular SDK (`firebase-app`, `firebase-auth`, `firebase-firestore`) যুক্ত করে `cloudSignIn()` এবং `cloudSync()`-এ `signInWithEmailAndPassword`/`createUserWithEmailAndPassword` ও `setDoc/getDoc`-এর বাস্তব implementation যোগ করুন।
7. Firestore rules-এ প্রতিটি user শুধু নিজের `users/{uid}` document পড়তে/লিখতে পারবে—এটি নিশ্চিত করুন। উদাহরণ নীতি:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```
8. `file://` দিয়ে নয়; Vercel বা অন্য HTTPS hosting-এ deploy করে পরীক্ষা করুন।
9. প্রথম ক্লাউড সিঙ্কের আগে JSON backup ডাউনলোড করে রাখুন এবং Firestore-এ কেবল নিজের authenticated UID-এর ডেটা রাখুন।

## পুরোনো ডেটা সম্পর্কে
HTML-এ আপনার দেওয়া শিটের প্রধান বেতন, খরচ, ৭টি রেমিট্যান্স ও ৮টি ধার-দেনার প্রাথমিক রেকর্ড রাখা হয়েছে। আগের HTML-এর `localStorage`-এর ডেটা নতুন সংস্করণে স্বয়ংক্রিয়ভাবে আমদানি হয় না। পুরোনো অ্যাপে ডেটা থাকলে আগে তার রিপোর্ট/JSON export করুন; তারপর নতুন সংস্করণে JSON restore যুক্ত করার আগে ব্যাকআপ কাঠামো মিলিয়ে নিন। কোনো পরিবর্তনের আগে ব্যাকআপ রাখুন।

## Deploy
`index.html`-কে Vercel project-এর root directory-তে রাখুন এবং deploy করুন। কোনো build command দরকার নেই। ক্লাউড ফিচার সম্পূর্ণ সক্রিয় করার আগে অ্যাপটিকে private রাখুন এবং security rules পরীক্ষা করুন.
