---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060502"
---
### <a name="configure-required-android-packages"></a>Configurar pacotes Android necessários

O pacote está [automaticamente ligado ao](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) construir a aplicação. Tem alguns passos adicionais abaixo para completar o processo de configuração.

### <a name="configure-android-manifest"></a>Configure manifesto Android

No seu "android/app/src/main/AndroidManifest.xml", verifique o nome do pacote, permissões e serviços necessários. Certifique-se de que registou `RNPushNotificationPublisher` e `RNPushNotificationBootEventReceiver` recetores, e registou o `RNPushNotificationListenerService` serviço. Os metadados de notificações podem ser usados para personalizar o aparecimento de notificações push.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Configure os serviços do Google

No registo "android/app/build.gradle" o Google Services:

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Copie o ficheiro "google-services.json" que descarregou durante a configuração da FCM para a pasta do projeto "android/app/".

### <a name="handle-push-notifications-for-android"></a>Lidar com notificações push para Android

Configuraste o serviço existente `RNPushNotificationListenerService` para lidar com notificações push do Android recebidas. Este serviço foi registado anteriormente no manifesto de candidatura. Processa notificações recebidas e sugere-as à parte nativa react de plataforma cruzada. Não são necessários passos adicionais.
