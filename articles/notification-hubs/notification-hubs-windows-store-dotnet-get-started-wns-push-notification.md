---
title: Enviar notificações para aplicações da Plataforma Universal do Windows com os Hubs de Notificação do Azure | Microsoft Docs
description: Lean como usar hubs de notificação Azure para empurrar notificações para uma aplicação da Plataforma Universal do Windows.
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
ms.openlocfilehash: f78f24ee68545b386169e29a5a52ccc572849ad7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127064"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações para aplicações da Plataforma Universal Windows utilizando hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Neste tutorial, vai criar um hub de notificação para enviar notificações push para uma aplicação da Plataforma Universal do Windows (UWP). Vai criar uma aplicação da Loja Windows em branco que recebe notificações push com o Serviço de Notificações Push do Windows (WNS). Em seguida, utiliza o seu centro de notificação para transmitir notificações push para todos os dispositivos que estão a executar a sua aplicação.

> [!NOTE]
> O código concluído deste artigo está disponível no [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Dê os seguintes passos:

> [!div class="checklist"]
> * Criar uma aplicação na Loja Windows
> * Criar um hub de notificação
> * Criar uma aplicação do Windows de exemplo
> * Enviar notificações de teste

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura Azure.** Se não tiver uma subscrição Azure, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Microsoft Visual Studio 2017 ou mais tarde. O exemplo neste tutorial utiliza o [Visual Studio 2019.](https://www.visualstudio.com/products)
- [Ferramentas de desenvolvimento de aplicações do UWP instaladas](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Uma conta ativa da Loja Windows
- Confirme que **as notificações de Obter notificações de apps e outros remetentes** estão ativadas. 
    - Janela de **definições** de lançamento no seu computador.
    - Selecione o azulejo **do Sistema.**
    - Selecione **Notificações & ações** do menu esquerdo. 
    - Confirme que as **notificações do Get de apps e outros remetentes** estão ativadas. Se não estiver ativado, ative-o.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Hubs de Notificação para aplicações do UWP.

## <a name="create-an-app-in-windows-store"></a>Criar uma aplicação na Loja Windows

Para enviar notificações push para aplicações do UWP, associe a aplicação à Loja Windows. Em seguida, configure o Hub de Notificação para se integrar no WNS.

1. Navegue para o [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), inicie sessão com a sua conta Microsoft e selecione **Criar uma nova aplicação**.

    ![Botão Nova aplicação](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Escreva um nome para a aplicação e selecione **Reservar nome do produto**. Desta forma, é criado um registo novo da Loja Windows para a sua aplicação.

    ![Nome da aplicação na loja](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Expandir **a gestão do Produto,** selecionar **WNS/MPNS,** e, em seguida, selecionar **o site Live Services**. Inicie sessão com a sua conta Microsoft. A página de inscrição da aplicação abre num novo separador. [My applications](https://apps.dev.microsoft.com)

    ![Página do WNS MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Note a palavra-passe dos **Segredos** de Aplicação e o identificador de **segurança do pacote (SID)**.

    >[!WARNING]
    >O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.

## <a name="create-a-notification-hub"></a>Criar um Centro de Notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Configurar as definições de WNS para o hub

1. Na categoria DEFINIÇÕES DE **NOTIFICAÇÃO,** selecione **Windows (WNS)**.
2. Introduza os valores para **SID do Pacote** e **Chave de Segurança** que anotou na secção anterior.
3. Clique em **Guardar** na barra de ferramentas.

    ![Caixas do SID do Pacote e Chave de Segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

O seu hub de notificação está agora configurado para funcionar com o WNS. Tem as cadeias de ligação para registar a sua aplicação e enviar notificações.

## <a name="create-a-sample-windows-app"></a>Criar uma aplicação do Windows de exemplo

1. No Estúdio Visual, abra o menu **File,** selecione **New**, e, em seguida, selecione **Project**.
2. No diálogo **Criar um novo projeto,** complete os seguintes passos:

    1. Na caixa de pesquisa na parte superior, escreva **o Windows Universal**.
    2. Nos resultados da pesquisa, selecione **Blank App (Universal Windows)** e, em seguida, selecione **Next**.

       ![Caixa de diálogo Novo Projeto](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. No **Configure o seu novo** diálogo de projeto, insira um **nome de Projeto**e um **Local** para os ficheiros do projeto.
    4. Selecione **Criar**.

3. Aceite as predefinições para as versões de **destino** e **mínimas** da plataforma e selecione **OK**.
4. No Solution Explorer, clique no projeto da aplicação da Windows Store, selecione **Publicar**e, em seguida, selecione **Associate App com a Loja**. É apresentado o assistente **Associar aplicação à Loja Windows**.
5. No assistente, inicie sessão com a sua conta Microsoft.
6. Selecione a aplicação que registou no passo 2, selecione **Seguinte** e, por fim, **Associar**. Desta forma, é adicionado ao manifesto da aplicação as informações de registo da Loja Windows necessárias.
7. No Visual Studio, clique com o botão direito do rato na solução e, em seguida, Selecione **Gerir Pacotes NuGet**. É aberta a janela **Gerir Pacotes NuGet**.
8. Na caixa de pesquisa, introduza **WindowsAzure.Messaging.Managed**, selecione **Instalar** e aceite os termos de utilização.

    ![Janela Gerir Pacotes NuGet][20]

    Esta ação transfere, instala e adiciona uma referência ao à biblioteca dos Hubs de Notificação do Azure para Windows mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Abra `App.xaml.cs` o ficheiro do projeto e adicione as seguintes declarações:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. No ficheiro do `App.xaml.cs` projeto, `App` localize a classe `InitNotificationsAsync` e adicione a seguinte definição de método. `<your hub name>` Substitua pelo nome do centro de notificação que `<Your DefaultListenSharedAccessSignature connection string>` criou `DefaultListenSharedAccessSignature` no portal Azure e substitua-a pela cadeia de ligação da página Access **Polices** do seu centro de notificação:

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
    > Substitua `hub name` o espaço reservado pelo nome do centro de notificação que aparece no portal Azure. Substitua também o espaço `DefaultListenSharedAccessSignature` reservado de cadeiade ligação com a cadeia de ligação que obteve na página Access **Polices** do seu centro de notificação numa secção anterior.

11. No topo do `OnLaunched` manipulador `App.xaml.cs`de eventos, adicione `InitNotificationsAsync` a seguinte chamada ao novo método:

    ```csharp
    InitNotificationsAsync();
    ```

    Esta ação garante que o canal URI está registado no seu centro de notificação sempre que a aplicação é lançada.

12. Para executar a aplicação, prima a tecla **F5** do teclado. Será exibida uma caixa de diálogo contendo a chave de registo. Para fechar o diálogo, clique **ok**.

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
Enviou notificações de transmissão para todos os seus dispositivos Windows utilizando o portal ou uma aplicação de consola. Para saber como enviar notificações push para dispositivos específicos, avance para o tutorial seguinte:

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
