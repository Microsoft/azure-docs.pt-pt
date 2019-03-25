---
title: Enviar notificações localizadas para aplicações do Windows com os Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como utilizar os Hubs de Notificação do Azure para enviar notificações de notícias de última localizadas.
services: notification-hubs
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: jowargo
ms.openlocfilehash: f3880db813072ca0bcecf073a8db24b21c87189f
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402717"
---
# <a name="tutorial-push-localized-notifications-to-windows-apps-by-using-azure-notification-hubs"></a>Tutorial: Notificações push localizada para aplicações do Windows ao utilizar os Hubs de notificação do Azure

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como enviar notificações localizadas para dispositivos móveis registados no serviço Hubs de Notificação. No tutorial, Atualize as aplicações criadas no [Tutorial: Enviar notificações para dispositivos específicos (plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) para suportar os cenários seguintes:

- A aplicação Loja Windows permite que os dispositivos cliente especifiquem um idioma e subscrevam diferentes categorias de notícias de última hora.
- A aplicação de back-end difunde notificações, utilizando o **tag** e **modelo** funcionalidades dos Hubs de notificação do Azure.

Quando concluir o tutorial, a aplicação móvel vai permitir-lhe registar-se em categorias nas quais está interessado, bem como especificar um idioma no qual as notificações vão ser recebidas. A aplicação de back-end envia notificações que estão localizadas por idioma e dispositivo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação do Windows para suportar informações de região
> * Atualizar a aplicação de back-end para enviar notificações localizadas
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Concluir o [Tutorial: Enviar notificações para dispositivos específicos (plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

Na [Tutorial: Enviar notificações para dispositivos específicos (plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md), criou uma aplicação que utilizada **etiquetas** subscrever notificações de notícias diferentes **categorias**. Neste tutorial, vai utilizar a funcionalidade **modelo** dos Hubs de Notificação para entregar facilmente notificações de notícias de última hora **localizadas**.

A um nível alto, os modelos são uma forma de especificar o formato no qual um dispositivo específico deverá receber as notificações. O modelo especifica o formato de payload exato ao consultar as propriedades que fazem parte da mensagem enviada pelo back-end da sua aplicação. Neste tutorial, a aplicação de back-end envia uma mensagem independente em termos de região que contém todos os idiomas suportados:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Os dispositivos são registados num modelo que consulta a propriedade correta. Por exemplo, se uma aplicação da Loja Windows que pretenda receber uma mensagem de alerta em inglês regista-se no modelo seguinte, com todas as etiquetas correspondentes:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Para saber mais sobre modelos, veja [Push modelos](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="update-windows-app-to-support-locale-information"></a>Atualizar a aplicação do Windows para suportar informações de região

1. Abra a solução do Visual Studio criou para o [Tutorial: Enviar notificações para dispositivos específicos (plataforma Universal do Windows)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).
2. Atualizar a solução `MainPage.xaml` arquivo para incluir uma caixa de combinação de localidade:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. Na `Notifications` , adicione um parâmetro de localidade para o `StoreCategoriesAndSubscribe` e `SubscribeToCategories` métodos.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    Em vez de chamar o `RegisterNativeAsync` método, chamada `RegisterTemplateAsync`. Vai registar um formato de notificações específico no qual o modelo depende da região. Vai igualmente indicar um nome para o modelo ("localizedWNSTemplateExample"), porque poderá pretender registar mais de um modelo (por exemplo, um modelo para notificações de alerta e outro para mosaicos). Também tem de dar um nome a esses modelos para atualizá-los ou eliminá-los.

    Se um dispositivo registar vários modelos com a mesma etiqueta, o envio de uma mensagem segmentada para essa etiqueta resulta na entrega de múltiplas notificações no dispositivo (uma por cada modelo). Este comportamento é útil em cenários nos quais a mesma mensagem lógica tem de resultar em várias notificações visuais que mostrem, por exemplo, um distintivo e um alerta a uma aplicação da Loja Windows.
4. Adicione o seguinte método para obter a região armazenada:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. Na `MainPage.xaml.cs` ficheiro, a atualização o clique no botão manipulador para recuperar o valor atual da caixa de combinação de localidade e fornecê-lo para a chamada para o `Notifications` classe:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. Por fim, no `App.xaml.cs` de ficheiros, atualize o `InitNotificationsAsync` método para recuperar a Localidade e utilizá-lo ao subscrever:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>Executar a aplicação de UWP

1. Execute a aplicação plataforma Universal do Windows. Aguarde até ver a mensagem **Registration successful** (Registo bem-sucedido).

    ![Aplicações móveis e registo](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Selecione as **categorias** e a **região**, e clique em **Subscribe** (Subscrever). A aplicação converte as categorias selecionadas em etiquetas e pede um novo registo do dispositivo para as etiquetas selecionadas ao hub de notificação.

    ![Aplicação móvel](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. Verá uma mensagem de **confirmação** sobre as **subscrições**.

    ![Mensagem de subscrição](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Atualizar aplicação de consola para enviar notificações localizadas

Quando envia notificações de modelos, só tem de indicar um conjunto de propriedades. Neste tutorial, a aplicação de back-end envia o conjunto de propriedades que contêm a versão localizada das notícias atuais, por exemplo:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

Nesta secção, vai atualizar o projeto de aplicação de consola na solução. Modifique o método `SendTemplateNotificationAsync` na aplicação de consola que criou anteriormente com o seguinte código:

> [!IMPORTANT]
> Especifique o nome e a cadeia de ligação com acesso total ao seu hub de notificações no código.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Esta simple chamada fornece notícias para a informação localizada **todos os** dispositivos, deixou da plataforma, como o Hub de notificação baseia-se e oferece o payload nativo correto para todos os dispositivos subscrito uma etiqueta específica.

## <a name="run-console-app-to-send-localized-notification"></a>Executar a aplicação de consola para enviar notificações localizadas
Executar o **aplicação de consola** enviar notificações para cada categoria e em cada idioma suportado. Verifique se só recebe uma notificação para as categorias que subscreveu e que a mensagem se destina à região que selecionou.

![Mensagens de notificação](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a enviar notificações localizadas para dispositivos específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações para utilizadores específicos que podem utilizar vários dispositivos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Push notifications to specific users](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) (Enviar notificações para utilizadores específicos)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
