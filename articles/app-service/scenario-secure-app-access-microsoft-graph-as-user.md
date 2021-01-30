---
title: Tutorial - Web app acede ao Microsoft Graph como o utilizador | Rio Azure
description: Neste tutorial, aprende-se a aceder a dados no Microsoft Graph para um utilizador inscrito.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 3413c1a3f27b48c60ae730ad230c653928702faa
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063387"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Tutorial: Aceda ao Microsoft Graph a partir de uma aplicação segura como utilizador

Saiba como aceder ao Microsoft Graph a partir de uma aplicação web em execução no Azure App Service.

:::image type="content" alt-text="Diagrama que mostra o acesso ao Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Pretende adicionar acesso ao Microsoft Graph a partir da sua aplicação web e realizar alguma ação como utilizador inscrito. Esta secção descreve como conceder permissões delegadas à aplicação web e obter as informações de perfil do utilizador inscrito a partir do Azure Ative Directory (Azure AD).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Conceder permissões delegadas a uma aplicação web.
> * Ligue para o Microsoft Graph a partir de uma aplicação web para um utilizador inscrito.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação web em execução no Azure App Service que tem o [módulo de autenticação/autorização do Serviço de Aplicações ativado.](scenario-secure-app-authentication-app-service.md)

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Conceder acesso frontal para ligar para o Microsoft Graph

Agora que ativou a autenticação e autorização na sua aplicação web, a aplicação web está registada na plataforma de identidade da Microsoft e é apoiada por uma aplicação AD Azure. Neste passo, você dá às permissões da aplicação web para aceder ao Microsoft Graph para o utilizador. (Tecnicamente, você dá à aplicação AZure AD da aplicação web as permissões para aceder à aplicação AD do Microsoft Graph Azure para o utilizador.)

No menu do [portal Azure,](https://portal.azure.com) selecione **Azure Ative Directory** ou procure e selecione **O Diretório Ativo Azure** a partir de qualquer página.

Selecione **registos de**  >  **aplicações De propriedade** Ver todas as  >  **aplicações neste diretório**. Selecione o nome da sua aplicação web e, em seguida, selecione **permissões API**.

**Selecione Adicionar uma permissão** e, em seguida, selecione APIs microsoft e Microsoft Graph.

Selecione **permissões delegadas** e, em seguida, selecione **User.Read** da lista. **Selecione Permissões de adicionar**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Configurar o Serviço de Aplicações para devolver um token de acesso utilizável

A aplicação web tem agora as permissões necessárias para aceder ao Microsoft Graph como utilizador inscrito. Neste passo, configura a autenticação e autorização do Serviço de Aplicações para lhe dar um token de acesso utilizável para aceder ao Microsoft Graph. Para este passo, precisa do ID do serviço a jusante (Microsoft Graph). O ID da aplicação para o Microsoft Graph é *0000003-0000-000-c000-00000000000000000000000*.

> [!IMPORTANT]
> Se não configurar o Serviço de Aplicações para devolver um token de acesso utilizável, recebe um ```CompactToken parsing failed with error code: 80049217``` erro quando liga para as APIs do Gráfico da Microsoft no seu código.

Vá ao [Azure Resource Explorer](https://resources.azure.com/) e utilize a árvore de recursos, localize a sua aplicação web. O URL de recursos deve ser semelhante a `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914` .

O Azure Resource Explorer é agora aberto com a sua aplicação web selecionada na árvore de recursos. No topo da página, selecione **Read/Write** para permitir a edição dos seus recursos Azure.

No navegador esquerdo, faça um furo até **config**  >  **authsettings**.

Na vista **authsettings,** **selecione Editar**. ```additionalLoginParams```Desa cotado na seguinte cadeia JSON utilizando a identificação do cliente que copiou.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Guarde as suas definições selecionando **PUT**. Esta definição pode demorar vários minutos a produzir efeito. A sua aplicação web está agora configurada para aceder ao Microsoft Graph com um token de acesso adequado. Caso não o faça, o Microsoft Graph retorna um erro dizendo que o formato do token compacto está incorreto.

## <a name="call-microsoft-graph-net"></a>Ligue para o Microsoft Graph (.NET)

A sua aplicação web tem agora as permissões necessárias e também adiciona o ID do cliente do Microsoft Graph aos parâmetros de login. Utilizando a [biblioteca Microsoft.Identity.Web,](https://github.com/AzureAD/microsoft-identity-web/)a aplicação web obtém um token de acesso para autenticação com o Microsoft Graph. Na versão 1.2.0 e posterior, a biblioteca Microsoft.Identity.Web integra-se e pode funcionar ao lado do módulo de autenticação/autorização do Serviço de Aplicações. Microsoft.Identity.Web deteta que a aplicação web está hospedada no Serviço de Aplicações e obtém o sinal de acesso do módulo de autenticação/autorização do Serviço de Aplicações. O token de acesso é então transmitido para pedidos autenticados com a API do Gráfico microsoft.

Para ver este código como parte de uma aplicação de amostra, consulte a [amostra no GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/2-WebApp-graphapi-on-behalf).

> [!NOTE]
> A biblioteca Microsoft.Identity.Web não é necessária na sua aplicação web para autenticação/autorização básica ou para autenticar pedidos com o Microsoft Graph. É possível ligar de forma segura para [apis a jusante](tutorial-auth-aad.md#call-api-securely-from-server-code) apenas com o módulo de autenticação/autorização do Serviço de Aplicações ativado.
> 
> No entanto, a autenticação/autorização do Serviço de Aplicações destina-se a cenários de autenticação mais básicos. Para cenários mais complexos (manuseamento de reclamações personalizadas, por exemplo), precisa da biblioteca Microsoft.Identity.Web ou [da Microsoft Authentication Library](../active-directory/develop/msal-overview.md). Há um pouco mais de trabalho de configuração e configuração no início, mas a biblioteca Microsoft.Identity.Web pode funcionar ao lado do módulo de autenticação/autorização do Serviço de Aplicações. Mais tarde, quando a sua aplicação web necessitar de lidar com cenários mais complexos, pode desativar o módulo de autenticação/autorização do Serviço de Aplicações e o Microsoft.Identity.Web já fará parte da sua aplicação.

### <a name="install-client-library-packages"></a>Instalar pacotes de bibliotecas de clientes

Instale os pacotes [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) e [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet no seu projeto utilizando a interface de linha de comando .NET Core ou a Consola de Gestor de Pacotes em Estúdio Visual.

# <a name="command-line"></a>[Linha de comandos](#tab/command-line)

Abra uma linha de comando e mude para o diretório que contém o seu ficheiro de projeto.

Executar os comandos de instalação.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Gestor de pacotes](#tab/package-manager)

Abra o projeto/solução no Estúdio Visual e abra a consola utilizando o comando de gestor de **pacotes Tools**  >  **NuGet Package**  >  **Manager.**

Executar os comandos de instalação.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

No ficheiro *Startup.cs,* o ```AddMicrosoftIdentityWebApp``` método adiciona o Microsoft.Identity.Web à sua aplicação web. O ```AddMicrosoftGraph``` método adiciona suporte ao Microsoft Graph.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* especifica a configuração para a biblioteca Microsoft.Identity.Web. No [portal Azure](https://portal.azure.com), selecione **Azure Ative Directory** a partir do menu do portal e, em seguida, selecione **registos de Aplicações**. Selecione o registo da aplicação criado quando ativou o módulo de autenticação/autorização do Serviço de Aplicações. (O registo da aplicação deve ter o mesmo nome que a sua aplicação web.) Pode encontrar o ID do inquilino e a identificação do cliente na página geral do registo da aplicação. O nome de domínio pode ser encontrado na página geral da Azure AD para o seu inquilino.

*O gráfico* especifica o ponto final do Microsoft Graph e os âmbitos iniciais necessários pela aplicação.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

O exemplo a seguir mostra como ligar para o Microsoft Graph como utilizador inscrito e obter algumas informações do utilizador. O ```GraphServiceClient``` objeto é injetado no controlador e a autenticação foi configurada para si pela biblioteca Microsoft.Identity.Web.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou este tutorial e já não precisa da aplicação web ou dos recursos associados, [limpe os recursos que criou.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Conceder permissões delegadas a uma aplicação web.
> * Ligue para o Microsoft Graph a partir de uma aplicação web para um utilizador inscrito.

> [!div class="nextstepaction"]
> [Serviço de aplicações acede ao Microsoft Graph como a app](scenario-secure-app-access-microsoft-graph-as-app.md)