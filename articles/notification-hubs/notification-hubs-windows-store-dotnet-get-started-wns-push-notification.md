---
title: Enviar notificações para aplicações da Plataforma Universal do Windows com os Hubs de Notificação do Azure | Microsoft Docs
description: Incline-se como usar os Hubs de Notificação Azure para empurrar notificações para uma aplicação da Plataforma Universal do Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 4f55b6eafe230f722979d535111ce45aa35981f0
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125042"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações para apps da Plataforma Universal windows usando hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Neste tutorial, vai criar um hub de notificação para enviar notificações push para uma aplicação da Plataforma Universal do Windows (UWP). Vai criar uma aplicação da Loja Windows em branco que recebe notificações push com o Serviço de Notificações Push do Windows (WNS). Em seguida, utilize o seu centro de notificações para transmitir notificações push para todos os dispositivos que estão a executar a sua aplicação.

> [!NOTE]
> O código concluído deste artigo está disponível no [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Tome os seguintes passos:

> [!div class="checklist"]
> * Criar uma aplicação na Loja Windows
> * Criar um hub de notificação
> * Criar uma aplicação do Windows de exemplo
> * Enviar notificações de teste

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura Azure** . Se não tiver uma subscrição do Azure, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Microsoft Visual Studio 2017 ou posterior. O exemplo neste tutorial utiliza [o Visual Studio 2019](https://www.visualstudio.com/products).
- [Ferramentas de desenvolvimento de aplicações do UWP instaladas](/windows/uwp/get-started/get-set-up)
- Uma conta ativa da Loja Windows
- Confirme que **a definição de notificações de apps e outros remetentes** está ativada. 
    - Janela **de definições de** lançamento no computador.
    - Selecione o **azulejo do sistema.**
    - Selecione **Notificações & ações** do menu esquerdo. 
    - Confirme que as **notificações de apps e outros remetentes estão** ativadas. Se não estiver ativado, ative-o.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Hubs de Notificação para aplicações do UWP.

## <a name="create-an-app-in-windows-store"></a>Criar uma aplicação na Loja Windows

Para enviar notificações push para aplicações do UWP, associe a aplicação à Loja Windows. Em seguida, configure o Hub de Notificação para se integrar no WNS.

1. Navegue para o [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), inicie sessão com a sua conta Microsoft e selecione **Criar uma nova aplicação** .

    ![Botão Nova aplicação](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Escreva um nome para a aplicação e selecione **Reservar nome do produto** . Desta forma, é criado um registo novo da Loja Windows para a sua aplicação.

    ![Nome da aplicação na loja](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Expandir **a gestão do produto,** selecione **WNS/MPNS,** e, em seguida, selecione **site de Serviços ao Vivo** . Inicie sessão com a sua conta Microsoft. A página de registo de inscrição abre num novo separador. Em alternativa, pode navegar diretamente para a página [das minhas aplicações](https://apps.dev.microsoft.com) e selecionar o nome da sua candidatura para chegar a esta página.

    ![Página do WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Note a palavra-passe **'Segredos de Aplicação',** bem como o **identificador de segurança do pacote (SID)** e **a identidade da aplicação** na secção Windows Store.

    >[!WARNING]
    >O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.

## <a name="create-a-notification-hub"></a>Criar um centro de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Configurar as definições de WNS para o hub

1. Na categoria **DEFINIÇÕES DE NOTIFICAÇÃO,** selecione **Windows (WNS)** .
2. Introduza os valores para **SID do Pacote** e **Chave de Segurança** que anotou na secção anterior.
3. Clique em **Guardar** na barra de ferramentas.

    ![Caixas do SID do Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O seu hub de notificação está agora configurado para funcionar com o WNS. Tem as cadeias de ligação para registar a sua aplicação e enviar notificações.

## <a name="create-a-sample-windows-app"></a>Criar uma aplicação do Windows de exemplo

1. No Estúdio Visual, abra o menu **Ficheiro,** selecione **Novo** e, em seguida, selecione **Project** .
2. No **Criar um novo** diálogo de projeto, complete os seguintes passos:

    1. Na caixa de pesquisa no topo, escreva **Windows Universal** .
    2. Nos resultados da pesquisa, selecione **Blank App (Universal Windows)** e, em seguida, selecione **Next** .

       ![Caixa de diálogo Novo Projeto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. No **Configure** o seu novo diálogo de projeto, insira um **nome de Projeto** e uma **Localização** para os ficheiros do projeto.
    4. Selecione **Criar** .

3. Aceite as predefinições para as versões de **destino** e **mínimas** da plataforma e selecione **OK** .
4. No Solution Explorer, clique com o botão direito no projeto de aplicações da Windows Store, **selecione Publish** e, em seguida, selecione **Associate App com a Loja** . É apresentado o assistente **Associar aplicação à Loja Windows** .
5. No assistente, inicie sessão com a sua conta Microsoft.
6. Selecione a aplicação que registou no passo 2, selecione **Seguinte** e, por fim, **Associar** . Desta forma, é adicionado ao manifesto da aplicação as informações de registo da Loja Windows necessárias.
7. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, Selecione **Gerir Pacotes NuGet** . É aberta a janela **Gerir Pacotes NuGet** .
8. Na caixa de pesquisa, introduza **WindowsAzure.Messaging.Managed** , selecione **Instalar** e aceite os termos de utilização.

    ![Janela Gerir Pacotes NuGet][20]

    Esta ação transfere, instala e adiciona uma referência ao à biblioteca dos Hubs de Notificação do Azure para Windows mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Abra o ficheiro do `App.xaml.cs` projeto e adicione as seguintes declarações:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. No ficheiro do `App.xaml.cs` projeto, localize a `App` classe e adicione a seguinte `InitNotificationsAsync` definição de método. `<your hub name>`Substitua-o pelo nome do centro de notificação que criou no portal Azure e substitua-o `<Your DefaultListenSharedAccessSignature connection string>` pela cadeia de `DefaultListenSharedAccessSignature` ligação da página Access **Polices** do seu centro de notificação:

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
    > Substitua o `hub name` espaço reservado pelo nome do hub de notificação que aparece no portal Azure. Substitua também o espaço reservado de ligação com a `DefaultListenSharedAccessSignature` cadeia de ligação que obteve na página **Access Polices** do seu centro de notificação numa secção anterior.

11. No topo do manipulador de `OnLaunched` eventos em `App.xaml.cs` , adicione a seguinte chamada ao novo `InitNotificationsAsync` método:

    ```csharp
    InitNotificationsAsync();
    ```

    Esta ação garante que o canal URI está registado no seu centro de notificações sempre que a aplicação for lançada.

12. Clique com o botão direito `Package.appxmanifest` e selecione Ver Código **(F7).** Localize `<Identity .../>` e substitua o valor pela Identidade de **Aplicação** do WNS que criou [anteriormente.](#create-an-app-in-windows-store)

13. Para executar a aplicação, prima a tecla **F5** do teclado. Será apresentada uma caixa de diálogo que contenha a chave de registo. Para fechar o diálogo, clique **em OK** .

    ![Registado com êxito](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

A aplicação já está pronta para receber notificações de alerta.

## <a name="send-test-notifications"></a>Enviar notificações de teste

Pode testar rapidamente a receção das notificações na sua aplicação ao enviar notificações no [portal do Azure](https://portal.azure.com/).

1. No portal do Azure, mude para o separador Descrição Geral e selecione **Testar Envio** na barra de ferramentas.

    ![Botão Testar Envio](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Na janela **Testar Envio** , efetue as ações abaixo:
    1. Para **Plataformas** , selecione **Windows** .
    2. Para **Tipo de Notificação** , selecione **Alerta** .
    3. Selecione **Send** (Enviar).

        ![Painel Testar Envio](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Veja o resultado da operação Enviar na lista **Resultado** na parte inferior da janela. Também pode ver uma mensagem de alerta.

    ![Resultado da operação Enviar](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Veja a mensagem de notificação: **Mensagem de teste** no ambiente de trabalho.

    ![Mensagem de notificação](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Passos seguintes
Enviou notificações de transmissão a todos os seus dispositivos Windows utilizando o portal ou uma aplicação para consolas. Para saber como enviar notificações push para dispositivos específicos, avance para o tutorial seguinte:

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
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
