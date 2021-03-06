---
title: Envie notificações para dispositivos Android específicos usando hubs de notificação do Azure e mensagens Google Cloud | Microsoft Docs
description: Saiba como utilizar os Hubs de Notificação para enviar notificações push para dispositivos Android específicos através dos Hubs de Notificação do Microsoft Azure e do Google Cloud Messaging.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c0c0018ac3007f77da820b9b0cecbb69c68bef31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92308306"
---
# <a name="tutorial-send-push-notifications-to-specific-android-devices-using-google-cloud-messaging-deprecated"></a>Tutorial: Enviar notificações push para dispositivos Android específicos usando mensagens Google Cloud (depreciadas)

> [!WARNING]
> A partir de 10 de abril de 2018, a Google depreifou o Google Cloud Messaging (GCM). O servidor GCM e as APIs do cliente são depreciadas e serão removidas logo a 29 de maio de 2019. Para obter mais informações, consulte [GCM e FCM Perguntas Frequentes.](https://developers.google.com/cloud-messaging/faq)

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição Geral

Este tutorial mostra como utilizar os Hubs de Notificação do Microsoft Azure para transmitir notificações de notícias de última hora para uma aplicação Android. Quando terminar, poderá registar-se para obter as categorias de notícias de última hora nas quais tem interesse e receber apenas notificações push para essas categorias. Este é um cenário com um padrão comum para muitas aplicações em que as notificações têm de ser enviadas para grupos de utilizadores que mostraram anteriormente interesse nas mesmas, por exemplo, leitor de RSS, aplicações para fãs de música, etc.

Os cenários de transmissão são ativados ao incluir uma ou mais *etiquetas* durante a criação de um registo no Hub de Notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos que foram registados para a etiqueta recebem a notificação. Como as etiquetas são simples cadeias, não têm de ser aprovisionadas com antecedência. Para obter mais informações sobre etiquetas, consulte [Encaminhamento de Hubs de Notificação e Expressões de Etiqueta](notification-hubs-tags-segment-push-message.md).

Neste tutorial, irá realizar as seguintes ações:

> [!div class="checklist"]
> * Adicionar a seleção de categorias à aplicação móvel.
> * Registrado para notificações com etiquetas.
> * Enviar notificações com etiquetas.
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial baseia-se na aplicação que criou em [Tutorial: Enviar notificações push para dispositivos Android através dos Hubs de Notificação do Microsoft Azure e do Google Cloud Messaging][get-started]. Antes de iniciar este tutorial, conclua o [Tutorial: Enviar notificações push para dispositivos Android através dos Hubs de Notificação do Microsoft Azure e do Google Cloud Messaging][get-started].

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

O primeiro passo consiste em adicionar os elementos de IU à sua atividade principal existente, para permitir que o utilizador selecione categorias a registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação é iniciada, é criado um registo do dispositivo no seu hub de notificação com as categorias selecionadas como etiquetas.

1. Abra o `res/layout/activity_main.xml file` , e substitua o conteúdo pelo seguinte:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Abra o `res/values/strings.xml` ficheiro e adicione as seguintes linhas:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    O seu `main_activity.xml` layout gráfico deve parecer-se com a seguinte imagem:

    ![Screenshot de um ambiente de desenvolvimento, com um ecrã de aplicativo visível. A aplicação lista as categorias de notícias que foram adicionadas ao código.][A1]
3. Crie uma aula `Notifications` no mesmo pacote que a sua `MainActivity` turma.

    ```java
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName,
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM,
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```

    Esta classe utiliza o armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Contém também métodos para se registar nestas categorias.
4. Na sua `MainActivity` classe remova os seus campos privados para `NotificationHub` `GoogleCloudMessaging` e, e adicione um campo `Notifications` para:

    ```java
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. Em seguida, no `onCreate` método, remova a inicialização do `hub` campo e do `registerWithNotificationHubs` método. Em seguida, adicione as seguintes linhas, que inicializam um exemplo da `Notifications` classe.

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Confirme se o nome do hub e a cadeia de ligação estão definidos corretamente na classe NotificationSettings.

    > [!NOTE]
    > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, só deve distribuir a chave para acesso de escuta com a sua aplicação cliente. O acesso de escuta permite que a sua aplicação seja registada para receber notificações, mas não é possível modificar os registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço de back-end protegido para o envio de notificações e a alteração de registos existentes.

6. Em seguida, adicione as seguintes importações:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
7. Adicione o seguinte método `subscribe` para gerir o evento de clique do botão Subscrever:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Este método cria uma lista de categorias e utiliza a `Notifications` classe para armazenar a lista no armazenamento local e registar as etiquetas correspondentes com o seu centro de notificação. Quando as categorias são alteradas, o registo é recriado com as novas categorias.

A sua aplicação pode agora armazenar um conjunto de categorias no armazenamento local do dispositivo e ficar registada no Hub de Notificação sempre que o utilizador alterar a seleção das categorias.

## <a name="register-for-notifications"></a>Registar-se para receber notificações

Estes passos efetuam o registo através do hub de notificação no arranque mediante a utilização das categorias que foram armazenadas no armazenamento local.

> [!NOTE]
> Como o registrationId atribuído pelo Google Cloud Messaging (GCM) pode mudar em qualquer altura, deve registar-se para receber notificações frequentemente para evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Relativamente às aplicações executadas com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo de modo a preservar a largura de banda caso tenha passado menos de um dia desde o registo anterior.

1. Adicione o seguinte código no final do `onCreate` método na `MainActivity` classe:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Este código garante que, sempre que a aplicação é iniciada, as categorias são recuperadas a partir do armazenamento local e o registo das mesmas é solicitado.
2. Em seguida, atualize o método `onStart()` da classe `MainActivity` da seguinte forma:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Este código atualiza a atividade principal com base no estado das categorias guardadas anteriormente.

A aplicação está agora completa, sendo capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para o efetuar o registo através do hub de notificação sempre que o utilizador alterar a seleção de categorias. Em seguida, defina um back-end apto a enviar notificações de categoria para esta aplicação.

## <a name="send-tagged-notifications"></a>Enviar notificações marcadas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testar a aplicação

1. No Android Studio, execute a aplicação no seu dispositivo Android ou emulador. A IU da aplicação fornece um conjunto de botões de alternar que lhe permite selecionar as categorias que pretende subscrever.
2. Ative um ou mais seletores de categorias e, em seguida, clique em **Subscrever**. A aplicação converte as categorias selecionadas em etiquetas e pede um novo registo do dispositivo para as etiquetas selecionadas ao hub de notificação. As categorias registadas são devolvidas e apresentadas numa notificação de alerta.

    ![Subscrever categorias](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Execute a aplicação de consola .NET, que envia notificações para cada categoria. As notificações das categorias selecionadas são apresentadas como notificações de alerta.

    ![Notificações de notícias de tecnologia](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, enviou notificações de transmissões para dispositivos Android específicos que foram registados para as categorias. Para saber como enviar notificações push para utilizadores específicos, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações push para utilizadores específicos](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)