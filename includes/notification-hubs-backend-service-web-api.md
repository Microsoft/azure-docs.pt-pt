---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081543"
---
### <a name="create-a-web-project"></a>Criar um projeto web

1. No **Estúdio Visual,** selecione **File**  >  **New Solution**.

1. Selecione **.NET Core**  >  **App**  >  **ASP.NET Core**  >  **API**  >  **Next**.
  
1. No **Configure o seu novo diálogo ASP.NET Core Web API,** selecione **Target Framework** de **.NET Core 3.1**.

1. Introduza *pushDemoApi* para o nome do **projeto** e, em seguida, selecione **Criar**.

1. Comece a depurar **(Command**  +  **Enter)** para testar a aplicação modelo.

    > [!NOTE]
    > A aplicação modelo está configurada para usar o **WeatherForecastController** como *o launchUrl*. Isto está situado em **Propriedades**  >  **launchSettings.jsem**.  
    >
    > Se for solicitado com um **certificado de desenvolvimento inválido encontrado** mensagem:
    >
    > 1. Clique em **Sim** para concordar em executar a ferramenta 'dotnet dev-certs https' para corrigir isto. A ferramenta 'dotnet dev-certs https' solicita-lhe que introduza uma palavra-passe para o certificado e a palavra-passe para o seu Keychain.
    >
    > 1. Clique **em Sim** quando solicitado para instalar e confiar no novo **certificado,** em seguida, insira a palavra-passe para o seu Keychain.

1. Expandir a pasta **controladores** e, em seguida, apagar **WeatherForecastController.cs**.

1. Apague **WeatherForecast.cs**.

1. **Controlo**  +  **Clique** no projeto **PushDemoApi** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Configurar os valores de configuração locais utilizando a [ferramenta Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager). Dissociar os segredos da solução garante que não acabem no controlo de fontes. **Open Terminal,** em seguida, vá ao diretório do arquivo do projeto e execute os seguintes comandos:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Substitua os valores do espaço reservado pelo nome do seu próprio hub de notificação e valores de cadeia de ligação. Fez uma nota deles na secção de centros de [notificação.](#create-a-notification-hub) Caso contrário, pode procurar em [Azure.](https://portal.azure.com)

    **NotificaçõesHub:Nome**:  
    Ver *Nome* no resumo **do Essencial** no topo da **Visão Geral**.  

    **NotificationHub:ConnectionString**:  
    Ver *DefaultFullSharedAccessssignature* in **Access Policies**

    > [!NOTE]
    > Para cenários de produção, pode olhar para opções como [a Azure KeyVault](https://azure.microsoft.com/services/key-vault) para armazenar de forma segura a cadeia de ligação. Para simplificar, os segredos serão adicionados às definições da aplicação [Azure App Service.](https://azure.microsoft.com/services/app-service/)

### <a name="authenticate-clients-using-an-api-key-optional"></a>Autenticar clientes utilizando uma chave API (Opcional)

As chaves da API não são tão seguras como fichas, mas serão suficientes para os fins deste tutorial. Uma chave API pode ser configurada facilmente através do [Middleware ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Adicione a **chave API** aos valores de configuração local.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Deve substituir o valor do placeholder pelo seu próprio e tomar nota dele.

1. **Controlo**  +  **Clique** no projeto **PushDemoApi,** escolha **Nova Pasta** no menu **Adicionar** e, em seguida, clique em **Adicionar** usando *a autenticação* como nome de **pasta**.

1. **Controlo**  +  **Clique** na pasta **Autenticação** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Selecione **Classe**  >  **Geral Vazia**, *introduza ApiKeyAuthOptions.cs* para o **Nome,** em seguida, clique em **Novo** adicionando a seguinte implementação.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Adicione outra **Classe Vazia** à pasta **de Autenticação** chamada *ApiKeyAuthHandler.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > Um [Manipulador de Autenticação](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) é um tipo que implementa o comportamento de um esquema, neste caso um esquema personalizado da Chave API.

1. Adicione outra **Classe Vazia** à pasta **de Autenticação** chamada *ApiKeyAuthenticationBuilderExtensions.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Este método de extensão simplifica o código de configuração do middleware em **Startup.cs** tornando-o mais legível e geralmente mais fácil de seguir.

1. Em **Startup.cs,** atualize o método **ConfigureServices** para configurar a autenticação da Chave API abaixo da chamada aos **serviços. Método AddControllers.**

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Ainda em **Startup.cs**, atualize o método **Configure** para ligar para os métodos de extensão **useAuthentication** e **UseAuthorization** no **IApplicationBuilder da aplicação.** Certifique-se de que estes métodos são chamados após **o useRouting** e antes **da aplicação. UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Call **UseAuthentication** regista o middleware que utiliza os esquemas de autenticação previamente registados (a partir de **ConfigureServices).** Isto deve ser chamado antes de qualquer middleware que dependa da autenticação dos utilizadores.

### <a name="add-dependencies-and-configure-services"></a>Adicionar Dependências e Serviços de Configuração

ASP.NET Core suporta o padrão de design de software [de injeção de dependência (DI),](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) que é uma técnica para alcançar a [Inversão do Controlo (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre as classes e as suas dependências.  

A utilização do centro de notificação e do [Centro de Notificação SDK para operações de backend](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) está encapsulada num serviço. O serviço está registado e disponibilizado através de uma abstração adequada.

1. **Controlo**  +  **Clique** na pasta **Dependências** e, em seguida, escolha **Gerir pacotes NuGet...**.

1. Procure **microsoft.Azure.NotificationHubs** e certifique-se de que está verificado.

1. Clique **em Adicionar Pacotes**e, em seguida, clique em **Aceitar** quando solicitado para aceitar os termos da licença.

1. **Controlo**  +  **Clique** no projeto **PushDemoApi,** escolha **Nova Pasta** no menu **Adicionar** e, em seguida, clique em **Adicionar** usando *modelos* como nome de **pasta**.

1. **Controlo**  +  **Clique** na pasta **Modelos** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Selecione **Classe**  >  **Geral Vazia**, *introduza PushTemplates.cs* para o **Nome,** em seguida, clique em **Novo** adicionando a seguinte implementação.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Esta classe contém as cargas de notificação simbólicas para as notificações genéricas e silenciosas exigidas por este cenário. As cargas são definidas fora da [Instalação](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) para permitir a experimentação sem ter de atualizar as instalações existentes através do serviço. O manuseamento de alterações nas instalações desta forma está fora de alcance para este tutorial. Para a produção, considere [modelos personalizados.](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages)

1. Selecione **General**  >  **Classe Geral Vazia**, introduza *DeviceInstallation.cs* para o **Nome,** em seguida, clique em **Novo** adicionando a seguinte implementação.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Adicione outra **Classe Vazia** à pasta **Modelos** chamada *NotificationRequest.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Adicione outra **Classe Vazia** à pasta **Modelos** chamada *NotificationHubOptions.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Adicione uma nova pasta ao projeto **PushDemoApi** chamado *Services*.

1. Adicione uma **Interface Vazia** à pasta **serviços** chamada *INotificationService.cs,* em seguida, adicione a seguinte implementação.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Adicione uma **Classe Vazia** à pasta **serviços** chamada *NotificationHubsService.cs*, em seguida, adicione o seguinte código para implementar a interface **INotificationService:**

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > A expressão de etiqueta fornecida à **SendTemplateNotificationAsync** está limitada a 20 tags. Limita-se a 6 para a maioria dos operadores, mas a expressão contém apenas RRO (//) neste caso. Se houver mais de 20 tags no pedido, então devem ser divididos em múltiplos pedidos. Consulte a documentação [de Roteamento e Tag Expressions](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) para obter mais detalhes.

1. Em **Startup.cs**, atualize o método **ConfigureServices** para adicionar o **NotificationHubsService** como uma implementação singleton do **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Criar a API de Notificações

1. **Controlo**  +  **Clique** na pasta **Controladores** e, em seguida, escolha **Novo Ficheiro...** no menu **Adicionar.**

1. Selecione **ASP.NET**  >  **Core Web API Controller Class**, *insira notificaçõesControlador* para o **nome**e, em seguida, clique em **New**.

1. Adicione os seguintes espaços de nome ao topo do ficheiro.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Atualize o controlador de modelo para que deriva do **ControllerBase** e seja decorado com o atributo **ApiController.**

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > A classe base **do Controlador** fornece suporte para visualizações, mas esta não é necessária neste caso e assim **a ControllerBase** pode ser usada em vez disso.

1. Se optar por completar os [clientes Authenticate utilizando uma secção chave API,](#authenticate-clients-using-an-api-key-optional) deverá também decorar o **NotificationsController** com o atributo **Authorize.**

    ```cs
    [Authorize]
    ```

1. Atualize o construtor para aceitar a instância registada do **INotificationService** como argumento e atribuí-lo a um membro readonly.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. Em **launchSettings.js(dentro** da pasta **Propriedades),** altere o **launchUrl** de `weatherforecast` *api/notificações* para corresponder ao URL especificado no atributo **Rota** do **Controlador de Registos.**

1. Comece a depurar **(Command**  +  **Enter)** para validar a aplicação está a trabalhar com o novo **NotificationsController** e devolve um estado **401 Não Autorizado.**

    > [!NOTE]
    > O Visual Studio pode não lançar automaticamente a aplicação no navegador. Vais usar [o Carteiro](https://www.postman.com/downloads) para testar a API a partir de agora.

1. Num novo **[separador Carteiro,](https://www.postman.com/downloads)** desa estaba o pedido para **GET** e insira o endereço abaixo.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > O endereço local local deve corresponder ao valor **de aplicaçãoUrl** encontrado em **Propriedades**  >  **launchSettings.jsem**. O padrão deve `https://localhost:5001;http://localhost:5000` ser, no entanto, algo para verificar se recebe uma resposta 404.

1. Se optar por completar os [clientes Authenticate utilizando uma secção chave API,](#authenticate-clients-using-an-api-key-optional) certifique-se de configurar os cabeçalhos de pedido para incluir o seu valor **apikey.**

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Clique no botão **Enviar.**

    > [!NOTE]
    > Você deve receber um estado **de 200 OK** com algum conteúdo **JSON.**
    >
    > Se receber um aviso **de verificação do certificado SSL,** pode mudar o **[carteiro](https://www.postman.com/downloads)** de verificação do certificado SSL solicitado nas **Definições**.

1. Substitua os métodos de classe com modelo pelo seguinte código.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Criar a App API

Agora cria uma [App API](https://azure.microsoft.com/services/app-service/api/) no [Azure App Service](https://docs.microsoft.com/azure/app-service/) para hospedar o serviço backend.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Clique **em Criar um recurso,** em seguida, procurar e escolher app **API,** em seguida, clique em **Criar**.

1. Atualize os seguintes campos e, em seguida, clique em **Criar**.

    **Nome da aplicação:**  
    Insira um nome globalmente único para a **App API**

    **Assinatura:**  
    Escolha o mesmo alvo **A Subscrição** que criou no centro de notificação.

    **Grupo de Recursos:**  
    Escolha o mesmo **Grupo de Recursos** em que criou o centro de notificação.

    **Plano de Serviço de Aplicação/Localização:**  
    Criar um novo **Plano de Serviço de Aplicações**  

    > [!NOTE]
    > Altere da opção por defeito para um plano que inclua suporte **SSL.** Caso contrário, terá de tomar as medidas adequadas ao trabalhar com a aplicação móvel para evitar que os pedidos em **http** sejam bloqueados.

    **Insights de aplicação:**  
    Mantenha a opção sugerida (um novo recurso será criado usando esse nome) ou escolha um recurso existente.

1. Uma vez que a **App API** tenha sido aprovisionada, navegue para esse recurso.

1. Tome nota da propriedade **URL** no resumo **essentials** no topo da **Visão Geral**. Este URL é o seu ponto final de *backend* que será usado mais tarde neste tutorial.

    > [!NOTE]
    > O URL utiliza o nome da aplicação API que especificou anteriormente, com o formato `https://<app_name>.azurewebsites.net` .

1. Selecione **Configuração** da lista (em **Definições).**  

1. Para cada uma das definições abaixo, clique em **Nova definição** de aplicação para introduzir o **Nome** e um **Valor,** em seguida, clique em **OK**.

   | Name                               | Valor                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Estas são as mesmas definições definidas anteriormente nas definições do utilizador. Deve poder copiá-los. A **definição autenticação:ApiKey** só é necessária se optar por completar os [clientes Authenticate utilizando uma secção chave API.](#authenticate-clients-using-an-api-key-optional) Para cenários de produção, pode olhar para opções como [Azure KeyVault.](https://azure.microsoft.com/services/key-vault) Estas foram adicionadas como configurações de aplicação para a simplicidade neste caso.

1. Uma vez adicionadas todas as definições de aplicação clique em **Guardar**e, em seguida, **Continue**.

### <a name="publish-the-backend-service"></a>Publique o serviço de backend

Em seguida, implementa a aplicação para a App API para torná-la acessível a partir de todos os dispositivos.  

1. Altere a sua configuração de **Debug** para **Release** se ainda não o fez.

1. **Controlo**  +  **Clique** no projeto **PushDemoApi** e, em seguida, escolha **Publicar para Azure...** a partir do menu **Publicar.**

1. Siga o fluxo de auth se for solicitado. Utilize a conta que usou na anterior criar a secção [app API.](#create-the-api-app)

1. Selecione a **App API do Serviço de Aplicações Azure** que criou anteriormente a partir da lista como alvo de publicação e, em seguida, clique em **Publicar**.

Depois de ter concluído o assistente, publica a app para o Azure e abre a aplicação. Tome nota da **URL** se ainda não o fez. Este URL é o seu ponto final de *backend* que é usado mais tarde neste tutorial.

### <a name="validating-the-published-api"></a>Validação da API publicada

1. No **[Carteiro](https://www.postman.com/downloads)** abra um novo separador, desabrote o pedido para **POST** e introduza o endereço abaixo. Substitua o espaço reservado pelo endereço base de que fez notar na secção de serviço de [backend](#publish-the-backend-service) anterior.

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > O endereço base deve estar no formato``https://<app_name>.azurewebsites.net/``

1. Se optar por completar os [clientes Authenticate utilizando uma secção chave API,](#authenticate-clients-using-an-api-key-optional) certifique-se de configurar os cabeçalhos de pedido para incluir o seu valor **apikey.**

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Escolha a opção **bruta** para o **Corpo,** em seguida, escolha **JSON** na lista de opções de formato e, em seguida, inclua algum conteúdo de espaço reservado **JSON:**

    ```json
    {}
    ```

1. Clique em **Enviar**.

    > [!NOTE]
    > Deverá receber um estatuto de **400 Bad Request** do serviço.

1. Faça os passos 1-4 novamente, mas desta vez especificando o ponto final de pedidos para validar recebe a mesma resposta **de 400 Bad Request.**

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Ainda não é possível testar a API utilizando dados de pedido válidos, uma vez que isso exigirá informações específicas da plataforma a partir da aplicação móvel do cliente.
