---
title: Envie notificações push para apps do Windows Phone usando hubs de notificação Azure Microsoft Docs
description: Neste tutorial, irá aprender a utilizar os Notification Hubs do Azure para enviar notificações push para uma aplicação Windows Phone 8 ou Windows Phone 8.1 Silverlight.
services: notification-hubs
documentationcenter: windows
keywords: notificação push, notificação push, push window phone
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998344"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>Tutorial: Enviar notificações push para apps do Windows Phone usando Centros de Notificação

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra-lhe como utilizar os Notification Hubs do Azure para enviar notificações push para aplicações Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se o seu foco incide sobre o Windows Phone 8.1 (não Silverlight), consulte a versão [Universal para Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) deste tutorial.

Neste tutorial, vai criar uma aplicação Windows Phone 8 em branco que recebe notificações push utilizando o Serviço de Notificações Push da Microsoft (MPNS). Depois de criar a aplicação, pode utilizar o hub de notificação para difundir notificações push para todos os dispositivos a executar a sua aplicação.

> [!NOTE]
> O SDK dos Notification Hubs do Windows Phone não suporta a utilização do Serviço de Notificações Push do Windows (WNS) com aplicações do Windows Phone 8.1 Silverlight. Para utilizar o WNS (em vez do MPNS) com aplicações do Windows Phone 8.1 Silverlight, siga o [Tutorial dos Hubs de Notificação – Windows Phone Silverlight], que utiliza a APIs REST.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um hub de notificação
> * Criar uma aplicação Windows Phone
> * Testar o envio de notificações

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura Azure**. Se não tiver uma subscrição do Azure, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 Express com componentes de desenvolvimento móveis](https://www.visualstudio.com/vs/older-downloads/)

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Notification Hubs para aplicações Windows Phone 8.

## <a name="create-your-notification-hub"></a>Criar o Notification Hub

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Configurar as definições do Windows Phone (MPNS)

1. Selecione **Windows Phone (MPNS)** em **DEFINIÇÕES DE NOTIFICAÇÃO**.
2. Selecione **Ativar autenticação push**.
3. Selecione **Guardar** na barra de ferramentas.

    ![Portal do Azure – Ativar notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    O seu hub já está criado e configurado para enviar notificações não autenticadas para Windows Phone.

    > [!NOTE]
    > Este tutorial utiliza MPNS no modo não autenticado. O modo MPNS não autenticado vem com restrições quanto às notificações que pode enviar para cada canal. Os Notification Hubs suportam o [Modo MPNS autenticado ](/previous-versions/windows/apps/ff941099(v=vs.105)) ao permitirem-lhe carregar o seu certificado.

## <a name="create-a-windows-phone-application"></a>Criar uma aplicação Windows Phone

Nesta secção, vai criar uma aplicação Windows Phone que efetua o próprio registo no hub de notificação.

1. No Visual Studio, crie uma nova aplicação do Windows Phone 8.

    ![Visual Studio – Novo Projeto – Aplicação do Windows Phone][13]

    No Visual Studio 2013 Update 2 ou posterior crie, em vez disso, uma aplicação Silverlight do Windows Phone.

    ![Visual Studio – Novo Projeto – Aplicação em Branco – Windows Phone Silverlight][11]
2. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet**.
3. Procure `WindowsAzure.Messaging.Managed`, clique em **Instalar** e aceite os termos de utilização.

    ![Visual Studio – Gestor de Pacotes NuGet][20]
4. Abra o ficheiro App,xaml.cs e adicione as seguintes instruções `using`:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. Adicione o seguinte código no topo do `Application_Launching` método `App.xaml.cs` em:

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
    }
    ```

   > [!NOTE]
   > O valor `MyPushChannel` é um índice que é usado para procurar um canal existente na coleção [HttpNotificationChannel.](/previous-versions/ff402781(v=vs.110)) Se não existir, crie uma nova entrada com esse nome.

    Insira o nome do seu hub e a cadeia de ligação chamada `DefaultListenSharedAccessSignature` que observou na secção anterior.
    Este código obtém o URI do canal para a aplicação a partir do MPNS e, em seguida, regista esse URI de canal no Notification Hub. Esta ação garante também que o URI do canal é registado no Notification Hub sempre que a aplicação for iniciada.

   > [!NOTE]
   > Este tutorial envia um alerta de notificação para o dispositivo. Ao enviar uma notificação de azulejos, deve, em vez disso, ligar para `BindToShellTile` o método no canal. Para apoiar as notificações de torradas e azulejos, ligue para ambos `BindToShellTile` e  `BindToShellToast` .

6. No Explorador de Soluções, expanda **Propriedades**, abra o `WMAppManifest.xml` ficheiro, clique no separador **Capacidades** e verifique se a capacidade **ID_CAP_PUSH_NOTIFICATION** está selecionada. Agora, a aplicação pode receber notificações push.

    ![Visual Studio – Capacidades das Aplicações Windows Phone][14]
7. Prima a tecla `F5` para executar a aplicação. É apresentada na aplicação uma mensagem de registo.
8. Feche a aplicação ou mude para a home page.

   > [!NOTE]
   > Para receber um alerta de notificação push, a aplicação não pode estar a ser executada em primeiro plano.

## <a name="test-send-a-notification"></a>Testar o envio de notificações

1. No portal do Azure, mude para o separador Descrição Geral.
2. Selecione **Testar Envio**.

    ![Botão Testar Envio](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. Na janela **Testar Envio**, siga os passos abaixo:

    1. Para **Plataformas**, selecione **Windows Phone**.
    2. Para **Tipo de Notificação**, selecione **Alerta**.
    3. Selecione **Enviar**
    4. Veja o **resultado** na lista na parte inferior da janela.

        ![Janela Testar Envio](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. No emulador do Windows Phone ou no Windows Phone, confirme que vê a mensagem de notificação.

    ![Notificação no Windows Phone](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>Passos seguintes

Neste exemplo simples, difundiu notificações push para todos os seus dispositivos Windows Phone 8. Avance para o tutorial seguinte para saber como enviar notificações push para dispositivos específicos:

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos específicos](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[Tutorial dos Hubs de Notificação – Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
