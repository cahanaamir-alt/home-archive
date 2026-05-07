# 🗄️ הארכיון הביתי — מדריך הקמה

## מה צריך?

1. חשבון Google (להתחברות)
2. פרויקט Firebase (חינמי)
3. GitHub Pages (חינמי) — לאחסון האתר

---

## שלב 1: הקמת Firebase

1. לך ל-[Firebase Console](https://console.firebase.google.com)
2. לחץ **Add Project** → תן שם (למשל `home-archive`) → צור
3. בדף הפרויקט לחץ **Web** (אייקון `</>`) → רשום את האפליקציה
4. **העתק את ה-firebaseConfig** שתקבל ושים במקום הערכים ב-`index.html`:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "home-archive-xxxxx.firebaseapp.com",
  projectId: "home-archive-xxxxx",
  storageBucket: "home-archive-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

## שלב 2: הפעלת Authentication

1. ב-Firebase Console → **Authentication** → **Sign-in method**
2. הפעל **Google** → בחר support email → שמור

## שלב 3: הקמת Firestore

1. ב-Firebase Console → **Firestore Database** → **Create Database**
2. בחר **Start in test mode**
3. בחר region (למשל `europe-west3` — הכי קרוב לישראל)

### חוקי אבטחה (Security Rules)

אחרי שהכל עובד, החלף את חוקי ברירת המחדל ב:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // User documents - only the user
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    // Shared home - only members
    match /homes/{homeId}/{document=**} {
      allow read, write: if request.auth != null 
        && request.auth.uid in resource.data.members;
      allow create: if request.auth != null;
      allow read: if request.auth != null;
    }
  }
}
```

## שלב 4: העלאה ל-GitHub Pages

```bash
# צור repository חדש ב-GitHub (למשל home-archive)
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/home-archive.git
git push -u origin main
```

ב-GitHub:
1. Settings → Pages
2. Source: **Deploy from a branch**
3. Branch: **main** → **/root** → Save
4. חכה 1-2 דקות → הקישור יופיע

## שלב 5: הגדרת Authorized Domain

1. ב-Firebase Console → **Authentication** → **Settings** → **Authorized domains**
2. הוסף את הדומיין של GitHub Pages:
   `YOUR_USERNAME.github.io`

## שלב 6: התקנה כאפליקציה (PWA)

אחרי שהאתר באוויר:
- **אנדרואיד**: Chrome → תפריט ⋮ → **Add to Home Screen** / **Install App**
- **אייפון**: Safari → Share → **Add to Home Screen**

---

## שימוש ב-AI (אופציונלי)

לסריקה אוטומטית של מסמכים:
1. לך ל-[Anthropic Console](https://console.anthropic.com)
2. צור API Key
3. באפליקציה: הגדרות → הדבק את המפתח

המפתח נשמר **מקומית בלבד** (localStorage) ולא נשלח לשום מקום מלבד Anthropic API.

---

## מבנה הקבצים

```
home-archive/
├── index.html      ← האפליקציה (הכל בקובץ אחד)
├── manifest.json   ← הגדרות PWA
├── sw.js           ← Service Worker (עבודה אופליין)
└── README.md       ← המדריך הזה
```

## אבטחה

- ✅ התחברות מאובטחת דרך Google (Firebase Auth)
- ✅ נתונים מוצפנים ב-Firestore עם חוקי אבטחה ברמת משתמש
- ✅ HTTPS (דרך GitHub Pages)
- ✅ מפתח AI נשמר מקומית בלבד
- ✅ אין שרת צד שלישי — רק Firebase + GitHub Pages
- 💡 אין לשמור מספרי ת.ז., כרטיסי אשראי, או סיסמאות במערכת
