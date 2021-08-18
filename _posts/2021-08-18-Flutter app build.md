---
layout: post
title: "Flutter android 앱 빌드"

categories: [JavaScript]

date: 2021-08-18
last_modified_at: 2021-08-18

toc: true
toc_sticky: true
---
참고:<br/>
https://flutter-ko.dev/docs/deployment/android
https://firebase.google.com/support/guides/launch-checklist
https://developer.android.com/studio/build/shrink-code


<br/>

# 1. keytool
<script src="https://gist.github.com/tempJ/ef1f24a4ba736d058491591ca2b7b2f8.js"></script>
이 명령어로 한다는데, 여기서 좀 헤멨다.


```
Warning:
JKS 키 저장소는 고유 형식을 사용합니다. "keytool -importkeystore -srckeystore c:/Users/USER_NAME/key.jks -destkeystore c:/Users/USER_NAME/key.jks -deststoretype pkcs12"를 사용하는 산업 표준 형식인 PKCS12로 이전하는 것이 좋습니다.
```
Warning이 발생해서...처음에 Error로 오인하고 

<script src="https://gist.github.com/tempJ/5e026361ab927f3c33585473bcd4c22e.js"></script>

해봤는데 이번엔 `-genkey` 할 때마다 

```
keytool 오류: java.lang.Exception: 키 쌍이 생성되지 않았으며 <key> 별칭이 존재합니다.
java.lang.Exception: 키 쌍이 생성되지 않았으며 <key> 별칭이 존재합니다.
        at sun.security.tools.keytool.Main.doGenKeyPair(Unknown Source)
        at sun.security.tools.keytool.Main.doCommands(Unknown Source)
        at sun.security.tools.keytool.Main.run(Unknown Source)
        at sun.security.tools.keytool.Main.main(Unknown Source)
```

발생. 한참 헤메다가 일단 원래대로 되돌리자는 생각에 이것저것 시도하다,

```
Warning:
"c:/Users/USER_NAME/key.jks"을(를) Non JKS/JCEKS(으)로 이전했습니다. JKS 키 저장소가 "c:/Users/USER_NAME/key.jks.old"(으)로 백업되었습니다.
```

이동할 때 이 문구가 떠서 `key.jks`와 `key.jks.old`를 경로에서 지우고 다시 -genkey 명령어 사용해봤더니 된다. 이후 서명이나 빌드 해보니까 PKCS12 이전 Warning은 무시해도 build, install에는 문제가 없음.

<br/>

# 2. keystore 참조
1. `ROJECT_ROOT/android/key.properties` 생성
2. <script src="https://gist.github.com/tempJ/30f52f93dd59f1ad57d73da84c1a57e8.js"></script>
딱 저 말 그대로 입력하면 끝. `keyAlias=key`를 제외하고, 각자 설정했던 keystore의 비밀번호, key의 비밀번호, key.jks가 위치한 경로를 적어주면 된다. `keyAlias=key`는 그대로 둘 것.

<br/>

# 3. Gradle `PROJECT_ROOT/android/app/build.gradle`
1. **앱 서명을 위한 파일 수정**
<br/>
폴더에 보면 아래 같은 부분이 있을텐데,
<script src="https://gist.github.com/tempJ/1bb5438fea6b6f62259d254beca7d87e.js"></script>
이 부분 바로 위에다(android 안 아님)
<br/>
<script src="https://gist.github.com/tempJ/bb91d4dcb8a87b319751e1ae6276a450.js"></script>
를 추가한다. android 안쪽에는 보면 buildTypes가 있을텐데,
<script src="https://gist.github.com/tempJ/30ed19349e3484007053d0b787a20d66.js"></script>
위를 아래와 같이 변경한다.
<script src="https://gist.github.com/tempJ/4f1468095cd92c779e718e4c6f47ec9b.js"></script>
2. **난독화와 축소를 위한 파일 수정**
<script src="https://gist.github.com/tempJ/cc0131465df175ef19d52c4900d1bdef.js"></script>
딱 저대로 추가하면 된다. 물론 이걸 위해서는 `proguard-rules.pro`의 작성이 필요하다. 이 외에도 다른 최적화를 위한 옵션 변경이 있는 것 같은데, 자세히 알아봐야 할 듯. 앱 빌드 할 때 `shrinkResources true`를 추가로 작성해보기는 했다.

<br/>

# 4. Proguard
1. `PROJECT_ROOT/android/app/proguard-rules.pro` 생성
2. 아래와 같은 규칙 추가
<script src="https://gist.github.com/tempJ/58beb430d3829fbc4e3f15fab256a74e.js"></script>

<br/>

# 5. Build
- 앱 번들로 빌드: `flutter build appbundle`
- apk로 빌드: `flutter build apk --split-per-abi`
- 빌드된 경로(aab, apk 등이 있는 경로)는 build 후 console창에 표시된다. 

<br/>

# 6. Install
`flutter install`을 통한 설치가 가능하다고는 하지만, 일반적으로 앱 설치하듯 apk만 옮겨서 바로 설치해도 무방하다.