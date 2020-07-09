---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147823"
---
### <a name="add-the-google-services-json-file"></a>Adicione o ficheiro JSON do Google Services

1. **Controlo**  +  **Clique** na pasta **android** e, em seguida, escolha **Abrir no Android Studio**. Em seguida, mude para a vista **do Projeto** (se já não estiver).

1. Localize as *google-services.jsno* ficheiro que descarregou anteriormente quando configurar o projeto **PushDemo** na [Consola Firebase](https://console.firebase.google.com). Em seguida, arraste-o para o diretório raiz do módulo **de aplicações** **android**  >  **android**  >  **(aplicativo**android android).

### <a name="configure-build-settings-and-permissions"></a>Configurar configurações e permissões de construção

1. Mude a vista **do Projeto** para **Android.**

1. Abra **AndroidManifest.xml**, adicione a **INTERNET** e **READ_PHONE_STATE** permissões após o elemento **de aplicação** antes da etiqueta de **</manifest>** fecho.

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Adicione os SDKs da base de fogo

1. No **Android Studio,** abra o arquivo **build.gradle** **(Gradle Scripts**  >  **build.gradle (Project: android)**). e certifique-se de que tem o classe 'com.google.gms:google-services' no nó ``buildscript``  >  **de dependências.**

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Certifique-se de que faz referência à versão mais recente de acordo com as instruções fornecidas na [Consola Firebase](https://console.firebase.google.com) quando criou o **Projeto Android.**

1. No ficheiro **build.gradle** **(Gradle Scripts**  >  **build.gradle (Módulo: app)**), aplique o **plugin Gradle gradle do Google Services**. Aplique o plugin logo acima do nó **android.**

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. No mesmo ficheiro, no nó **de dependências,** adicione a dependência da biblioteca **Android de Mensagens Cloud.**

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Certifique-se de que refere a versão mais recente de acordo com a documentação do [cliente Android de Mensagens Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/android/client).

1. Guarde as alterações e, em seguida, clique no botão **Sync Now** (a partir do pedido da barra de ferramentas) ou No Projeto Sync **com Ficheiros Gradle**.

### <a name="handle-push-notifications-for-android"></a>Lidar com notificações push para Android

1. No **Android Studio**, **Control**  +  **Clique** na pasta do pacote **com.<your_organization>.pushdemo** **(app**  >  **src**  >  **main**  >  **kotlin),** escolha **Pacote** no novo menu. **New** Introduza **os serviços** como o nome e, em seguida, prima **Return**.

1. **Controlo**  +  **Clique** na pasta **de serviços,** escolha **Kotlin File/Class** **a** partir do novo menu. Introduza **o DeviceInstallationService** como o nome e, em seguida, prima **Devolução**.

1. Implementar o **DeviceInstallsService** utilizando o seguinte código.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Esta classe implementa a contrapartida específica da plataforma para o `com.<your_organization>.pushdemo/deviceinstallation` canal. Isto foi definido na parte flutter da app dentro **do DeviceInstallationService.dart**. Neste caso, as chamadas são feitas do código comum para o hospedeiro nativo. Não se esqueça de substituir **<your_organization>** por sua própria organização onde quer que isso seja usado.
    >
    > Esta classe fornece um ID único (usando [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) como parte da carga de registo do centro de notificação.

1. Adicione outro **Ficheiro Kotlin/Classe** à pasta de **serviços** chamada *NotificationRegistrationService,* em seguida, adicione o seguinte código.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Esta classe implementa a contrapartida específica da plataforma para o `com.<your_organization>.pushdemo/notificationregistration` canal. Isto foi definido na parte flutter da app dentro **do NotificationRegistrationService.dart**. Neste caso, as chamadas são feitas do hospedeiro nativo para o código comum. Mais uma vez, tome cuidado para substituir **<your_organization>** pela sua própria organização onde quer que isso seja usado.

1. Adicione outro **Ficheiro Kotlin/Classe** à pasta de **serviços** chamada *NotificationActionService*e adicione o seguinte código.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Esta classe implementa a contrapartida específica da plataforma para o `com.<your_organization>.pushdemo/notificationaction` canal. Isto foi definido na parte flutter da app dentro **do NotificationActionService.dart**. As chamadas podem ser feitas em ambas as direções neste caso. Não se esqueça de substituir **<your_organization>** por sua própria organização onde quer que isso seja usado.

1. Adicione um novo **Kotlin File/Class** ao pacote **com.<your_organization>.pushdemo** chamado *PushNotificationsFirebaseMessagingService,* em seguida, implemente usando o seguinte código.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Esta classe é responsável pelo tratamento de notificações quando a aplicação está em primeiro plano. Irá ligar condicionalmente para o **triggerAction** no **NotificationActionService** se uma ação for incluída na carga útil de notificação recebida no **OnMessageReceived**. Isto também chamará **refreshRegistration** no **NotificationRegistrationService** quando o token **firebase** for regenerado por sobrevassar a função **onNewToken.**
    >
    > Mais uma vez, tenha o cuidado de substituir **<your_organization>** pela sua própria organização onde quer que seja usada.

1. Em **AndroidManifest.xml** **(app**  >  **src**  >  **main),** adicione o **PushNotificationsFirebaseMessagingService** na parte inferior do elemento de **aplicação** com o `com.google.firebase.MESSAGING_EVENT` filtro de intenção.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. De volta ao **DeviceInstallationService**, certifique-se de que as seguintes importações estão presentes no topo do ficheiro.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Substitua **<your_organization>** pelo seu próprio valor de organização.

1. Atualize o texto do espaço reservado *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* para obter o valor simbólico do **Serviço pushNotificationFirebaseMessaging .**

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. Na **MainActivity,** certifique-se de que as seguintes importações estão presentes no topo do processo.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Substitua **<your_organization>** pelo seu próprio valor de organização.

1. Adicione uma variável para armazenar uma referência ao **Serviço de Instalação de Dispositivos**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Adicione uma função chamada **processNotificationActions** para verificar se uma **Intenção** tem um valor extra nomeado **ação**. Desencadeie condicionalmente essa ação ou armazene-a para utilização posterior se a ação estiver a ser processada durante o lançamento da aplicação.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Substitua a função **onNewIntent** para chamar **processoNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Uma vez que o **LaunchMode** for **MainActivity** está definido para **SingleTop,** uma **Intenção** será enviada para a instância **de Atividade** existente através da função **onNewIntent** em vez da função **onCreate** e, por isso, deve lidar com uma **intenção** de entrada tanto em funções **onCreate** como **em NewIntent.**

1. Substitua a função **onCreate,** deslome o **dispositivoInstallaService** para uma nova instância do **DeviceInstallAtionService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Descode as propriedades do **NotificationActionService** e **da notificationRegistrationService** em **Serviços pushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. Na mesma função, ligue condicionalmente **para FirebaseInstanceId.getInstance().instanceId**. Implemente o **OnCompleteListener** para definir o valor **simbólico** resultante no **PushNotificationFirebaseMessagingService** antes de chamar **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Ainda no **onCreate,** ligue **para o processoNotificationActions** no final da função. Utilize *o argumento* de *launchAction* para indicar que esta ação está a ser processada durante o lançamento da app.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Tem de voltar a registar a aplicação sempre que a executou e impedi-la de uma sessão de depuragem para continuar a receber notificações push.
