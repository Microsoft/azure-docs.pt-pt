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
ms.custom: mvc, devx-track-csharp
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 2c77eba69fd914e8ecc7d08a1b16f61ceefe101b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92320580"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Tutorial: Enviar notificações para dispositivos específicos que executam aplicações da Plataforma Universal Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como usar os Hubs de Notificação Azure para transmitir notificações de notícias de última hora. Este tutorial abrange aplicações Windows Store ou Windows Phone 8.1 (não-Silverlight). Se estiver a direcionar o Windows Phone 8.1 Silverlight, consulte [notificações push para dispositivos específicos do Windows Phone utilizando os Hubs de Notificação Azure](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Neste tutorial, aprende a usar os Hubs de Notificação do Azure para empurrar notificações para dispositivos Windows específicos que executam uma aplicação Universal Windows Platform (UWP). Depois de completar o tutorial, pode inscrever-se nas categorias de notícias de última hora que lhe interessam. Receberá notificações push apenas para essas categorias.

Para ativar cenários de transmissão, inclua uma ou mais *tags* quando criar uma inscrição no centro de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos registados para a etiqueta recebem a notificação. Para obter mais informações sobre etiquetas, consulte [expressões de encaminhamento e etiquetas](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> As versões de projeto 8.1 e anteriores do Windows Store e do Windows Phone não são suportadas no Visual Studio 2019. Para mais informações, consulte [Visual Studio 2019 Plataforma Targeting and Compatibilidade.](/visualstudio/releases/2019/compatibility)

Neste tutorial, faz as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar a seleção de categorias à aplicação móvel
> * Registar-se para receber notificações
> * Enviar notificações marcadas
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua [Tutorial: Enviar notificações para aplicações da Plataforma Universal do Windows com os Hubs de Notificação do Azure][get-started].  

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

O primeiro passo é adicionar os elementos da IU à sua página principal existente, de modo a que os utilizadores possam selecionar as categorias em que se vão registar. As categorias selecionadas são armazenadas nos dispositivos. Quando a aplicação começa, cria um registo de dispositivo no seu centro de notificações, com as categorias selecionadas como tags.

1. Abra o ficheiro de projeto *MainPage.xaml* e, em seguida, copie o seguinte código no `Grid` elemento:

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

1. No **Solution Explorer,** clique com o botão direito no projeto, selecione **Add**  >  **Class**. In **Add New Item**, nomeie as *notificações de* classe e selecione **Adicionar**. Se necessário, adicione o `public` modificador à definição de classe.

1. Adicione as `using` seguintes declarações ao novo ficheiro:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Copie o seguinte código para a nova `Notifications` classe:

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

    Esta classe utiliza o armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Em vez de ligar para o `RegisterNativeAsync` método, ligue `RegisterTemplateAsync` para se registar para as categorias usando um registo de modelo.

    Se quiser registar mais de um modelo, forneça um nome de modelo, por exemplo, *simpleWNSTemplateExample*. Deve dar nomes aos modelos para poder atualizá-los ou eliminá-los. Você pode registar mais de um modelo para ter um para notificações de torradas e um para azulejos.

    >[!NOTE]
    > Com os Centros de Notificação, um dispositivo pode registar vários modelos utilizando a mesma etiqueta. Neste caso, uma mensagem de entrada que visa a etiqueta resulta em múltiplas notificações a serem entregues no dispositivo, uma para cada modelo. Este processo permite-lhe exibir a mesma mensagem em várias notificações visuais, como tanto como um crachá como como uma notificação de torradas numa aplicação da Windows Store.

    Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

1. No ficheiro de projeto *App.xaml.cs,* adicione a seguinte propriedade à `App` classe:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Você usa esta propriedade para criar e aceder a um `Notifications` caso.

    No código, substitua os marcadores de posição `<hub name>` e `<connection string with listen access>` pelo nome do seu hub de notificação e pela cadeia de ligação de **DefaultListenSharedAccessSignature**, que obteve anteriormente.

   > [!NOTE]
   > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, distribua a chave apenas para acesso de *escuta* com a sua aplicação cliente. Com o acesso de escuta, a sua aplicação pode registar-se para receber notificações, mas não é possível modificar registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço de back-end protegido para o envio de notificações e a alteração de registos existentes.

1. No ficheiro *MainPage.xaml.cs,* adicione a seguinte linha:

    ```csharp
    using Windows.UI.Popups;
    ```

1. No ficheiro *MainPage.xaml.cs,* adicione o seguinte método:

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

    Este método cria uma lista de categorias e utiliza a `Notifications` classe para armazenar a lista no armazenamento local. Também regista as etiquetas correspondentes no seu hub de notificação. Quando as categorias mudam, o registo é recriada com as novas categorias.

A aplicação pode agora armazenar um conjunto de categorias no armazenamento local do dispositivo. A aplicação regista-se no hub de notificação sempre que os utilizadores alteram a seleção de categorias.

## <a name="register-for-notifications"></a>Registar-se para receber notificações

Nesta secção, vai utilizar as categorias que foram armazenadas no armazenamento local para fazer o registo no hub de notificação no arranque.

> [!NOTE]
> Tendo em conta que o URI do canal que é atribuído pelo Windows Notification Service (WNS) pode ser alterado em qualquer altura, deve registar-se para receber notificações com frequência, a fim de evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Para aplicações que você execute frequentemente, por exemplo, mais de uma vez por dia, você provavelmente pode saltar o registo para preservar a largura de banda se menos de um dia passou desde o registo anterior.

1. Para utilizar a `notifications` classe para subscrever com base em categorias, abra o ficheiro *App.xaml.cs* e, em seguida, atualize o `InitNotificationsAsync` método.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Este processo garante que, quando a aplicação começa, recupera as categorias do armazenamento local. Em seguida, solicita o registo destas categorias. Criou o `InitNotificationsAsync` método como parte das [notificações enviar para as aplicações da Plataforma Universal windows utilizando o tutorial de Azure Notification Hubs.][get-started]
2. No ficheiro de projeto *MainPage.xaml.cs,* adicione o seguinte código ao `OnNavigatedTo` método:

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

A aplicação está agora concluída e Pode armazenar um conjunto de categorias no armazenamento local do dispositivo. Quando os utilizadores alteram a seleção da categoria, as categorias guardadas são utilizadas para se registarem no centro de notificação. Na próxima secção, vai definir um back-end apto a enviar notificações de categorias para esta aplicação.

## <a name="run-the-uwp-app"></a>Executar a aplicação UWP

1. No Visual Studio, selecione F5 para compilar e iniciar a aplicação. A IU da aplicação fornece um conjunto de botões de alternar que lhe permite selecionar as categorias que pretende subscrever.

   ![Aplicação de Notícias de Última Hora](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Ative um ou mais alternadores de categorias e, em seguida, **selecione Subscrever**.

   A aplicação converte as categorias selecionadas em etiquetas e pede um novo registo do dispositivo para as etiquetas selecionadas ao hub de notificação. A aplicação exibe as categorias registadas numa caixa de diálogo.

    ![Seletores de categorias e botão Subscribe](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Criar uma aplicação de consola para enviar notificações marcadas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>Executar a aplicação de consola para enviar notificações marcadas

Execute a aplicação criada na secção anterior. As notificações das categorias selecionadas são apresentadas como notificações de alerta.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a difundir notícias de última hora por categoria. A aplicação back-end empurra notificações marcadas para dispositivos que se registaram para receber notificações para essa etiqueta. Para saber como empurrar notificações para utilizadores específicos independentemente do dispositivo que usam, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Empurre notificações localizadas para apps do Windows utilizando hubs de notificação do Azure](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

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
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)