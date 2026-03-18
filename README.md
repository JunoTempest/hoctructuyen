# hoctructuyen
Giải nén file lib trước, sau đó làm những bước sau
Cài đặt
1. Clone project
bashgit clone <repo-url>
cd hoctructuyen
2. Cài dependencies
bashflutter pub get
3. Sinh code (bắt buộc — freezed + riverpod)
bashflutter pub run build_runner build --delete-conflicting-outputs

Nếu có lỗi conflict, chạy build_runner clean trước rồi build lại.

Cấu hình Firebase
Project Firebase: hoctructuyen-b340a
Android
File google-services.json đặt tại:
android/app/google-services.json
iOS
File GoogleService-Info.plist đặt tại:
ios/Runner/GoogleService-Info.plist
Web
File firebase_options.dart đã có tại:
lib/core/constants/firebase_options.dart
(không cần thay đổi nếu dùng chung project)

Lấy file config: Firebase Console → Project Settings → Your apps → Download config file

Firestore Rules
Copy nội dung sau vào Firebase Console → Firestore → Rules:
jsrules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function isSignedIn() {
      return request.auth != null;
    }
    function isAdmin() {
      return isSignedIn() &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    match /users/{userId} {
      allow read:  if isSignedIn();
      allow write: if isSignedIn() && (request.auth.uid == userId || isAdmin());
    }
    match /courses/{courseId} {
      allow read:  if isSignedIn();
      allow write: if isSignedIn();
    }
    match /schedules/{scheduleId} {
      allow read, write: if isSignedIn();
    }
    match /bookings/{bookingId} {
      allow read, write: if isSignedIn();
    }
    match /sessions/{sessionId} {
      allow read, write: if isSignedIn();
      match /presence/{uid}   { allow read, write: if isSignedIn(); }
      match /strokes/{strokeId} { allow read, write: if isSignedIn(); }
      match /messages/{msgId}  { allow read, write: if isSignedIn(); }
    }
    match /chats/{chatId} {
      allow read, write: if isSignedIn();
      match /messages/{messageId} { allow read, write: if isSignedIn(); }
    }
    match /bug_reports/{reportId} {
      allow read, write: if isSignedIn();
    }
  }
}


Cấu hình Agora
File: lib/core/constants/app_constants.dart
dartstatic const agoraAppId = '8bac8b1b37d44976977cef0e7280e1cb';

Quan trọng: Vào Agora Console → project này → App Certificate → TẮT (để dùng token rỗng). Nếu bật App Certificate thì video call sẽ báo lỗi 101.

# cần cấp quyền video/mic/... cho ios/web/android/windows/... để có thể hoạt động phần mềm này tốt nhất. 
