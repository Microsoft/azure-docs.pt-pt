---
title: Enviar notificações para aplicações da Plataforma Universal do Windows com os Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como usar os hubs de notificação do Azure para enviar notificações por push a um aplicativo da plataforma universal do Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: ec0181dd75cd656859967c30dc3941175407413a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895018"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Tutorial: Enviar notificações para aplicações da Plataforma Universal do Windows com os Hubs de Notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Neste tutorial, vai criar um hub de notificação para enviar notificações push para uma aplicação da Plataforma Universal do Windows (UWP). Vai criar uma aplicação da Loja Windows em branco que recebe notificações push com o Serviço de Notificações Push do Windows (WNS). Em seguida, use seu hub de notificação para transmitir notificações por push para todos os dispositivos que estão executando seu aplicativo.

> [!NOTE]
> O código concluído deste artigo está disponível no [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Execute as seguintes etapas:

> [!div class="checklist"]
> * Criar uma aplicação na Loja Windows
> * Criar um hub de notificação
> * Criar uma aplicação do Windows de exemplo
> * Enviar notificações de teste

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Microsoft Visual Studio 2017 ou posterior. O exemplo neste tutorial usa o [Visual Studio 2019](https://www.visualstudio.com/products).
- [Ferramentas de desenvolvimento de aplicações do UWP instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Uma conta ativa da Loja Windows
- Confirme se a configuração **obter notificações de aplicativos e outros remetentes** está habilitada. 
    - Inicie a janela **configurações** no seu computador.
    - Selecione o bloco **sistema** .
    - Selecione **notificações & ações** no menu à esquerda. 
    - Confirme se a configuração **obter notificações de aplicativos e outros remetentes** está habilitada. Se não estiver habilitado, habilite-o.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Hubs de Notificação para aplicações do UWP.

## <a name="create-an-app-in-windows-store"></a>Criar uma aplicação na Loja Windows

Para enviar notificações push para aplicações do UWP, associe a aplicação à Loja Windows. Em seguida, configure o Hub de Notificação para se integrar no WNS.

1. Navegue para o [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), inicie sessão com a sua conta Microsoft e selecione **Criar uma nova aplicação**.

    ![Botão Nova aplicação](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Escreva um nome para a aplicação e selecione **Reservar nome do produto**. Desta forma, é criado um registo novo da Loja Windows para a sua aplicação.

    ![Nome da aplicação na loja](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Expanda **Gerenciamento de produtos**, selecione **WNS/MPNS**e, em seguida, selecione **site de serviços ativos**. Inicie sessão com a sua conta Microsoft. A página de registro do aplicativo é aberta em uma nova guia. como alternativa, você pode navegar diretamente para a página [meus aplicativos](https://apps.dev.microsoft.com) e selecionar o nome do aplicativo para chegar a essa página.

    ![Página do WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Observe a senha dos **segredos do aplicativo** e o **Sid (identificador de segurança do pacote)** .

    >[!WARNING]
    >O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Configurar as definições de WNS para o hub

1. Na categoria **configurações de notificação** , selecione **Windows (WNS)** .
2. Introduza os valores para **SID do Pacote** e **Chave de Segurança** que anotou na secção anterior.
3. Clique em **Guardar** na barra de ferramentas.

    ![Caixas do SID do Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O seu hub de notificação está agora configurado para funcionar com o WNS. Tem as cadeias de ligação para registar a sua aplicação e enviar notificações.

## <a name="create-a-sample-windows-app"></a>Criar uma aplicação do Windows de exemplo

1. No Visual Studio, abra o menu **arquivo** , selecione **novo**e, em seguida, selecione **projeto**.
2. Na caixa de diálogo **criar um novo projeto** , conclua as seguintes etapas:

    1. Na caixa de pesquisa na parte superior, digite **Windows universal**.
    2. Nos resultados da pesquisa, selecione **aplicativo em branco (universal do Windows)** e, em seguida, selecione **Avançar**.

       ![Caixa de diálogo Novo Projeto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. Na caixa de diálogo **configurar seu novo projeto** , insira um **nome de projeto**e um **local** para os arquivos de projeto.
    4. Selecione **Criar**.

3. Aceite as predefinições para as versões de **destino** e **mínimas** da plataforma e selecione **OK**.
4. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto de aplicativo da Windows Store, selecione **publicar**e, em seguida, selecione **associar aplicativo à loja**. É apresentado o assistente **Associar aplicação à Loja Windows**.
5. No assistente, inicie sessão com a sua conta Microsoft.
6. Selecione a aplicação que registou no passo 2, selecione **Seguinte** e, por fim, **Associar**. Desta forma, é adicionado ao manifesto da aplicação as informações de registo da Loja Windows necessárias.
7. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, Selecione **Gerir Pacotes NuGet**. É aberta a janela **Gerir Pacotes NuGet**.
8. Na caixa de pesquisa, introduza **WindowsAzure.Messaging.Managed**, selecione **Instalar** e aceite os termos de utilização.

    ![Janela Gerir Pacotes NuGet][20]

    Esta ação transfere, instala e adiciona uma referência ao à biblioteca dos Hubs de Notificação do Azure para Windows mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Abra o arquivo de projeto `App.xaml.cs` e adicione as seguintes instruções:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. No arquivo de `App.xaml.cs` do projeto, localize a classe `App` e adicione a seguinte definição de método `InitNotificationsAsync`. Substitua `<your hub name>` pelo nome do hub de notificação criado na portal do Azure e substitua `<Your DefaultListenSharedAccessSignature connection string>` pela cadeia de conexão `DefaultListenSharedAccessSignature` da página **políticas de acesso** do hub de notificação:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

    Este código obtém o URI do canal para a aplicação a partir do WNS e, em seguida, regista esse URI de canal no Notification Hub.

    >[!NOTE]
    > Substitua o espaço reservado `hub name` pelo nome do hub de notificação que aparece na portal do Azure. Substitua também o espaço reservado da cadeia de conexão pela cadeia de conexão `DefaultListenSharedAccessSignature` que você obteve na página de **políticas de acesso** do hub de notificação em uma seção anterior.

11. Na parte superior do manipulador de eventos `OnLaunched` no `App.xaml.cs`, adicione a seguinte chamada para o novo método `InitNotificationsAsync`:

    ```csharp
    InitNotificationsAsync();
    ```

    Essa ação garante que o URI do canal seja registrado em seu hub de notificação sempre que o aplicativo for iniciado.

12. Para executar o aplicativo, pressione a tecla **F5** do teclado. Será exibida uma caixa de diálogo contendo a chave de registro. Para fechar a caixa de diálogo, clique em **OK**.

    ![Registado com êxito](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

A aplicação já está pronta para receber notificações de alerta.

## <a name="send-test-notifications"></a>Enviar notificações de teste

Pode testar rapidamente a receção das notificações na sua aplicação ao enviar notificações no [portal do Azure](https://portal.azure.com/).

1. No portal do Azure, mude para o separador Descrição Geral e selecione **Testar Envio** na barra de ferramentas.

    ![Botão Testar Envio](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Na janela **Testar Envio**, efetue as ações abaixo:
    1. Para **Plataformas**, selecione **Windows**.
    2. Para **Tipo de Notificação**, selecione **Alerta**.
    3. Selecione **Enviar**.

        ![Painel Testar Envio](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Veja o resultado da operação Enviar na lista **Resultado** na parte inferior da janela. Também pode ver uma mensagem de alerta.

    ![Resultado da operação Enviar](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Veja a mensagem de notificação: **Mensagem de teste** no ambiente de trabalho.

    ![Mensagem de notificação](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Passos seguintes
Você enviou notificações de difusão para todos os seus dispositivos Windows usando o portal ou um aplicativo de console. Para saber como enviar notificações push para dispositivos específicos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Enviar notificações push para dispositivos específicos](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: https://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: https://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: https://msdn.microsoft.com/library/windows/apps/hh779719.aspx
