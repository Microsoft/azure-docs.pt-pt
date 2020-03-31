---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184773"
---
1. No seu projeto de `AndroidManifest.xml` **aplicação,** abra o ficheiro. Adicione o seguinte `application` código após a etiqueta de abertura:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Abra o `ToDoActivity.java`ficheiro e faça as seguintes alterações:

    - Adicione a declaração de importação:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Mude a `MobileServiceClient` definição de estática **privada** para **privada,** por isso agora se parece com isto:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Adicionar `registerPush` método:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Atualizar o método **onCreate** da `ToDoActivity` classe. Certifique-se de adicionar `MobileServiceClient` este código depois de o instantâneo.

        ```java
        registerPush();
        ```

3. Adicione uma nova classe para lidar com notificações. No Project Explorer, abra a **app** > **java** > os seus nós**de espaço de nome do projeto** e clique no nó de nome do pacote. Clique em **Novo**e, em seguida, clique na **Classe Java.** Em Nome, `ToDoMessagingService`escreva, e, em seguida, clique OK. Em seguida, substitua a declaração de classe por:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Adicione outra classe para lidar com atualizações de token. Crie `ToDoInstanceIdService` a classe java e substitua a declaração de classe por:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

A sua aplicação está agora atualizada para suportar notificações push.
