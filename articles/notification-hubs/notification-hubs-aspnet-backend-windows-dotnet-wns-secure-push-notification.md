---
title: Push seguros de Hubs de notificação do Azure
description: Saiba como enviar notificações push seguro no Azure. Exemplos de código escrito em C# utilizando a API .NET.
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: cf23ef5df3bdcaad23841da111fa06cc36b4cd57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459243"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Notificações push em segurança de Hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

Suporte de notificação push no Microsoft Azure permite-lhe aceder uma infraestrutura de push fáceis de usar, várias plataformas, aumentadas horizontalmente, o que simplifica bastante a implementação de notificações push para aplicações de consumidor e empresariais para dispositivos móveis plataformas.

Devido à regulamentação ou restrições de segurança, por vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitida através da infraestrutura de notificação push padrão. Este tutorial descreve como atingir a mesma experiência ao enviar informações confidenciais por meio de uma ligação autenticada e segura entre o dispositivo de cliente e o back-end de aplicação.

Num alto nível, o fluxo é o seguinte:

1. O back-end da aplicação:
   * Payload de arquivos seguro na base de dados de back-end.
   * Envia o ID de notificação para o dispositivo (não existem informações de seguras são enviadas).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo entra em contacto com o back-end solicitando o payload de seguro.
   * A aplicação pode mostrar o payload como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), partimos do princípio de que o dispositivo armazena um token de autenticação no armazenamento local, depois do usuário fizer logon. Isso garante uma experiência totalmente integrada, como o dispositivo pode obter o payload de seguros a notificação com este token. Se seu aplicativo não armazena os tokens de autenticação do dispositivo, ou se estes tokens podem ter expirados, a aplicação de dispositivo, ao receber a notificação deverá apresentar uma notificação genérica pedir ao utilizador para iniciar a aplicação. A aplicação, em seguida, autentica o utilizador e mostra o payload de notificação.

Este tutorial de Push seguro mostra como enviar uma notificação push de forma segura. O tutorial baseia-se a [notificar utilizadores](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) tutorial, pelo que deverá concluir os passos esse tutorial primeiro.

> [!NOTE]
> Este tutorial pressupõe que já criou e configurou o notification hub conforme descrito em [introdução aos Hubs de notificação (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Além disso, tenha em atenção que o Windows Phone 8.1 requer credenciais do Windows (não Windows Phone) e que as tarefas em segundo plano não funcionam no Windows Phone 8.0 ou Silverlight 8.1. Para aplicativos da Windows Store, pode receber notificações através de uma tarefa em segundo plano apenas se a aplicação está ativada de ecrã de bloqueio (clique na caixa de verificação a Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projeto do Windows Phone

1. Na **NotifyUserWindowsPhone** do projeto, adicione o código a seguir a App.xaml.cs para registar a tarefa em segundo plano de push. Adicione a seguinte linha de código no final do método `OnLaunched()`:

    ```c#
    RegisterBackgroundTask();
    ```
2. Ainda no App.xaml.cs, adicione o seguinte código imediatamente após o `OnLaunched()` método:

    ```c#
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
3. Adicione o seguinte `using` declarações na parte superior do ficheiro App.xaml.cs:

    ```c#
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. A partir do menu **Ficheiro** no Visual Studio, clique em **Guardar Tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de plano de fundo de Push

A próxima etapa é criar o componente de plano de fundo de push.

1. No Explorador de soluções, clique no nó de nível superior da solução (**SecurePush da solução** neste caso), em seguida, clique em **Add**, em seguida, clique em **novo projeto**.
2. Expanda **Store Apps**, em seguida, clique em **aplicativos do Windows Phone**, em seguida, clique em **componente de tempo de execução do Windows (Windows Phone)**. Nomeie o projeto **PushBackgroundComponent**e, em seguida, clique em **OK** para criar o projeto.

    ![][12]
3. No Solution Explorer, clique com botão direito a **PushBackgroundComponent (Windows Phone 8.1)** do projeto, em seguida, clique em **Add**, em seguida, clique em **classe**. Nome à nova classe `PushBackgroundTask.cs`. Clique em **adicionar** para gerar a classe.
4. Substitua os conteúdos integrais do `PushBackgroundComponent` definição de espaço de nomes pelo código seguinte, substituindo o marcador de posição `{back-end endpoint}` com o ponto final de back-end obtido durante a implementação de back-end:

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
5. No Solution Explorer, clique com botão direito a **PushBackgroundComponent (Windows Phone 8.1)** do projeto e, em seguida, clique em **gerir pacotes NuGet**.
6. No lado esquerdo, clique em **Online**.
7. Na caixa **Procurar**, escreva **Cliente HTTP**.
8. Na lista de resultados, clique em **bibliotecas de cliente do Microsoft HTTP**e, em seguida, clique em **instalar**. Conclua a instalação.
9. Na caixa **Procurar** no NuGet, escreva **Json.net**. Instalar o **Json.NET** empacotar, em seguida, feche a janela do Gestor de pacotes NuGet.
10. Adicione as seguintes `using` declarações na parte superior do `PushBackgroundTask.cs` ficheiro:

    ```c#
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. No Solution Explorer, no **NotifyUserWindowsPhone (Windows Phone 8.1)** do projeto, clique com botão direito **referências**, em seguida, clique em **Add Reference...** . Na caixa de diálogo Gestor de referências, selecione a caixa junto a **PushBackgroundComponent**e, em seguida, clique em **OK**.
12. No Explorador de soluções, faça duplo clique **Package. appxmanifest** no **NotifyUserWindowsPhone (Windows Phone 8.1)** projeto. Sob **notificações**, defina **com capacidade de alerta** para **Sim**.

    ![][3]
13. Ainda na **Package. appxmanifest**, clique nas **declarações** menu junto à parte superior. Na **declarações disponíveis** menu pendente, clique em **tarefas em segundo plano**e, em seguida, clique em **Add**.
14. Na **Package. appxmanifest**, em **propriedades**, verifique **notificação Push**.
15. Na **Package. appxmanifest**, em **as definições da aplicação**, tipo **PushBackgroundComponent.PushBackgroundTask** no **ponto de entrada** campo.

    ![][13]
16. No menu **Ficheiro**, clique em **Guardar Tudo**.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, faça o seguinte:

1. No Visual Studio, execute o **AppBackend** aplicação Web API. É apresentada uma página da web ASP.NET.
2. No Visual Studio, execute o **NotifyUserWindowsPhone (Windows Phone 8.1)** aplicativo do Windows Phone. O emulador do Windows Phone é executado e carrega a aplicação automaticamente.
3. Na **NotifyUserWindowsPhone** aplicação da interface do Usuário, introduza um nome de utilizador e palavra-passe. Estes podem ser qualquer cadeia de caracteres, mas têm de ser o mesmo valor.
4. Na **NotifyUserWindowsPhone** aplicação da interface do Usuário, clique em **iniciar sessão e registar**. Em seguida, clique em **enviar push**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
