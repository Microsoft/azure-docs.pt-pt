---
title: Enviar notificações para utilizadores específicos com Hubs de Notificação do Azure | Microsoft Docs
description: Saiba como enviar notificações para utilizadores específicos com as aplicações de Plataforma Universal do Windows (UWP).
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: jowargo
ms.openlocfilehash: 32714b3e5a5ed859716faef2ca660f8b2c90b089
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402513"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Tutorial: Enviar notificações a utilizadores específicos com Notification Hubs do Azure

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como utilizar os Hubs de Notificação do Azure para enviar notificações push para um utilizador específico da aplicação num dispositivo específico. Um back-end de ASP.NET WebAPI é utilizado para autenticar clientes. Quando o back-end autentica um utilizador de aplicação de cliente, adiciona automaticamente uma etiqueta ao registo de notificação. O back-end utiliza esta etiqueta para enviar notificações para o utilizador específico.

> [!NOTE]
> O código concluído deste tutorial pode ser encontrado no [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers).

Neste tutorial, siga os seguintes passos:

> [!div class="checklist"]
> * Criar o Projeto WebAPI
> * Autenticar clientes no back-end de WebAPI
> * Utilizar o back-end de WebAPI para registar notificações
> * Enviar notificações a partir do back-end de WebAPI
> * Publicar o back-end de WebAPI novo
> * Atualizar o código do projeto de cliente
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial baseia-se no hub de notificação e projeto do Visual Studio que criou no [Tutorial: Enviar notificações para aplicações da plataforma Universal do Windows com os Hubs de notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial. Por isso, conclua-o antes de iniciar este tutorial.

> [!NOTE]
> Se estiver a utilizar Aplicações Móveis no Serviço de Aplicações do Azure como o seu serviço de back-end, veja a secção [Versão para Aplicações Móveis](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) deste tutorial.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>Atualizar o código para o cliente UWP

Nesta secção, atualize o código no projeto concluída para o [Tutorial: Enviar notificações para aplicações da plataforma Universal do Windows com os Hubs de notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) tutorial. O projeto já deve estar associado à Microsoft Store. Também deve estar configurado para utilizar o seu Hub de Notificação. Nesta secção, adicione o código para chamar o novo back-end de WebAPI e utilize-o para registar e enviar notificações.

1. No Visual Studio, abra a solução que criou para o [Tutorial: Enviar notificações para aplicações da plataforma Universal do Windows com os Hubs de notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
2. No Solution Explorer, clique com o botão direito no projeto de plataforma Universal do Windows (UWP) e, em seguida, clique em **gerir pacotes NuGet**.
3. No lado esquerdo, selecione **procurar**.
4. Na caixa **Procurar**, escreva **Cliente HTTP**.
5. Na lista de resultados, clique em **System.Net.Http** e clique em **Instalar**. Conclua a instalação.
6. Na caixa **Procurar** no NuGet, escreva **Json.net**. Instale o pacote **Newtonsoft.json** e, em seguida, feche a janela Gestor de Pacote NuGet.
7. No Explorador de Soluções, no projeto **WindowsApp**, faça duplo clique em **MainPage.xaml** para o abrir no editor do Visual Studio.
8. Na `MainPage.xaml` código XML, substitua o `<Grid>` secção com o código a seguir: Este código adiciona uma caixa de nome de utilizador e palavra-passe de texto que o utilizador é autenticado com. Ele também adiciona as caixas de texto para a mensagem de notificação e a etiqueta de nome de utilizador que deverá receber a notificação:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```
9. No Explorador de soluções, abra a `MainPage.xaml.cs` de ficheiros para o **(Windows 8.1)** e **(Windows Phone 8.1)** projetos. Adicione as seguintes declarações `using` na parte superior dos ficheiros:

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```
10. Na `MainPage.xaml.cs` para o **WindowsApp** do projeto, adicione o seguinte membro para o `MainPage` classe. Certifique-se de que substitui `<Enter Your Backend Endpoint>` pelo ponto final do back-end propriamente dito que obteve antes. Por exemplo, `http://mybackend.azurewebsites.net`.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```
11. Adicione o código abaixo para a classe MainPage `MainPage.xaml.cs` para o **(Windows 8.1)** e **(Windows Phone 8.1)** projetos.

    O método `PushClick` é o processador de cliques do botão **Enviar Notificação Push**. Chama o back-end para acionar uma notificação para todos os dispositivos com uma etiqueta de nome de utilizador que corresponde ao parâmetro `to_tag`. A mensagem de notificação é enviada como conteúdo JSON no corpo do pedido.

    O método `LoginAndRegisterClick` é o processador de cliques do botão **Iniciar sessão e registar**. Armazena o token de autenticação básico (representa qualquer token que o seu esquema de autenticação utilize) no armazenamento local e, em seguida, utiliza o `RegisterClient` para se registar para receber notificações com o back-end.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```
12. Open `App.xaml.cs` e localize a chamada a `InitNotificationsAsync()` no `OnLaunched()` manipulador de eventos. Comente ou elimine a chamada para `InitNotificationsAsync()`. O processador de botões inicia os registos de notificações.

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```
13. Clique com o botão direito do rato no projeto **WindowsApp**, clique em **Adicionar** e em **Classe**. Nome da classe `RegisterClient.cs`, em seguida, clique em **OK** para gerar a classe.

    Esta classe encapsula as chamadas REST necessárias para contactar o back-end da aplicação, para se registar para receber notificações push. Também armazena localmente os *registrationIds* criados pelo Hub de Notificação conforme detalhado em [Registar-se a partir do back-end da aplicação](https://msdn.microsoft.com/library/dn743807.aspx). Utiliza um token de autorização guardado no armazenamento local quando clica no botão **Iniciar sessão e registar**.
14. Adicione as seguintes declarações `using` na parte superior do ficheiro RegisterClient.cs:

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```
15. Adicione o código seguinte dentro da definição de classe `RegisterClient`.

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```
16. Guarde todas as alterações.

## <a name="test-the-application"></a>Testar a Aplicação

1. Inicie a aplicação em ambos os Windows.
2. Introduza um **Nome de Utilizador** e **Palavra-passe** conforme apresentado no ecrã abaixo. Devem diferir do nome de utilizador e da palavra-passe que introduziu no Windows Phone.
3. Clique em **Iniciar sessão e registar** e verifique se uma caixa de diálogo mostra que iniciou sessão. Este código também ativa o botão **Enviar Notificação Push**.

    ![][14]
5. Em seguida, no campo **Etiqueta de Nome de Utilizador de Destinatário**, introduza o nome de utilizador registado. Introduza a mensagem de notificação e clique em **Enviar Notificação Push**.
6. Apenas os dispositivos registados com a etiqueta de nome de utilizador correspondente recebem a mensagem de notificação.

    ![][15]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a enviar notificações push para utilizadores específicos que têm etiquetas associadas aos respetivos registos. Para saber como enviar notificações push com base na localização, avance para o seguinte tutorial:

> [!div class="nextstepaction"]
>[Enviar notificações push com base na localização](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
