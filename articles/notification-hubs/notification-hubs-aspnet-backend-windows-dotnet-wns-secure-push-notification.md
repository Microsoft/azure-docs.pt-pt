---
title: Hubs de notificação Azure segura empurrão para windows
description: Saiba como enviar notificações de push seguras em Azure. Exemplos de código escrito em C# utilizando a API .NET.
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
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530736"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Pressione as notificações de segurança dos Centros de Notificação Do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

O suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura push de fácil utilização, multiplataforma e escalada, que simplifica consideravelmente a implementação de notificações push tanto para aplicações de consumo como para empresas para dispositivos móveis plataformas.

Devido a restrições regulamentares ou de segurança, por vezes, um pedido pode querer incluir algo na notificação que não pode ser transmitido através da infraestrutura padrão de notificação push. Este tutorial descreve como obter a mesma experiência enviando informações sensíveis através de uma ligação segura e autenticada entre o dispositivo cliente e o backend da aplicação.

A um nível elevado, o fluxo é o seguinte:

1. A aplicação back-end:
   * As lojas asseguram a carga útil na base de dados de back-end.
   * Envia a identificação desta notificação para o dispositivo (não são enviadas informações seguras).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo contacta a parte traseira solicitando a carga útil segura.
   * A aplicação pode mostrar a carga útil como uma notificação no dispositivo.

É importante notar que no fluxo anterior (e neste tutorial), assumimos que o dispositivo armazena um símbolo de autenticação no armazenamento local, após o início do utilizador. Isto garante uma experiência completamente perfeita, uma vez que o dispositivo pode recuperar a carga útil segura da notificação utilizando este símbolo. Se a sua aplicação não armazenar fichas de autenticação no dispositivo, ou se estas fichas puderem ser caducadas, a aplicação do dispositivo, ao receber a notificação, deverá apresentar uma notificação genérica que o utilizador lance a aplicação. A aplicação autentica então o utilizador e mostra a carga útil da notificação.

Este tutorial Secure Push mostra como enviar uma notificação push de forma segura. O tutorial baseia-se no tutorial notificar [utilizadores,](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) pelo que deve completar os passos desse tutorial primeiro.

> [!NOTE]
> Este tutorial assume que criou e configurou o seu centro de notificação conforme descrito em [Getting Started with Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Além disso, note que o Windows Phone 8.1 requer credenciais do Windows (não Do Windows Phone) e que as tarefas de fundo não funcionam no Windows Phone 8.0 ou Silverlight 8.1. No caso das aplicações da Windows Store, só pode receber notificações através de uma tarefa de fundo se a aplicação estiver ativada por ecrã sinuoso (clique na caixa de verificação no Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o Projeto Windows Phone

1. No projeto **NotificaUserWindowsPhone,** adicione o seguinte código a App.xaml.cs para registar a tarefa de fundo push. Adicione a seguinte linha de código no final do método `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Ainda em App.xaml.cs, adicione o seguinte `OnLaunched()` código imediatamente após o método:

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
3. Adicione as `using` seguintes declarações na parte superior do ficheiro App.xaml.cs:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. A partir do menu **Ficheiro** no Visual Studio, clique em **Guardar Tudo**.

## <a name="create-the-push-background-component"></a>Criar o componente de fundo push

O próximo passo é criar o componente de fundo push.

1. No Solution Explorer, clique no nó de nível superior da solução **(Solution SecurePush** neste caso), em seguida, clique em **Adicionar**, em seguida, clique em **Novo Projeto**.
2. Expandir **aplicativos de loja,** em seguida, clicar em **Aplicações windows phone,** em seguida, clicar no Componente de Prazo de **Execução do Windows (Windows Phone)**. Nomeie o projeto **PushBackgroundComponent**e, em seguida, clique em **OK** para criar o projeto.

    ![][12]
3. No Solution Explorer, clique no projeto **PushBackgroundComponent (Windows Phone 8.1)** e, em seguida, clique em **Adicionar**, em seguida, clique em **Classe**. Diga o `PushBackgroundTask.cs`nome da nova classe. Clique em **Adicionar** para gerar a classe.
4. Substitua todo o `PushBackgroundComponent` conteúdo da definição de espaço de `{back-end endpoint}` nome com o seguinte código, substituindo o espaço reservado pelo ponto final obtido durante a implementação do seu back-end:

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
5. No Solution Explorer, clique no projeto **PushBackgroundComponent (Windows Phone 8.1)** e, em seguida, clique em **Gerir pacotes NuGet**.
6. No lado esquerdo, clique em **Online**.
7. Na caixa **Procurar**, escreva **Cliente HTTP**.
8. Na lista de resultados, clique nas **Bibliotecas do Cliente do Microsoft HTTP**e, em seguida, clique em **Instalar**. Conclua a instalação.
9. Na caixa **Procurar** no NuGet, escreva **Json.net**. Instale o pacote **Json.NET** e, em seguida, feche a janela NuGet Package Manager.
10. Adicione as `using` seguintes declarações `PushBackgroundTask.cs` na parte superior do ficheiro:

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
11. No Solution Explorer, no projeto **NotifyUserWindowsPhone (Windows Phone 8.1),** clique direito **referências,** em seguida, clique em **Adicionar Referência...**. No diálogo do Gestor de Referência, verifique a caixa ao lado do **PushBackgroundComponent**, e, em seguida, clique em **OK**.
12. No Solution Explorer, clique duas vezes **no pacote.appxmanifest** no projeto **NotificaUserWindowsPhone (Windows Phone 8.1).** No âmbito **das Notificações,** coloque **torradas capazes de** **sim.**

    ![][3]
13. Ainda em **Pacote.appxmanifest,** clique no menu **Declarações** perto do topo. Nas **Declarações Disponíveis,** clique em Tarefas de **Fundo,** e clique em **Adicionar**.
14. No **Pacote.appxmanifest,** em **Propriedades,** verifique a **notificação push**.
15. No **Pacote.appxmanifest**, em definições de **aplicação**, type **PushBackgroundComponent.PushBackgroundTask** no campo ponto de **entrada.**

    ![][13]
16. No menu **Ficheiro**, clique em **Guardar Tudo**.

## <a name="run-the-application"></a>Executar a Aplicação

Para executar a aplicação, faça o seguinte:

1. No Estúdio Visual, execute a aplicação **AppBackend** Web API. É apresentada uma página web ASP.NET.
2. No Estúdio Visual, execute a aplicação **NotificaUserWindowsPhone (Windows Phone 8.1)** Windows Phone. O emulador Windows Phone executa e carrega a aplicação automaticamente.
3. Na aplicação **NotificaUserWindowsPhone** UI, introduza um nome de utilizador e uma palavra-passe. Estas podem ser qualquer corda, mas devem ter o mesmo valor.
4. Na aplicação **NotificaUserWindowsPhone** UI, clique **em Iniciar sessão e registar.** Em seguida, clique em **Enviar**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
