---
title: Enviar notificações para dispositivos específicos (Plataforma Universal do Windows) | Microsoft Docs
description: Utilize os Hubs de Notificação do Azure com etiquetas no registo para enviar notícias de última hora para uma aplicação da Plataforma Universal do Windows.
services: notification-hubs
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 8e583ac1c8ac4b6f32c2fa9f8b7ed07c2e7033e8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890003"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Tutorial: Notificações push para dispositivos específicos do Windows que executam aplicações de plataforma Universal do Windows

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como utilizar os Notification Hubs do Azure para difundir notificações de notícias de última hora para aplicações da Loja Windows ou do Windows Phone 8.1 (não Silverlight). Se tiver como objetivo o Windows Phone 8.1 Silverlight, veja a versão para [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Neste tutorial, vai aprender a utilizar os Hubs de Notificação do Azure para enviar notificações para dispositivos Windows específicos que executem aplicações da Plataforma Universal do Windows (UWP). Depois de concluí-lo, pode registar-se em categorias de notícias de última hora em que esteja interessado e receberá notificações push relativas a essas categorias apenas.

Os cenários de difusão são ativados ao incluir uma ou mais *etiquetas* durante a criação de um registo no hub de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos registados para a etiqueta receberão a notificação. Para obter mais informações sobre as etiquetas, veja [Tags in Registrations](notification-hubs-tags-segment-push-message.md) (Etiquetas nos Registos).

> [!NOTE]
> O Visual Studio 2017 não suporta as versões de projetos da Loja Windows e do Windows Phone 8.1 e anteriores. Para obter mais informações, veja [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Segmentação e Compatibilidade de Plataformas do Visual Studio 2017)

Neste tutorial, siga os passos seguintes:

> [!div class="checklist"]
> * Adicionar a seleção de categorias à aplicação móvel
> * Registar-se para receber notificações
> * Enviar notificações com etiquetas
> * Executar a aplicação e gerar notificações

## <a name="prerequisites"></a>Pré-requisitos

Concluir o [Tutorial: Enviar notificações para aplicações da plataforma Universal do Windows com os Hubs de notificação do Azure] [ get-started] antes de começar este tutorial.  

## <a name="add-category-selection-to-the-app"></a>Adicionar a seleção de categorias à aplicação

O primeiro passo é adicionar os elementos da IU à sua página principal existente, de modo a que os utilizadores possam selecionar as categorias em que se vão registar. As categorias selecionadas são armazenadas nos dispositivos. Quando a aplicação é iniciada, é criado um registo do dispositivo no seu hub de notificação com as categorias selecionadas como etiquetas.

1. Abra o ficheiro de projeto de mainpage. XAML e, em seguida, copie o seguinte código no `Grid` elemento:

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

2. Na **Explorador de soluções**, com o botão direito no projeto, adicione uma nova classe: **Notificações**. Adicionar a **pública** modificador à definição de classe e, em seguida, adicione o seguinte `using` instruções para o novo arquivo de código:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Copie o seguinte código para o novo `Notifications` classe:

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

    Esta classe utiliza o armazenamento local para armazenar as categorias de notícias que este dispositivo tem de receber. Em vez de chamar o `RegisterNativeAsync` método, chamada `RegisterTemplateAsync` registar para as categorias com um registo de modelo.

    Se quiser registar mais de um modelo (por exemplo, um para notificações de alerta e outro para mosaicos), indique um nome para o modelo (como "simpleWNSTemplateExample"). Deve dar nomes aos modelos para poder atualizá-los ou eliminá-los.

    >[!NOTE]
    >Se um dispositivo registar vários modelos com a mesma etiqueta, o envio de uma mensagem segmentada para essa etiqueta resulta na entrega de múltiplas notificações no dispositivo (uma por cada modelo). Este comportamento é útil em cenários nos quais a mesma mensagem lógica tem de resultar em várias notificações visuais (que mostrem, por exemplo, um distintivo e um alerta numa aplicação da Loja Windows).

    Para obter mais informações, veja [Templates](notification-hubs-templates-cross-platform-push-messages.md) (Modelos).

4. No arquivo de projeto App.xaml.cs, adicione a seguinte propriedade para o `App` classe:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    Use essa propriedade para criar e aceder a um `Notifications` instância.

    No código, substitua os marcadores de posição `<hub name>` e `<connection string with listen access>` pelo nome do seu hub de notificação e pela cadeia de ligação de *DefaultListenSharedAccessSignature*, que obteve anteriormente.

   > [!NOTE]
   > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, distribua a chave apenas para acesso de *escuta* com a sua aplicação cliente. Com o acesso de escuta, a sua aplicação pode registar-se para receber notificações, mas não é possível modificar registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço de back-end protegido para o envio de notificações e a alteração de registos existentes.

5. Na `MainPage.xaml.cs` de ficheiros, adicione a seguinte linha:

    ```csharp
    using Windows.UI.Popups;
    ```

6. Na `MainPage.xaml.cs` de ficheiros, adicione o seguinte método:

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

    Este método cria uma lista de categorias e utiliza o `Notifications` classe para armazenar a lista no armazenamento local. Também regista as etiquetas correspondentes no seu hub de notificação. Quando as categorias são alteradas, o registo é recriado com as categorias novas.

A aplicação pode agora armazenar um conjunto de categorias no armazenamento local do dispositivo. A aplicação regista-se no hub de notificação sempre que os utilizadores alteram a seleção de categorias.

## <a name="register-for-notifications"></a>Registar-se para receber notificações

Nesta secção, vai utilizar as categorias que foram armazenadas no armazenamento local para fazer o registo no hub de notificação no arranque.

> [!NOTE]
> Tendo em conta que o URI do canal que é atribuído pelo Windows Notification Service (WNS) pode ser alterado em qualquer altura, deve registar-se para receber notificações com frequência, a fim de evitar falhas de notificação. Este exemplo regista-se em notificações sempre que a aplicação é iniciada. Relativamente às aplicações que executa com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo de modo a preservar a largura de banda caso tenha passado menos de um dia desde o registo anterior.

1. Para utilizar o `notifications` classe subscrever com base em categorias, abra o ficheiro App.xaml.cs e, em seguida, atualize o `InitNotificationsAsync` método.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Este processo garante que, sempre que a aplicação é iniciada, obtém as categorias do armazenamento local e pede o registo das mesmas. Que criou o `InitNotificationsAsync` método como parte do [introdução aos Hubs de notificação] [ get-started] tutorial.
2. Na `MainPage.xaml.cs` ficheiros do projeto, adicione o seguinte código para o `OnNavigatedTo` método:

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

A aplicação está agora concluída e pode armazenar um conjunto de categorias no armazenamento local do dispositivo que é utilizado para fazer o registo no hub de notificação quando os utilizadores alteram a seleção das categorias. Na próxima secção, vai definir um back-end apto a enviar notificações de categorias para esta aplicação.

## <a name="send-tagged-notifications"></a>Enviar notificações com etiquetas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Executar a aplicação e gerar notificações

1. No Visual Studio, selecione **F5** para compilar e iniciar a aplicação. A IU da aplicação disponibiliza um conjunto de seletores que lhe permitem escolher as categorias que quer subscrever.

    ![Aplicação de Notícias de Última Hora][1]

2. Ative um ou mais seletores de categorias e clique em **Subscribe** (Subscrever).

    A aplicação converte as categorias selecionadas em etiquetas e pede um novo registo do dispositivo para as etiquetas selecionadas ao hub de notificação. As categorias registadas são devolvidas e apresentadas numa caixa de diálogo.

    ![Seletores de categorias e botão Subscribe][19]

3. Envie uma notificação nova a partir do back-end de uma das seguintes formas:

   * **Aplicação de consola**: Inicie a aplicação de consola.
   * **Java/PHP**: Execute a sua aplicação ou script.

     As notificações das categorias selecionadas são apresentadas como notificações de alerta.

     ![Notificações de alerta][14]

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a difundir notícias de última hora por categoria. A aplicação de back-end envia as notificações com etiquetas para os dispositivos que se registaram para receber notificações para essas etiquetas. Para saber como enviar notificações para utilizadores específicos independentemente do dispositivo que utilizem, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Push localized notifications](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md) (Enviar notificações localizadas)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png
[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png
[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

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
