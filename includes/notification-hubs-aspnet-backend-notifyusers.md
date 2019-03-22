---
title: incluir ficheiro
description: Inclua o ficheiro que contém o código para criar um projeto de back-end de ASP.NET WebAPI.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/04/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8413044beff68390dbccdf16c4477e113a85f81b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125277"
---
## <a name="create-the-webapi-project"></a>Criar o projeto WebAPI

As secções seguintes abordam a criação de um novo back-end de ASP.NET WebAPI. Este processo tem três objetivos principais:

- **Autenticar clientes**: Adicionar um manipulador de mensagens para autenticar pedidos de cliente e associar o utilizador com o pedido.
- **Registar para obter notificações utilizando o back-end de WebAPI**: Adicionar um controlador para processar registos novos para um dispositivo de cliente receber notificações. O nome de utilizador autenticado é adicionado automaticamente ao registo como uma [etiqueta](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Enviar notificações para clientes**: Adicionar um controlador para fornecer uma forma para que os utilizadores acione um envio seguro para dispositivos e clientes associados à etiqueta.

Crie o novo back-end de ASP.NET WebAPI através das seguintes ações:

> [!IMPORTANT]
> Se estiver a utilizar o Visual Studio 2015 ou anterior, antes de começar este tutorial, certifique-se que instalou a versão mais recente do Gestor de Pacotes de NuGet para o Visual Studio.
>
>Para confirmar, inicie o Visual Studio. No menu **Ferramentas**, selecione **Extensões e Atualizações**. Procure o **Gestor de Pacotes de NuGet** na sua versão do Visual Studio e verifique se tem a versão mais recente. Se a sua versão não for a versão mais recente, desinstale-a e, em seguida, reinstale o Gestor de Pacotes de NuGet.

![][B4]

> [!NOTE]
> Verifique que tem instalado o [Azure SDK](https://azure.microsoft.com/downloads/) para Visual Studio para implementação de Website.

1. Inicie o Visual Studio ou o Visual Studio Express.

2. Selecione **Explorador de Servidores** e inicie sessão na sua conta do Azure. Para criar os recursos do site na sua conta, tem de ter sessão iniciada.

3. No Visual Studio, clique com o botão direito do rato na solução Visual Studio, aponte para **Adicionar** e clique em **Novo Projeto**.
4. Expanda **Visual C#**, selecione **Web** e clique em **Aplicação Web ASP.NET**.

5. Na caixa **Nome**, escreva **AppBackend** e selecione **OK**.

    ![A janela Novo Projeto][B1]

6. Na janela **Novo Projeto ASP.NET**, selecione a caixa de verificação **API Web** e, em seguida, selecione **OK**.

    ![A janela Novo Projeto ASP.NET][B2]

7. Na janela **Configurar Aplicação Web do Microsoft Azure**, selecione uma subscrição e, em seguida, na lista **Plano do Serviço de Aplicações**, efetue uma das seguintes ações:

    * Selecione um plano do serviço de aplicações que já tenha criado.
    * Selecione **Criar um novo plano do serviço de aplicações** e, em seguida, crie um.

   Não precisa de uma base de dados para este tutorial. Depois de selecionar o seu plano do serviço de aplicações, selecione **OK** para criar o projeto.

    ![A janela Configurar Aplicação Web do Microsoft Azure][B5]

## <a name="authenticate-clients-to-the-webapi-backend"></a>Autenticar clientes no back-end de WebAPI

Nesta secção, crie uma nova classe de processadores de mensagens com o nome **AuthenticationTestHandler** para o back-end novo. Esta classe é derivada de [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) e adicionada como um processador de mensagens, para processar todos os pedidos enviados para o back-end.

1. No Explorador de Soluções, clique com o botão direito do rato no projeto **AppBackend**, selecione **Adicionar** e, em seguida, selecione **Classe**.
2. Dê o nome **AuthenticationTestHandler.cs** à classe e selecione **Adicionar** para gerar a classe. Esta classe autentica utilizadores com a *Autenticação Básica*, para simplicidade. A sua aplicação pode utilizar qualquer esquema de autenticação.
3. No AuthenticationTestHandler.cs, adicione as instruções `using` seguintes:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. No AuthenticationTestHandler.cs, substitua a definição de classe `AuthenticationTestHandler` pelo seguinte código:

    O processador autoriza o pedido quando se verificarem as três condições seguintes:

   * O pedido inclui o cabeçalho *Autorização*.
   * O pedido utiliza a autenticação *básica*.
   * A cadeia de nome de utilizador e a cadeia de palavra-passe são a mesma cadeia.

   Caso contrário, o pedido é rejeitado. Esta autenticação não é uma verdadeira abordagem de autenticação e autorização. É apenas um exemplo simples para este tutorial.

   Se `AuthenticationTestHandler` autenticar e autorizar a mensagem do pedido, o utilizador de autenticação básica é anexado ao pedido atual em [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). As informações do utilizador em HttpContext serão utilizadas por outro controlador (RegisterController) mais tarde, para adicionar uma [etiqueta](https://msdn.microsoft.com/library/azure/dn530749.aspx) ao pedido de registo de notificação.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (verifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool verifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Nota de segurança: O `AuthenticationTestHandler` classe fornecer uma autenticação verdadeira. É utilizada apenas para imitar a autenticação básica e não é segura. Tem de implementar um mecanismo de autenticação segura nos seus serviços e aplicações de produção.
5. Para registar o processador de mensagens, adicione o seguinte código ao fim do método `Register` na classe **App_Start/WebApiConfig.cs**:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Guarde as alterações.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Utilizar o back-end de WebAPI para registar notificações

Nesta secção, adicione um controlador novo ao back-end de WebAPI para processar pedidos de registo de um utilizador e de um dispositivo para as notificações, através da biblioteca de cliente dos hubs de notificação. O controlador adiciona uma etiqueta de utilizador para o utilizador que `AuthenticationTestHandler` autenticou e anexou a HttpContext. A etiqueta tem o formato de cadeia `"username:<actual username>"`.

1. No Explorador de Soluções, clique com o botão direito no projeto **AppBackend** e, em seguida, selecione **Gerir Pacotes de NuGet**.

2. No painel esquerdo, selecione **Online** e, na caixa **Procurar**, escreva **Microsoft.Azure.NotificationHubs**.

3. Na lista de resultados, selecione **Hubs de Notificação do Microsoft Azure** e, em seguida, selecione **Instalar**. Conclua a instalação e feche a janela Gestor de Pacotes de NuGet.

    Esta ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure mediante a utilização do [Pacote NuGet Microsoft.Azure.Notification Hubs](http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Crie um ficheiro de classe novo que represente a ligação ao hub de notificação utilizado para enviar notificações. No Explorador de Soluções, clique com o botão direito do rato na pasta **Modelos**, selecione **Adicionar** e, em seguida, selecione **Classe**. Dê o nome **Notifications.cs** à classe nova e selecione **Adicionar** para gerá-la.

    ![A janela Adicionar Novo Item][B6]

5. Em Notifications.cs, adicione a instrução `using` à parte superior do ficheiro:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Substitua a definição de classe `Notifications` pelo código seguinte e substitua os dois marcadores de posição pela cadeia de ligação (com acesso total) do seu hub de notificação e o nome do hub (disponível no [portal do Azure](http://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
7. Em seguida, crie um controlador novo com o nome **RegisterController**. No Explorador de Soluções, clique com o botão direito do rato na pasta **Controladores**, selecione **Adicionar** e, em seguida, selecione **Controlador**.

8. Selecione **Controlador da API Web 2 – Vazio** e, em seguida, selecione **Adicionar**.

    ![A janela Adicionar Estrutura][B7]

9. Na caixa **Nome do controlador**, escreva **RegisterController** para designar a classe nova e selecione **Adicionar**.

    ![A janela Adicionar Controlador][B8]

10. No RegisterController.cs, adicione as instruções `using` seguintes:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Adicione o código seguinte dentro da definição de classe `RegisterController`. Neste código, adicione uma etiqueta de utilizador para o utilizador que está anexado a HttpContext. O utilizador foi autenticado e anexado a HttpContext através do filtro de mensagem que adicionou, `AuthenticationTestHandler`. Também pode adicionar verificações opcionais para confirmar que o utilizador tem direitos para se registar nas etiquetas pedidas.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "gcm":
                registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Guarde as alterações.

## <a name="send-notifications-from-the-webapi-backend"></a>Enviar notificações a partir do back-end de WebAPI

Nesta secção, irá adicionar um novo controlador que indica uma forma de os dispositivos de cliente enviarem uma notificação. A notificação baseia-se na etiqueta de nome de utilizador que utiliza a Biblioteca .NET dos Hubs de Notificação do Azure no back-end de ASP.NET WebAPI.

1. Crie outro controlador novo com o nome **NotificationsController** da mesma forma que criou o **RegisterController** na secção anterior.

2. No NotificationsController.cs, adicione as instruções `using` seguintes:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Adicione o método seguinte à classe **NotificationsController**:

    Este código envia um tipo de notificação com base no parâmetro `pns` do Serviço de Notificação de Plataforma (PNS). O valor de `to_tag` é utilizado para definir a etiqueta *nome de utilizador* na mensagem. Esta etiqueta tem de corresponder a uma etiqueta de nome de utilizador de um registo de hub de notificação ativo. A mensagem da notificação é retirada do corpo do pedido POST e formatada para o PNS de destino.

    Consoante o PNS que os seus dispositivos suportados utilizam para receber notificações, estas são suportadas com vários formatos. Por exemplo, em dispositivos Windows, poderá utilizar uma [notificação de alerta com WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) que não seja diretamente suportada por outro PNS. Nesse caso, o seu back-end tem de formatar a notificação de modo a que seja suportada pelo PNS dos dispositivos que pretende incluir. Em seguida, utilize a API de envio adequada na [classe NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "gcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Para executar a aplicação e garantir a precisão do seu trabalho até ao momento, prima a tecla **F5**. A aplicação abre um browser e é apresentada na home page do ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>Publicar o back-end de WebAPI novo

Em seguida, implemente a aplicação num site do Azure para que seja acessível a partir de todos os dispositivos.

1. Clique com o botão direito do rato no projeto **AppBackend** e selecione **Publicar**.

2. Selecione **Serviço de Aplicações do Microsoft Azure** como o destino da publicação e selecione \*\*Publicar. A janela Criar Serviço de Aplicações é aberta. Aí, pode criar todos os recursos do Azure necessários para executar a aplicação Web ASP.NET no Azure.

    ![O mosaico do Serviço de Aplicações do Microsoft Azure][B15]

3. Na janela **Criar Serviço de Aplicações**, selecione a sua conta do Azure. Selecione **Alterar Tipo** > **Aplicação Web**. Mantenha o **Nome da Aplicação Web** predefinido e, em seguida, selecione a **Subscrição**, o **Grupo de Recursos** e o **Plano do Serviço de Aplicações**.

4. Selecione **Criar**.

5. Anote a propriedade **URL do Site**, na secção **Resumo**. Este URL será o seu *ponto final de back-end* mais adiante no tutorial.

6. Selecione **Publicar**.

Depois de concluir o assistente, este publica a aplicação Web ASP.NET no Azure e, em seguida, abre a aplicação no browser predefinido.  A aplicação é visualizável nos Serviços Aplicacionais do Azure.

O URL utiliza o nome da aplicação Web que especificou anteriormente, com o formato http://<nome_da_aplicação>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
