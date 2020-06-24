---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c13b7ee8c5c0a0d302e4822047ea60f9df120bf8
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112070"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Configure Info.plist e Direitos.plist

1. Certifique-se de que inscreveu na sua **Conta de Desenvolvimento Apple** em Preferências de Estúdio **Visual Studio**  >  **Visual...**  >  **Publicação**  >  **As contas do Apple Developer** e o *certificado* e perfil *de provisionamento* adequados foram descarregados. Devia ter criado estes ativos como parte dos passos anteriores.

1. Em **PushDemo.iOS**, abra **info.plist** e certifique-se de que o **BundleIdentifier** corresponde ao valor utilizado para o respetivo perfil de provisionamento no [Portal do Desenvolvedor](https://developer.apple.com)da Apple . O **BundleIdentifier** estava no ``com.<organization>.PushDemo`` formato.

1. No mesmo ficheiro, definir a **versão mínima** do sistema para **13.0**.

    > [!NOTE]
    > Apenas os dispositivos que executam **o iOS 13.0 ou acima** são suportados para efeitos deste tutorial, no entanto pode alargá-lo para dispositivos de suporte que executam versões mais antigas.

1. Abra as **opções** de projeto para **PushDemo.iOS** (clique duas vezes no projeto).

1. Nas **Opções de Projeto**, em Build > **iOS Bundle Signing**, certifique-se de que a sua conta Developer é selecionada sob a **equipa**. Em seguida, certifique-se de que é selecionada a "assinatura de gestão automática" e que o seu Certificado de Assinatura e Perfil de Provisionamento são automaticamente selecionados.

    > [!NOTE]
    > Se o seu *Certificado de Assinatura* e Perfil de *Provisionamento* não tiver sido selecionado automaticamente, escolha **o Provisionamento Manual,** clique em **Opções de Assinatura de Pacotes**. Certifique-se de que a sua *Equipa* está selecionada para **a Assinatura de Identidade** e o seu perfil de provisionamento específico *pushDemo* é selecionado para o **Perfil de Provisionamento** para as configurações **de Debug** e **Release,** garantindo que o **iPhone** é selecionado para a **Plataforma** em ambos os casos.

1. Em **PushDemo.iOS**, abra **Entitlements.plist** e certifique-se de que **as notificações push** são verificadas quando visualizadas no **separador Direitos.** Em seguida, certifique-se de que a definição **aps ambiente** está definida para **o desenvolvimento** quando visualizada no separador **Fonte.**

### <a name="handle-push-notifications-for-ios"></a>Lidar com notificações push para iOS

1. **Controlo**  +  **Clique** no projeto **PushDemo.iOS,** escolha **Nova Pasta** no menu **Adicionar** e, em seguida, clique em **Adicionar** usando *serviços* como nome de **pasta**.

1. **Controlo**  +  **Clique** na pasta **Serviços** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Selecione **Classe**  >  **Geral Vazia**, *introduza DeviceInstallationService.cs* para o **Nome,** em seguida, clique em **Novo** adicionando a seguinte implementação.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Esta classe fornece um ID único (utilizando o valor [UIDevice.IdentifierForVendor)](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) e a carga útil do registo do centro de notificação.

1. Adicione uma nova pasta ao projeto **PushDemo.iOS** chamado *Extensões* e adicione uma **Classe Vazia** àquela pasta chamada *NSDataExtensions.cs* com a seguinte implementação.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. Em **AppDelegate.cs**, certifique-se de que os seguintes espaços de nome foram adicionados ao topo do ficheiro.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Adicione uma constante para a chave de cache simbólica do dispositivo.

    ```csharp
    const string CachedDeviceToken = "cached_device_token";
    ```

1. Adicione propriedades privadas e respetivos campos de suporte para armazenar uma referência às implementações **iPushDemoNotificationActionService**, **INotificationRegistrationService**e **IDeviceInstallationService.**

    ```csharp
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
    ```

1. Adicione o método **RegisterForRemoteNotifications** para registar as definições de notificação do utilizador e, em seguida, para notificações remotas com **a APNS**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Adicione o método **CompleteRegistrationAsync** para definir o valor da `IDeviceInstallationService.Token` propriedade. Refresque o registo e cache o token do dispositivo se este tiver sido atualizado desde que foi armazenado pela última vez.

    ```csharp
    async Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();

        var cachedToken = await SecureStorage.GetAsync(CachedDeviceToken)
            .ConfigureAwait(false);

        if (!string.IsNullOrWhiteSpace(cachedToken) &&
            cachedToken.Equals(DeviceInstallationService.Token))
            return;

        await NotificationRegistrationService.RefreshRegistrationAsync()
            .ConfigureAwait(false);

        await SecureStorage.SetAsync(CachedDeviceToken, DeviceInstallationService.Token)
            .ConfigureAwait(false);
    }
    ```

1. Adicione o método **ProcessNotificationActions** para o processamento dos dados de notificação **NSDictionary** e ligar condicionalmente **para NotificationActionService.TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Substitua o método **Registado ForRemoteNotifications** passando o argumento **deviceToken** para o método **CompleteRegistrationAsync.**

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Substitua o método **ReceivedRemoteNotification** passando o argumento **userInfo** para o método **ProcessNotificationActions.**

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Anular o método **failedToRegisterForRemoteNotifications** para registar o erro.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Este é muito um espaço reservado. Você vai querer implementar o manuseamento adequado de registos e erros para cenários de produção.

1. Atualize o método **de AcabamentosLaunching** para ligar `Bootstrap.Begin` logo após a chamada para passar na `Forms.Init` implementação específica da plataforma do **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. No mesmo método, solicite condicionalmente a autorização e registe-se para notificações remotas imediatamente após `Bootstrap.Begin` .

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Ainda em **AcabadoLaunching,** ligue para **ProcessNotificationActions** imediatamente após a chamada para `LoadApplication` se o argumento de **opções** contiver o **UIApplication.LaunchOptionsRemoteNotificationKey** passando no objeto **utilizadorInfo** resultante.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
