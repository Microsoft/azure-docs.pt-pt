---
title: Enviar notificações push para dispositivos Windows Phone específicos com os Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar os Hubs de Notificação do Azure para enviar notificações push para dispositivos Windows Phone 8 ou Windows Phone 8.1 específicos (não todos) registados com o back-end da aplicação.
services: notification-hubs
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 10f8c2e21f2dcf8c108576d54fe6776ecf04a0f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861325"
---
# <a name="tutorial-push-notifications-to-specific-windows-phone-devices-by-using-azure-notification-hubs"></a>Tutorial: Notificações push para dispositivos Windows Phone específicos ao utilizar os Hubs de notificação do Azure

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

Este tutorial mostra-lhe como utilizar os Hubs de Notificação do Azure para enviar notificações push para dispositivos Windows Phone 8 ou Windows Phone 8.1 específicos. Se o seu foco incide sobre o Windows Phone 8.1 (não Silverlight), consulte a versão [Universal para Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) deste tutorial.

Para possibilitar este cenário, inclua uma ou mais *etiquetas* quando criar um registo no hub de notificação. Quando as notificações são enviadas para uma etiqueta, todos os dispositivos registados para a etiqueta receberão a notificação. Para obter mais informações sobre etiquetas, consulte [Etiquetas nos registos](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> O SDK dos Notification Hubs do Windows Phone não suporta a utilização do Serviço de Notificações Push do Windows (WNS) com aplicações do Windows Phone 8.1 Silverlight. Para utilizar o WNS (em vez do MPNS) com aplicações para Windows Phone 8.1 Silverlight, siga o [tutorial Hubs de Notificação – Windows Phone Silverlight], que utiliza APIs REST.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar a seleção de categorias à aplicação móvel
> * Registar-se para receber notificações com etiquetas
> * Enviar notificações com etiquetas
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Concluir o [Tutorial: Notificações push para aplicações Windows Phone ao utilizar os Hubs de notificação do Azure](notification-hubs-windows-mobile-push-notifications-mpns.md). Neste tutorial, irá atualizar a aplicação móvel de modo a poder registar-se para receber categorias de notícias de última hora do seu interesse e receber apenas notificações push relativas a essas categorias.

## <a name="add-category-selection-to-the-mobile-app"></a>Adicionar a seleção de categorias à aplicação móvel

O primeiro passo consiste em adicionar os elementos de IU à sua página principal existente que permitem ao utilizador selecionar as categorias a registar. As categorias selecionadas por um utilizador são armazenadas no dispositivo. Quando a aplicação é iniciada, é criado o registo do dispositivo no seu Hub de Notificação com as categorias selecionadas como etiquetas.

1. Abra o `MainPage.xaml` de ficheiros, em seguida, substitua o `Grid` elementos com o nome `TitlePanel` e `ContentPanel` com o código a seguir:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Adicionar uma classe chamada `Notifications` ao projeto. Adicionar o `public` modificador à definição de classe. Em seguida, adicione o seguinte `using` instruções para o novo ficheiro:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Copie o seguinte código para o novo `Notifications` classe:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Esta classe utiliza o armazenamento isolado para armazenar as categorias de notícias que este dispositivo irá receber. Também contém métodos com vista a registar-se para receber estas categorias através de um registo de notificação [modelo](notification-hubs-templates-cross-platform-push-messages.md).
4. Na `App.xaml.cs` ficheiros do projeto, adicione a seguinte propriedade para o `App` classe. Substitua os marcadores de posição `<hub name>` e `<connection string with listen access>` pelo nome do seu hub de notificação e pela cadeia de ligação de *DefaultListenSharedAccessSignature* que obteve anteriormente.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Uma vez que, de um modo geral, as credenciais que são distribuídas com uma aplicação cliente não são seguras, só deve distribuir a chave para acesso de escuta com a sua aplicação cliente. O acesso de escuta permite que a sua aplicação seja registada para receber notificações, mas não é possível modificar os registos existentes nem enviar notificações. A chave de acesso total é utilizada num serviço back-end protegido para enviar notificações e alterar registos existentes.

5. Na `MainPage.xaml.cs`, adicione a seguinte linha:

    ```csharp
    using Windows.UI.Popups;
    ```
6. No ficheiro de projeto MainPage.xaml.cs, adicione o seguinte método:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Este método cria uma lista de categorias e utiliza o `Notifications` etiquetas de classe para armazenar a lista no armazenamento local e registe-se de que o correspondente com o seu hub de notificação. Quando as categorias são alteradas, o registo é recriado com as novas categorias.

A sua aplicação pode agora armazenar um conjunto de categorias no armazenamento local do dispositivo e ficar registada no Hub de Notificação sempre que o utilizador alterar a seleção das categorias.

## <a name="register-for-notifications"></a>Registar-se para receber notificações

Estes passos efetuam o registo através do hub de notificação no arranque mediante a utilização das categorias que foram armazenadas no armazenamento local.

> [!NOTE]
> Tendo em conta que o URI do canal atribuído pelo Serviço de Notificações Push da Microsoft (MPNS) pode ser alterado em qualquer altura, deve registar-se para receber notificações com frequência a fim de evitar falhas de notificação. Este exemplo efetua o registo para receber notificações sempre que a aplicação é iniciada. Relativamente às aplicações executadas com frequência, ou seja, mais do que uma vez por dia, pode provavelmente ignorar o registo para poupar a largura de banda, caso tenha passado menos de um dia desde o registo anterior.

1. Abra o ficheiro App.xaml.cs e adicione a `async` modificador para `Application_Launching` método e substitua o registo dos Notification Hubs código que adicionou no [introdução aos Hubs de notificação] com o código a seguir:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Este código garante que, sempre que a aplicação é iniciada, as categorias são obtidas a partir do armazenamento local e é pedido o registo destas categorias.
2. No arquivo de projeto MainPage.xaml.cs, adicione o seguinte código que implementa o `OnNavigatedTo` método:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Este código atualiza a página principal com base no estado das categorias guardadas anteriormente.

A aplicação está agora completa, sendo capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para o efetuar o registo através do hub de notificação sempre que o utilizador alterar a seleção de categorias. Em seguida, defina um back-end apto a enviar notificações de categoria para esta aplicação.

## <a name="send-tagged-notifications"></a>Enviar notificações com etiquetas

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testar a aplicação

1. No Visual Studio, prima F5 para compilar e iniciar a aplicação.

    ![Aplicação móvel com categorias][1]

    A IU da aplicação fornece um conjunto de seletores que lhe permite escolher as categorias que quer subscrever.
2. Ative um ou mais seletores de categorias e, em seguida, clique em **Subscrever**.

    A aplicação converte as categorias selecionadas em etiquetas e pede um novo registo do dispositivo para as etiquetas selecionadas ao hub de notificação. As categorias registadas são devolvidas e apresentadas numa caixa de diálogo.

    ![Mensagem de subscrição concluída][2]
3. Depois de receber uma confirmação de que a subscrição das categorias foi concluída, execute a aplicação de consola para enviar notificações para cada categoria. Certifique-se de que apenas recebe uma notificação para as categorias que subscreveu.

    ![Mensagem de notificação][3]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a enviar notificações push para dispositivos específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações push para utilizadores específicos que podem utilizar vários dispositivos, avance para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Push notifications to specific users](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) (Enviar notificações para utilizadores específicos)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Introdução aos Hubs de Notificação]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??
