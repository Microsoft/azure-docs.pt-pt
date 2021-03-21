---
title: Azure Notification Hubs Secure Push for Windows
description: Saiba como enviar notificações push seguras em Azure. Exemplos de código escrito em C# utilizando a API .NET.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019430"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Enviar notificações push seguras dos hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Descrição geral

O suporte de notificação push no Microsoft Azure permite-lhe aceder a uma infraestrutura push de fácil utilização, multiplataforma e dimensionada, o que simplifica consideravelmente a implementação de notificações push para aplicações de consumidores e empresas para plataformas móveis.

Devido a restrições regulamentares ou de segurança, por vezes, um pedido pode querer incluir algo na notificação que não pode ser transmitido através da infraestrutura padrão de notificação push. Este tutorial descreve como obter a mesma experiência enviando informações sensíveis através de uma ligação segura e autenticada entre o dispositivo cliente e o backend da app.

A um nível elevado, o fluxo é o seguinte:

1. A aplicação back-end:
   * As lojas protegem a carga útil na base de dados back-end.
   * Envia o ID desta notificação para o dispositivo (não é enviada nenhuma informação segura).
2. A aplicação no dispositivo, ao receber a notificação:
   * O dispositivo contacta a parte de trás solicitando a carga útil segura.
   * A aplicação pode mostrar a carga útil como uma notificação no dispositivo.

É importante notar que no fluxo anterior (e neste tutorial), assumimos que o dispositivo armazena um token de autenticação no armazenamento local, após o registo do utilizador. Isto garante uma experiência completamente perfeita, uma vez que o dispositivo pode recuperar a carga útil segura da notificação utilizando este token. Se a sua aplicação não armazenar fichas de autenticação no dispositivo, ou se estas fichas puderem ser expiradas, a aplicação do dispositivo, ao receber a notificação, deverá apresentar uma notificação genérica que o leva o utilizador a lançar a aplicação. A aplicação autentica então o utilizador e mostra a carga útil da notificação.

Este tutorial mostra como enviar uma notificação push de forma segura. O tutorial baseia-se no tutorial de [utilizadores da Notificação,](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) pelo que deve completar primeiro os passos nesse tutorial.

> [!NOTE]
> Este tutorial pressupõe que criou e configura o seu centro de notificações como descrito no [Enviar notificações para as aplicações da Plataforma Universal windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Além disso, note que o Windows Phone 8.1 requer credenciais do Windows (não do Windows Phone) e que as tarefas de fundo não funcionam no Windows Phone 8.0 ou Silverlight 8.1. Para aplicações windows Store, só pode receber notificações através de uma tarefa de fundo se a aplicação estiver ativada no ecrã de bloqueio (clique na caixa de verificação no Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Modificar o projeto Windows Phone

1. No projeto **NotifyUserWindowsPhone,** adicione o seguinte código à App.xaml.cs para registar a tarefa de fundo de push. Adicione a seguinte linha de código no final do método `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```

2. Ainda em App.xaml.cs, adicione o seguinte código imediatamente após o `OnLaunched()` método:

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

3. Adicione as `using` seguintes declarações no topo do ficheiro app.xaml.cs:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. A partir do menu **Ficheiro** no Visual Studio, clique em **Guardar Tudo**.

## <a name="create-the-push-background-component"></a>Crie o componente de fundo push

O próximo passo é criar o componente de fundo de impulso.

1. No Solution Explorer, clique no nó de nível superior da solução **(Solution SecurePush** neste caso), clique em **Adicionar** e, em seguida, clique em **Novo Projeto**.
2. Expandir **as aplicações da loja,** em seguida, clicar em **Aplicativos windows phone** e, em seguida, clicar no Componente de tempo de **execução do Windows (Windows Phone)**. Nomeie o projeto **PushBackgroundComponent** e, em seguida, clique **em OK** para criar o projeto.

    ![Screenshot do diálogo Add New Project com a opção Visual C# do Windows Runtime Component (Windows Phone).][12]
3. No Solution Explorer, clique com o botão direito no projeto **PushBackgroundComponent (Windows Phone 8.1)** e, em seguida, clique em **Adicionar** e, em seguida, clique em **Classe**. Diga o nome da nova `PushBackgroundTask.cs` classe. Clique **em Adicionar** para gerar a classe.
4. Substitua todo o conteúdo da definição de `PushBackgroundComponent` espaço de nome pelo seguinte código, substituindo o espaço reservado pelo ponto final de `{back-end endpoint}` back-end obtido enquanto implementa o seu back-end:

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

5. No Solution Explorer, clique com o botão direito no projeto **PushBackgroundComponent (Windows Phone 8.1)** e, em seguida, clique em **Gerir Pacotes NuGet**.
6. No lado esquerdo, clique em **Online**.
7. Na caixa **Procurar**, escreva **Cliente HTTP**.
8. Na lista de resultados, clique nas **Bibliotecas do Cliente Microsoft HTTP** e, em seguida, clique em **Instalar**. Conclua a instalação.
9. Na caixa **Procurar** no NuGet, escreva **Json.net**. Instale o pacote **Json.NET** e feche a janela NuGet Package Manager.
10. Adicione as `using` seguintes declarações no topo do `PushBackgroundTask.cs` ficheiro:

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

11. No Solution Explorer, no projeto **NotifyUserWindowsPhone (Windows Phone 8.1),** clique com o botão direito **Referências,** em seguida, clique em **Adicionar Referência...**. No diálogo do Gestor de Referência, verifique a caixa ao lado do **PushBackgroundComponent** e, em seguida, clique **em OK**.
12. No Solution Explorer, clique duplo **em Package.appxmanifest** no projeto **NotifyUserWindowsPhone (Windows Phone 8.1).** Em **Notificações**, definir **Torrada capaz** de **sim**.

    ![Screenshot da janela Solution Explorer focada no Package.appxmanifest com a opção "Torrada" definida para Sim delineada a vermelho.][3]
13. Ainda em **Package.appxmanifest,** clique no menu **Declarações** perto do topo. Nas **declarações disponíveis,** clique em **"Background Tasks"** e, em seguida, clique em **Adicionar**.
14. Em **Package.appxmanifest**, under **Properties**, verifique **a notificação push**.
15. Em **Package.appxmanifest**, em **Definições de Aplicação**, **escreva PushBackgroundComponent.PushBackgroundTask** no campo **Ponto de Entrada.**

    ![Screenshot da janela Solution Explorer focada no Package.appxmanifest com as declarações disponíveis, declarações apoiadas, notificações push e opções de ponto de entrada delineadas a vermelho.][13]
16. No menu **Ficheiro**, clique em **Guardar Tudo**.

## <a name="run-the-application"></a>Executar a aplicação

Para executar a aplicação, faça o seguinte:

1. No Estúdio Visual, execute a aplicação **AppBackend** Web API. É apresentada uma página web ASP.NET.
2. No Estúdio Visual, execute a aplicação **NotifyUserWindowsPhone (Windows Phone 8.1)** do Windows Phone. O emulador do Windows Phone executa e carrega a aplicação automaticamente.
3. Na aplicação **NotifyUserWindowsPhone** UI, insira um nome de utilizador e senha. Estes podem ser qualquer corda, mas devem ter o mesmo valor.
4. Na aplicação **NotifyUserWindowsPhone** UI, clique **em Iniciar sessão e registe-se**. Em seguida, clique **em Enviar o empurrão**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
