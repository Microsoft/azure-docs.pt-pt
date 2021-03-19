---
title: 'Quickstart: Adicione o início de sôs-in com a Microsoft a uma aplicação web core ASP.NET | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação implementa o início de sôrção da Microsoft numa aplicação web ASP.NET Core utilizando o OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: e7296b04e3e912e96ac8c2ed77b44288324c262f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578707"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Quickstart: Adicione o início de sôs-in com a Microsoft a uma aplicação web core ASP.NET

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação web core ASP.NET pode assinar em utilizadores de qualquer organização do Azure Ative Directory (Azure AD).  

> [!div renderon="docs"]
> O seguinte diagrama mostra como funciona a aplicação da amostra:
>
> ![Diagrama da interação entre o navegador web, a aplicação web e a plataforma de identidade da Microsoft na aplicação da amostra.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Pré-requisitos
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [Código de Estúdio Visual](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1+](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Registe-se e descarregue a app
> Tem duas opções para começar a construir a sua aplicação: configuração automática ou manual.
>
> ### <a name="automatic-configuration"></a>Configuração automática
> Se quiser configurar automaticamente a sua aplicação e, em seguida, descarregar a amostra de código, siga estes passos:
>
> 1. Aceda à página do <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">portal Azure para o registo de aplicações.</a>
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação num clique.
>
> ### <a name="manual-configuration"></a>Configuração manual
> Se pretender configurar manualmente a sua aplicação e a sua amostra de código, utilize os seguintes procedimentos.
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar a inscrição.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Para **nome,** insira um nome para a sua candidatura. Por exemplo, **insira AspNetCore-Quickstart**. Os utilizadores da sua aplicação verão este nome e poderão alterá-lo mais tarde.
> 1. Para **redirecionar URI,** insira **https://localhost:44321/signin-oidc** .
> 1. Selecione **Registar**.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. Para **URL de logout de canal frontal,** insira **https://localhost:44321/signout-oidc** .
> 1. Sob **subvenção implícita e fluxos híbridos,** selecione **fichas de identificação**.
> 1. Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código neste arranque rápido funcione:
> - Para **redirecionar URI,** insira **https://localhost:44321/** e . **https://localhost:44321/signin-oidc** .
> - Para **URL de logout de canal frontal,** insira **https://localhost:44321/signout-oidc** . 
>
> O ponto final de autorização emitirá fichas de identificação de pedido.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-aspnet-core-project"></a>Passo 2: Descarregue o projeto ASP.NET Core

> [!div renderon="docs"]
> [Descarregue a solução ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configuramos o seu projeto com valores das propriedades da sua aplicação, e está pronto para funcionar.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Passo 3: Configurar o seu projeto ASP.NET Core
> 1. Extraia o arquivo .zip numa pasta local perto da raiz da sua unidade. Por exemplo, extrair em *C:\Azure-Samples*.
> 
>    Recomendamos extrair o arquivo num diretório perto da raiz da sua unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
> 1. Abra a solução no Visual Studio 2019.
> 1. Abra a *appsettings.jsno* ficheiro e modifique o seguinte código:
>
>    ```json
>    "Domain": "Enter the domain of your tenant, e.g. contoso.onmicrosoft.com",
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - `Enter_the_Application_Id_here`Substitua-se pelo ID de aplicação (cliente) da aplicação que registou no portal Azure. Pode encontrar o valor de **ID da Aplicação (cliente)** na **página** geral da aplicação.
>    - Substitua `common` por uma das seguintes:
>       - Se a sua candidatura suporta **apenas contas neste diretório organizacional,** substitua este valor pelo ID do diretório (inquilino) (a GUID) ou pelo nome do inquilino (por exemplo, `contoso.onmicrosoft.com` ). Pode encontrar o valor de ID do **Diretório (inquilino)** na **página** geral da aplicação.
>       - Se a sua aplicação suportar **contas em qualquer diretório organizacional,** substitua este valor por `organizations` .
>       - Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** deixe este valor como `common` .
>
> Para este arranque rápido, não altere quaisquer outros valores no *appsettings.jsno* ficheiro.
>
> #### <a name="step-4-build-and-run-the-application"></a>Passo 4: compilar e executar a aplicação
>
> Construa e execute a aplicação no Visual Studio selecionando o menu **Debug** > **Start Debugging**, ou premindo a tecla F5.
>
> É solicitado para as suas credenciais, e depois solicitado para consentir com as permissões que a sua aplicação requer. **Selecione Aceite** na solicitação de consentimento.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Screenshot da caixa de diálogo de consentimento, mostrando as permissões que a aplicação está solicitando ao utilizador.":::
>
> Depois de consentir com as permissões solicitadas, a aplicação mostra que assinou com sucesso com as suas credenciais de Diretório Azure Ative.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Screenshot de um navegador web que mostra a aplicação web em execução e o utilizador inscrito.":::

## <a name="more-information"></a>Mais informações

Esta secção fornece uma visão geral do código necessário para iniciar seduções nos utilizadores. Esta visão geral pode ser útil para entender como o código funciona, quais são os principais argumentos, e como adicionar o início de sing-in a uma aplicação core ASP.NET existente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Como funciona a amostra
>
> ![Diagrama da interação entre o navegador web, a aplicação web e a plataforma de identidade da Microsoft na aplicação da amostra.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de arranque

O middleware *Microsoft.AspNetCore.Authentication* utiliza uma `Startup` classe que é executada quando o processo de hospedagem começa:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

O `AddAuthentication()` método configura o serviço para adicionar autenticação baseada em cookies. Esta autenticação é utilizada em cenários de navegador e para definir o desafio para o OpenID Connect.

A linha que contém `.AddMicrosoftIdentityWebApp` adiciona a autenticação da plataforma de identidade da Microsoft à sua aplicação. A aplicação é então configurada para assinar nos utilizadores com base nas seguintes informações na `AzureAD` secção doappsettings.js *no* ficheiro de configuração:

| *appsettings.jsna* chave | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Identificação da aplicação (cliente) da aplicação registada no portal Azure.                                                                                       |
| `Instance`             | Serviço de ficha de segurança (STS) para o utilizador autenticar. Este valor é tipicamente `https://login.microsoftonline.com/` , indicando a nuvem pública Azure. |
| `TenantId`             | Nome do seu inquilino ou identificação do inquilino (um GUID), ou `common` para assinar em utilizadores com contas de trabalho ou escola ou contas pessoais da Microsoft.                             |

O `Configure()` método contém dois métodos importantes `app.UseAuthentication()` `app.UseAuthorization()` e, que permitem a sua funcionalidade nomeada. Também no `Configure()` método, deve registar as rotas web da Microsoft Identity com pelo menos uma chamada `endpoints.MapControllerRoute()` para: `endpoints.MapControllers()`

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Atributo para proteger um controlador ou métodos

Pode proteger um controlador ou métodos de controlador utilizando o `[Authorize]` atributo. Este atributo restringe o acesso ao controlador ou métodos, permitindo apenas utilizadores autenticados. Um desafio de autenticação pode então ser iniciado a aceder ao controlador se o utilizador não for autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

O repo GitHub que contém este tutorial ASP.NET Core inclui instruções e mais amostras de código que mostram como:

- Adicione a autenticação a uma nova aplicação web core ASP.NET.
- Ligue para o Microsoft Graph, outros APIs da Microsoft ou para as suas próprias APIs web.
- Adicione autorização.
- Inscreva-se nos utilizadores em nuvens nacionais ou com identidades sociais.

> [!div class="nextstepaction"]
> [ASP.NET tutoriais de aplicativos web Core no GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
