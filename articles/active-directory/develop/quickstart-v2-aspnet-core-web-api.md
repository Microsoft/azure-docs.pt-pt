---
title: 'Quickstart: Proteja uma API web core ASP.NET com a plataforma de identidade microsoft | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você descarrega e modifica uma amostra de código que demonstra como proteger uma API web core ASP.NET, utilizando a plataforma de identidade da Microsoft para autorização.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 30593c51f17b99989409ddd22c9c1caa28468039
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720836"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-the-microsoft-identity-platform"></a>Quickstart: Proteja uma API web core ASP.NET com a plataforma de identidade microsoft

Neste quickstart, você descarrega uma amostra de código API web ASP.NET e revê a forma como restringe o acesso de recursos apenas a contas autorizadas. A amostra suporta a autorização de contas e contas pessoais da Microsoft em qualquer organização do Azure Ative Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Pré-requisitos
>
> - Conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
> - [Inquilino do Diretório Ativo Azure](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1+](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Código de Estúdio Visual](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Passo 1: Registar o pedido
>
> Em primeiro lugar, registe a API web no seu inquilino AZure AD e adicione um âmbito seguindo estes passos:
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Para **nome,** insira um nome para a sua candidatura. Por exemplo, insira **AspNetCoreWebApi-Quickstart**. Os utilizadores da sua aplicação verão este nome e poderão alterá-lo mais tarde.
> 1. Selecione **Registar**.
> 1. Em **Gestão**, **selecione Expor uma API** Adicione um  >  **âmbito**. Para **iD uri de aplicação,** aceite o padrão selecionando **Save and continue**, e, em seguida, insira os seguintes detalhes:
>    - **Nome do âmbito:**`access_as_user`
>    - **Quem pode consentir?**: **Administradores e utilizadores**
>    - **Nome do exposição de consentimento de administração:**`Access AspNetCoreWebApi-Quickstart`
>    - **Descrição do consentimento da administração**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Nome de visualização do consentimento do utilizador:**`Access AspNetCoreWebApi-Quickstart`
>    - **Descrição do consentimento do utilizador**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Estado**: **Habilitado**
> 1. **Selecione Adicionar âmbito** para completar a adição de âmbito.

## <a name="step-2-download-the-aspnet-core-project"></a>Passo 2: Descarregue o projeto ASP.NET Core

> [!div renderon="docs"]
> [Descarregue a solução core ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) do GitHub.

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Passo 3: Configurar o projeto ASP.NET Core
>
> Neste passo, configuure o código de amostra para trabalhar com o registo de aplicações que criou anteriormente.
>
> 1. Extraia o arquivo .zip numa pasta perto da raiz da sua unidade. Por exemplo, extrair em *C:\Azure-Samples*.
>
>    Recomendamos extrair o arquivo num diretório perto da raiz da sua unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
>
> 1. Abra a solução na pasta *webapi* no seu editor de código.
> 1. Abra a *appsettings.jsno* ficheiro e modifique o seguinte código:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - `Enter_the_Application_Id_here`Substitua-se pelo ID de aplicação (cliente) da aplicação que registou no portal Azure. Pode encontrar o ID da aplicação (cliente) na **página** geral da aplicação.
>    - Substitua `Enter_the_Tenant_Info_Here` por uma das seguintes:
>       - Se a sua candidatura apoiar **apenas neste diretório organizacional,** substitua este valor pelo ID do diretório (inquilino) (um GUIADO) ou nome de inquilino (por exemplo, `contoso.onmicrosoft.com` ). Pode encontrar o ID do diretório (inquilino) na **página** geral da aplicação.
>       - Se a sua aplicação suportar **contas em qualquer diretório organizacional,** substitua este valor por `organizations` .
>       - Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** deixe este valor como `common` .
>
> Para este arranque rápido, não altere quaisquer outros valores no *appsettings.jsno* ficheiro.

## <a name="how-the-sample-works"></a>Como funciona a amostra

A API web recebe um símbolo de uma aplicação do cliente, e o código na API web valida o token. Este cenário é explicado mais detalhadamente em [Cenário: API web protegida](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Classe de arranque

O middleware *Microsoft.AspNetCore.Authentication* utiliza uma `Startup` classe executada quando o processo de hospedagem começa. No seu `ConfigureServices` método, o `AddMicrosoftIdentityWebApi` método de extensão fornecido pelo *Microsoft.Identity.Web* é chamado.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

O `AddAuthentication()` método configura o serviço para adicionar a autenticação baseada em JwtBearer.

A linha que contém `.AddMicrosoftIdentityWebApi` adiciona a autorização da plataforma de identidade da Microsoft à sua API web. Em seguida, é configurado para validar fichas de acesso emitidas pela plataforma de identidade da Microsoft com base nas informações `AzureAD` na secção doappsettings.js *no* ficheiro de configuração:

| *appsettings.jsna* chave | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Identificação da aplicação (cliente) da aplicação registada no portal Azure.                                                                                       |
| `Instance`             | Serviço de ficha de segurança (STS) para o utilizador autenticar. Este valor é tipicamente `https://login.microsoftonline.com/` , indicando a nuvem pública Azure. |
| `TenantId`             | Nome do seu inquilino ou identificação do seu inquilino (um GUID), ou `common` para assinar em utilizadores com contas de trabalho ou escola ou contas pessoais da Microsoft.                             |

O `Configure()` método contém dois métodos importantes `app.UseAuthentication()` `app.UseAuthorization()` e, que permitem a sua funcionalidade nomeada:

```csharp
// The runtime calls this method. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protecting-a-controller-a-controllers-method-or-a-razor-page"></a>Proteger um controlador, um método de controlador ou uma página razor

Pode proteger um controlador ou métodos de controlador utilizando o `[Authorize]` atributo. Este atributo restringe o acesso ao controlador ou métodos, permitindo apenas utilizadores autenticados. Pode iniciar-se um desafio de autenticação no acesso ao controlador se o utilizador não for autenticado.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validation-of-scope-in-the-controller"></a>Validação do âmbito no controlador

O código da API verifica que os âmbitos necessários estão no símbolo através da `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` utilização:

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

O repositório GitHub que contém esta amostra de código API web core ASP.NET inclui instruções e mais amostras de código que mostram como:

- Adicione a autenticação a um novo ASP.NET Core web API.
- Ligue para a API web a partir de uma aplicação de ambiente de trabalho.
- Ligue para APIs a jusante como o Microsoft Graph e outros APIs da Microsoft.

> [!div class="nextstepaction"]
> [ASP.NET tutoriais de API web core no GitHub](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
