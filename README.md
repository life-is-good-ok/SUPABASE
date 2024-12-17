# Google Sign-In with Supabase in Flutter

This guide outlines the steps to configure **Google Sign-In** with Supabase in a Flutter project.

---

## Prerequisites

1. **Supabase Project**: [Create a project on Supabase](https://supabase.com/).
2. **Google Cloud Console Account**: Access to [Google Cloud Console](https://console.cloud.google.com/).
3. **Flutter SDK** installed.

---

## Steps

### 1. Enable Google Provider in Supabase

1. Go to the **Supabase Dashboard**.
2. Navigate to **Authentication** → **Providers** → **Google**.
3. Enable the **Google** provider.
4. You will need a **Client ID** and **Client Secret** from Google Cloud (next step).

---

### 2. Configure OAuth Credentials in Google Cloud

1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Navigate to:
   **APIs & Services** → **Credentials** → **Create Credentials** → **OAuth client ID**.
3. **Set up OAuth Consent Screen**:
   - User Type: **External**
   - Fill in app details (name, support email, etc.).
   - Add necessary scopes: `email`, `profile`, `openid`.

4. **Create OAuth Client**:
   - Application Type: **Web application**
   - Add the following Redirect URI:
     ```
     https://<YOUR-SUPABASE-PROJECT-URL>/auth/v1/callback
     ```
     Replace `<YOUR-SUPABASE-PROJECT-URL>` with your Supabase project URL (found in the Supabase Dashboard).

5. Copy the generated **Client ID** and **Client Secret**.

---

### 3. Add OAuth Credentials to Supabase

1. Go back to the **Supabase Dashboard**.
2. Paste the **Client ID** and **Client Secret** into the Google provider settings under **Authentication → Providers → Google**.
3. Save the configuration.

---

### 4. Set Up Flutter Project

1. Add dependencies to your Flutter project:

   ```bash
   flutter pub add supabase_flutter google_sign_in
   ```

2. Initialize Supabase in your app:

   ```dart
   import 'package:supabase_flutter/supabase_flutter.dart';

   Future<void> main() async {
     await Supabase.initialize(
       url: 'https://<YOUR-SUPABASE-PROJECT-URL>',
       anonKey: '<YOUR-SUPABASE-ANON-KEY>',
     );
     runApp(MyApp());
   }
   ```

   Replace `<YOUR-SUPABASE-PROJECT-URL>` and `<YOUR-SUPABASE-ANON-KEY>` with your Supabase project's credentials.

---

### 5. Implement Google Sign-In

Use the following code to trigger Google Sign-In:

```dart
import 'package:supabase_flutter/supabase_flutter.dart';

Future<void> signInWithGoogle() async {
  try {
    await Supabase.instance.client.auth.signInWithOAuth(
      Provider.google,
      redirectTo: 'com.example.app://callback',
    );
    print('Sign-in successful');
  } catch (e) {
    print('Error: $e');
  }
}
```

- Replace `com.example.app://callback` with your custom URI scheme (next step).

---

### 6. Add Custom URI Scheme for Android

To handle redirects on mobile devices:

1. Open `android/app/src/main/AndroidManifest.xml`.
2. Add the following intent filter within the `<activity>` tag:

   ```xml
   <intent-filter>
       <action android:name="android.intent.action.VIEW" />
       <category android:name="android.intent.category.DEFAULT" />
       <category android:name="android.intent.category.BROWSABLE" />
       <data android:scheme="com.example.app" android:host="callback" />
   </intent-filter>
   ```
   Replace `com.example.app` with your app's package name.

3. Use the same scheme in the `redirectTo` parameter in your Dart code.

---

### 7. Run and Test

1. Run your Flutter project:
   ```bash
   flutter clean
   flutter pub get
   flutter run
   ```
2. Trigger Google Sign-In and verify the user is authenticated in Supabase.

---

## Common Issues

- **Redirect URI Mismatch**: Ensure the URI in Google Cloud matches the Supabase callback URL.
- **Client ID/Secret Error**: Double-check the credentials from Google Cloud.
- **Custom URI Scheme**: Verify the scheme matches in both AndroidManifest and Dart code.

---

## References
- [Supabase Documentation](https://supabase.com/docs)
- [Google Cloud Console](https://console.cloud.google.com/)
- [Flutter OAuth Setup](https://pub.dev/packages/supabase_flutter)

---

### Done! 🚀
Your Flutter app should now support Google Sign-In using Supabase.
