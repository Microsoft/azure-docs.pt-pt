---
title: Push seguro dos hubs de notificação do Azure
description: Saiba como enviar notificações por push seguras no Azure. Exemplos de código escrito em C# utilizando a API .NET.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5d1cf2a74d4fe85bb85eb244da3e3757f36fba0a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212044"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Enviar notificações por push de hubs de notificação do Azure com segurança

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio fácil de usar, multiplataforma e escalável, que simplifica bastante a implementação de notificações por push para aplicativos de consumidor e empresariais para dispositivos móveis compatíveis.

Devido a restrições normativas ou de segurança, às vezes um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo cliente e o back-end do aplicativo.

Em um alto nível, o fluxo é o seguinte:

1. O back-end do aplicativo:
   * Armazena a carga segura no banco de dados back-end.
   * Envia a ID dessa notificação para o dispositivo (nenhuma informação segura é enviada).
2. O aplicativo no dispositivo, ao receber a notificação:
   * O dispositivo entra em contato com o back-end solicitando a carga segura.
   * O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que, no fluxo anterior (e neste tutorial), presumimos que o dispositivo armazene um token de autenticação no armazenamento local, depois que o usuário fizer logon. Isso garante uma experiência completamente simples, pois o dispositivo pode recuperar a carga segura da notificação usando esse token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação, deverá exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Em seguida, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial de push seguro mostra como enviar uma notificação por push com segurança. O tutorial se baseia no tutorial [notificar usuários](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , portanto, você deve concluir as etapas nesse tutorial primeiro.

> [!NOTE]
> Este tutorial pressupõe que você criou e configurou seu hub de notificação conforme descrito em [introdução com hubs de notificação (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Além disso, observe que Windows Phone 8,1 requer credenciais do Windows (não Windows Phone) e as tarefas em segundo plano não funcionam no Windows Phone 8,0 ou no Silverlight 8,1. Para aplicativos da Windows Store, você poderá receber notificações por meio de uma tarefa em segundo plano somente se o aplicativo estiver habilitado para tela de bloqueio (clique na caixa de seleção no arquivo AppManifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projeto de Windows Phone

1. No projeto **NotifyUserWindowsPhone** , adicione o seguinte código ao app.XAML.cs para registrar a tarefa de plano de fundo Push. Adicione a seguinte linha de código no final do método `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Ainda em app.XAML.cs, adicione o seguinte código imediatamente após o `OnLaunched()` método:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. Adicione as seguintes `using` instruções na parte superior do arquivo app.XAML.cs:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. A partir do menu **Ficheiro** no Visual Studio, clique em **Guardar Tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de plano de fundo de push

A próxima etapa é criar o componente de plano de fundo Push.

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no nó de nível superior da solução (**solução SecurePush** , nesse caso), clique em **Adicionar**e em **novo projeto**.
2. Expanda **armazenar aplicativos**, clique em **Windows Phone aplicativos**e, em seguida, clique em **Windows Runtime Component (Windows Phone)** . Nomeie o projeto **PushBackgroundComponent**e clique em **OK** para criar o projeto.

    ![][12]
3. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **PushBackgroundComponent (Windows Phone 8,1)** , clique em **Adicionar**e em **classe**. Nomeie a nova classe `PushBackgroundTask.cs`. Clique em **Adicionar** para gerar a classe.
4. Substitua todo o conteúdo da `PushBackgroundComponent` definição do namespace pelo código a seguir, substituindo o espaço reservado `{back-end endpoint}` pelo ponto de extremidade de back-end obtido durante a implantação do back-end:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **PushBackgroundComponent (Windows Phone 8,1)** e clique em **gerenciar pacotes NuGet**.
6. No lado esquerdo, clique em **Online**.
7. Na caixa **Procurar**, escreva **Cliente HTTP**.
8. Na lista de resultados, clique em **bibliotecas de cliente http da Microsoft**e, em seguida, clique em **instalar**. Conclua a instalação.
9. Na caixa **Procurar** no NuGet, escreva **Json.net**. Instale o pacote **JSON.net** e feche a janela Gerenciador de pacotes NuGet.
10. Adicione as seguintes `using` instruções na parte superior `PushBackgroundTask.cs` do arquivo:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. Em Gerenciador de Soluções, no projeto **NotifyUserWindowsPhone (Windows Phone 8,1)** , clique com o botão direito do mouse em **referências**e clique em **Adicionar referência..** .. No diálogo Gerenciador de referências, marque a caixa ao lado de **PushBackgroundComponent**e clique em **OK**.
12. Em Gerenciador de Soluções, clique duas vezes em **Package. appxmanifest** no projeto **NotifyUserWindowsPhone (Windows Phone 8,1)** . Em **notificações**, defina **habilitado para notificação do sistema** como **Sim**.

    ![][3]
13. Ainda em **Package. appxmanifest**, clique no menu **declarações** próximo à parte superior. Na lista suspensa **declarações disponíveis** , clique em **tarefas em segundo plano**e, em seguida, clique em **Adicionar**.
14. Em **Package. appxmanifest**, em **Propriedades**, marque **notificação por push**.
15. Em **Package. appxmanifest**, em **configurações do aplicativo**, digite **PushBackgroundComponent. PushBackgroundTask** no campo **ponto de entrada** .

    ![][13]
16. No menu **Ficheiro**, clique em **Guardar Tudo**.

## <a name="run-the-application"></a>Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No Visual Studio, execute o aplicativo de API Web **AppBackend** . Uma página da Web do ASP.NET é exibida.
2. No Visual Studio, execute o aplicativo **NotifyUserWindowsPhone (Windows Phone 8,1)** Windows Phone. O emulador de Windows Phone executa e carrega o aplicativo automaticamente.
3. Na interface do usuário do aplicativo **NotifyUserWindowsPhone** , insira um nome de usuário e senha. Elas podem ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.
4. Na interface do usuário do aplicativo **NotifyUserWindowsPhone** , clique em **fazer logon e registrar-se**. Em seguida, clique em **enviar envio por push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
