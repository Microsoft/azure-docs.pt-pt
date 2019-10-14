---
title: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows) | Microsoft Docs
description: Utilize os Hubs de Notificação do Azure com etiquetas no registo para enviar notícias de última hora para uma aplicação da Plataforma Universal do Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: aa6714729e48ab63957b5f9a69c5c064c3c34df6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296728"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Tutorial: Enviar notificações para dispositivos Windows específicos que executem aplicações da Plataforma Universal do Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os hubs de notificação do Azure para transmitir notificações de últimas notícias. Este tutorial aborda aplicativos da Windows Store ou Windows Phone 8,1 (não Silverlight). Se você estiver visando Windows Phone 8,1 Silverlight, consulte [notificações por push para dispositivos Windows Phone específicos usando os hubs de notificação do Azure](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Neste tutorial, você aprenderá a usar os hubs de notificação do Azure para enviar notificações por push para dispositivos Windows específicos que executam um aplicativo Plataforma Universal do Windows (UWP). Depois de concluir o tutorial, você pode se registrar para as categorias de últimas notícias nas quais você está interessado. Você receberá notificações por push apenas para essas categorias.

Para habilitar cenários de difusão, inclua uma ou mais *marcas* ao criar um registro no Hub de notificação. Quando as notificações são enviadas a uma marca, todos os dispositivos registrados para a marca recebem a notificação. Para obter mais informações sobre marcas, consulte [expressões de marca e de roteamento](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> As versões 8,1 e anteriores do projeto do Windows Store e do Windows Phone não têm suporte no Visual Studio 2019. Para obter mais informações, consulte [direcionamento e compatibilidade da plataforma Visual Studio 2019](/visualstudio/releases/2019/compatibility).

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar a seleção de categorias à aplicação móvel
> * Registar-se para receber notificações
> * Enviar notificações marcadas
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Conclua o [tutorial: enviar notificações para plataforma universal do Windows aplicativos usando os hubs de notificação do Azure][get-started] antes de iniciar este tutorial.  

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

O primeiro passo é adicionar os elementos da IU à sua página principal existente, de modo a que os utilizadores possam selecionar as categorias em que se vão registar. As categorias selecionadas são armazenadas nos dispositivos. Quando o aplicativo é iniciado, ele cria um registro de dispositivo em seu hub de notificação, com as categorias selecionadas como marcas.

1. Abra o arquivo de projeto *MainPage. XAML* e copie o seguinte código no elemento `Grid`:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto, selecione **Adicionar** **classe** > . Em **Adicionar novo item**, nomeie as *notificações*de classe e selecione **Adicionar**. Se necessário, adicione o modificador `public` à definição de classe.

1. Adicione as seguintes instruções `using` ao novo arquivo:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Copie o código a seguir para a nova classe `Notifications`:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Esta classe utiliza o armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Em vez de chamar o método `RegisterNativeAsync`, chame `RegisterTemplateAsync` para se registrar para as categorias usando um registro de modelo.

    Se você quiser registrar mais de um modelo, forneça um nome de modelo, por exemplo, *simpleWNSTemplateExample*. Deve dar nomes aos modelos para poder atualizá-los ou eliminá-los. Você pode registrar mais de um modelo para ter um para notificações do sistema e outro para blocos.

    >[!NOTE]
    > Com os hubs de notificação, um dispositivo pode registrar vários modelos usando a mesma marca. Nesse caso, uma mensagem de entrada direcionada à marca resulta em várias notificações entregues ao dispositivo, uma para cada modelo. Esse processo permite que você exiba a mesma mensagem em várias notificações visuais, como um emblema e uma notificação do sistema em um aplicativo da Windows Store.

    Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

1. No arquivo de projeto *app.XAML.cs* , adicione a seguinte propriedade à classe `App`:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Você usa essa propriedade para criar e acessar uma instância `Notifications`.

    No código, substitua os marcadores de posição `<hub name>` e `<connection string with listen access>` pelo nome do seu hub de notificação e pela cadeia de ligação de **DefaultListenSharedAccessSignature**, que obteve anteriormente.

   > [!NOTE]
   > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, distribua a chave apenas para acesso de *escuta* com a sua aplicação cliente. Com o acesso de escuta, a sua aplicação pode registar-se para receber notificações, mas não é possível modificar registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço de back-end protegido para o envio de notificações e a alteração de registos existentes.

1. No arquivo *MainPage.XAML.cs* , adicione a seguinte linha:

    ```csharp
    using Windows.UI.Popups;
    ```

1. No arquivo *MainPage.XAML.cs* , adicione o seguinte método:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Esse método cria uma lista de categorias e usa a classe `Notifications` para armazenar a lista no armazenamento local. Também regista as etiquetas correspondentes no seu hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

A aplicação pode agora armazenar um conjunto de categorias no armazenamento local do dispositivo. A aplicação regista-se no hub de notificação sempre que os utilizadores alteram a seleção de categorias.

## <a name="register-for-notifications"></a>Registar-se para receber notificações

Nesta secção, vai utilizar as categorias que foram armazenadas no armazenamento local para fazer o registo no hub de notificação no arranque.

> [!NOTE]
> Tendo em conta que o URI do canal que é atribuído pelo Windows Notification Service (WNS) pode ser alterado em qualquer altura, deve registar-se para receber notificações com frequência, a fim de evitar falhas de notificação. Este exemplo é registado para notificação sempre que a aplicação é iniciada. Para aplicativos que você executa com frequência, digamos, mais de uma vez por dia, você pode, provavelmente, ignorar o registro para preservar a largura de banda se menos de um dia tiver passado desde o registro anterior.

1. Para usar a classe `notifications` para assinar com base em categorias, abra o arquivo *app.XAML.cs* e, em seguida, atualize o método `InitNotificationsAsync`.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Esse processo garante que, quando o aplicativo for iniciado, ele recuperará as categorias do armazenamento local. Em seguida, ele solicita o registro dessas categorias. Você criou o método `InitNotificationsAsync` como parte do tutorial [enviar notificações para plataforma universal do Windows aplicativos usando os hubs de notificação do Azure][get-started] .
2. No arquivo de projeto *MainPage.XAML.cs* , adicione o seguinte código ao método `OnNavigatedTo`:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Este código atualiza a página principal com base no estado das categorias guardadas anteriormente.

A aplicação está agora concluída e Ele pode armazenar um conjunto de categorias no armazenamento local do dispositivo. Quando os usuários alteram a seleção de categoria, as categorias salvas são usadas para registrar com o Hub de notificação. Na próxima secção, vai definir um back-end apto a enviar notificações de categorias para esta aplicação.

## <a name="run-the-uwp-app"></a>Executar o aplicativo UWP

1. No Visual Studio, selecione F5 para compilar e iniciar o aplicativo. A IU da aplicação disponibiliza um conjunto de seletores que lhe permitem escolher as categorias que quer subscrever.

   ![Aplicação de Notícias de Última Hora](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Habilite uma ou mais alternâncias de categoria e, em seguida, selecione **assinar**.

   A aplicação converte as categorias selecionadas em etiquetas e pede um novo registo do dispositivo para as etiquetas selecionadas do Hub de Notificação. O aplicativo exibe as categorias registradas em uma caixa de diálogo.

    ![Seletores de categorias e botão Subscribe](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Criar um aplicativo de console para enviar notificações marcadas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Executar o aplicativo de console para enviar notificações marcadas

Execute o aplicativo criado na seção anterior. As notificações das categorias selecionadas são apresentadas como notificações de alerta.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a difundir notícias de última hora por categoria. O aplicativo de back-end envia notificações marcadas para dispositivos que se registraram para receber notificações para essa marca. Para saber como enviar notificações por push a usuários específicos independentemente do dispositivo que eles usam, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
> [Enviar notificações localizadas por push para aplicativos do Windows usando os hubs de notificação do Azure](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
