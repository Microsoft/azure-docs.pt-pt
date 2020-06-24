---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112064"
---
### <a name="validate-package-name-and-permissions"></a>Validar o nome e permissões do pacote

1. No **PushDemo.Android,** abra as **Opções de Projeto** e depois **aplicação Android** a partir da secção **Build.**

1. Verifique se o **nome Pacote** corresponde ao valor utilizado no projeto **PushDemo** [da consola Firebase.](https://console.firebase.google.com) O **nome do Pacote** estava no ``com.<organization>.pushdemo`` formato.

1. Desaponte a **versão mínima do Android** para o Android **8.0 (nível API 26)** e a **versão Target Android** para o mais recente **nível API**.

    > [!NOTE]
    > Apenas os dispositivos com **nível API de nível 26 ou superior** são suportados para efeitos deste tutorial, no entanto pode alargá-lo para dispositivos de suporte que executam versões mais antigas.

1. Certifique-se de que as permissões **de INTERNET** e **READ_PHONE_STATE** estão ativadas sob **as permissões necessárias**.

1. Clique **em OK**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Adicione os pacotes Xamarin Google Play Services Base e Xamarin.Firebase.Messaging

1. Em **PushDemo.Android**, **Control**  +  **Clique** na pasta **Pacotes** e, em seguida, escolha **Gerir Pacotes NuGet...**.

1. Procure **Xamarin.GooglePlayServices.Base** (não **Cave)** e certifique-se de que está verificado.

1. Procure **Xamarin.Firebase.Messaging** e certifique-se de que é verificado.

1. Clique **em Adicionar Pacotes**e, em seguida, clique em **Aceitar** quando solicitado para aceitar os termos **da licença**.

### <a name="add-the-google-services-json-file"></a>Adicione o ficheiro JSON do Google Services

1. **Controlo**  +  **Clique** no `PushDemo.Android` projeto e, em seguida, escolha **O Ficheiro Existente...** no menu **Adicionar.**

1. Escolha a *google-services.jsno* ficheiro que descarregou anteriormente quando configurar o projeto **PushDemo** na [Consola Firebase](https://console.firebase.google.com) e, em seguida, clique em **Abrir**.

1. Quando solicitado, opte por **copiar o ficheiro para o diretório**.

1. **Controlo**  +  **Clique** no *google-services.jsficheiro* dentro do `PushDemo.Android` projeto e certifique-se de que o **GoogleServicesJson** está definido como Build **Action**.

### <a name="handle-push-notifications-for-android"></a>Lidar com notificações push para Android

1. **Controlo**  +  **Clique** no `PushDemo.Android` projeto, escolha **Nova Pasta** no menu **Adicionar** e, em seguida, clique em **Adicionar** usando *os Serviços* como nome de **pasta**.

1. **Controlo**  +  **Clique** na pasta **Serviços** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Selecione **Classe**  >  **Geral Vazia**, *introduza DeviceInstallationService.cs* para o **Nome,** em seguida, clique em **Novo** adicionando a seguinte implementação.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Esta classe fornece um ID único (usando [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) como parte da carga de registo do centro de notificação.

1. Adicione outra **Classe Vazia** à pasta **serviços** chamada *PushNotificationFirebaseMessagingService.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. Em **MainActivity.cs**, certifique-se de que os seguintes espaços de nome foram adicionados ao topo do ficheiro.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. Em **MainActivity.cs**, desaperte o **LaunchMode** para **SingleTop** para que o **MainActivity** não seja criado novamente quando for aberto.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Adicione propriedades privadas e campos de apoio correspondentes para armazenar uma referência às implementações **iPushNotificationActionService** e **IDeviceInstallationService.**

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implemente a interface **IOnSuccessListener** para recuperar e armazenar o token **firebase.**

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Adicione um novo método chamado **ProcessNotificationActions** que verificará se uma determinada **Intenção** tem um valor extra nomeado *ação*. Desencadeie condicionalmente essa ação utilizando a implementação do **IPushDemoNotificationActionService.**

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Substitua o método **OnNewIntent** para ligar para o método **CheckIntentForNotificationActions.**

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Uma vez que o **LaunchMode** for the **Activity** está definido para **SingleTop,** uma **Intenção** será enviada para a instância **de Atividade** existente através do método **OnNewIntent** em vez do método **OnCreate** e, por isso, deve lidar com uma intenção de entrada tanto nos métodos **OnCreate** como **onNewIntent.**

1. Atualize o método **OnCreate** para ligar `Bootstrap.Begin` logo após a chamada para passar na `base.OnCreate` implementação específica da plataforma do **IDeviceInstallAtionService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. No mesmo método, ligue condicionalmente **para GetInstanceId** no caso **FirebaseApp,** logo após a chamada para `Bootstrap.Begin` , adicionando **MainActivity** como o **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Ainda em **OnCreate,** ligue para **o ProcessNotificationActions** imediatamente após a chamada para `LoadApplication` passar na **intenção**atual .

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Tem de voltar a registar a aplicação sempre que a executou e impedi-la de uma sessão de depuragem para continuar a receber notificações push.
