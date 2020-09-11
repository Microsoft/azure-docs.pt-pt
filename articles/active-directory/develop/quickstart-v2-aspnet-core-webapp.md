---
title: Adicione o s-in com a Microsoft para ASP.NET aplicações web Core [ Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como implementar o sôd in da Microsoft numa aplicação web core ASP.NET utilizando o OpenID Connect
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
ms.openlocfilehash: 7e8cea18999bec34eba6630a96dca089fd4725af
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016340"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Quickstart: Adicione o início de sôs-in com a Microsoft a uma aplicação web core ASP.NET

Neste quickstart, você usa uma amostra de código para saber como uma aplicação web core ASP.NET pode assinar em contas pessoais (hotmail.com, outlook.com, outros) e contas de trabalho e escola de qualquer instância do Azure Ative Directory (Azure AD). (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: registar e configurar manualmente a aplicação e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao [portal Azure - Inscrições de aplicações.](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar manualmente as informações de registo da aplicação à sua solução, siga estes passos:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com).
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de Aplicações,** em seguida, **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `AspNetCore-Quickstart` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Insira um **URI redirecionado** de `https://localhost:44321/`
> 1. Selecione **Registar**.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. Em **Redirecionar URIs**, selecione **Add URI**, e, em seguida, insira `https://localhost:44321/signin-oidc`
> 1. Insira um **URL logout** de `https://localhost:44321/signout-oidc`
> 1. Em **Concessão implícita**, selecione **Tokens de ID**.
> 1. Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código para este arranque rápido funcione, é necessário adicionar URLs de resposta como `https://localhost:44321/` e , adicione o URL `https://localhost:44321/signin-oidc` logoto como , e `https://localhost:44321/signout-oidc` solicite fichas de identificação para serem emitidas pelo ponto final de autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Passo 2: Descarregue o seu projeto ASP.NET Core

> [!div renderon="docs"]
> [Descarregue a solução ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Executar o projeto.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para funcionar.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>Passo 3: Configurar o seu projeto ASP.NET Core
> 1. Extraia o arquivo .zip numa pasta local perto da raiz da sua unidade. Por exemplo, em *C:\Azure-Samples*.
> 1. Abra a solução no Visual Studio 2019.
> 1. Abra a *appsettings.jsno* ficheiro e modifique o seguinte:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - Substitua `Enter_the_Application_Id_here` pelo ID de **Aplicação (cliente)** da aplicação que registou no portal Azure. Pode encontrar **iD de aplicação (cliente)** na **página** geral da aplicação.
>    - Substitua `common` por uma das seguintes:
>       - Se a sua candidatura apoiar **apenas neste diretório organizacional,** substitua este valor pelo **ID do Diretório (inquilino)** (um **GUIADO)** ou nome de inquilino (por exemplo, `contoso.onmicrosoft.com` ). Pode encontrar o ID do **Diretório (inquilino)** na **página** geral da aplicação.
>       - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>       - Se a sua aplicação suportar **todos os utilizadores da conta microsoft,** deixe este valor como `common`
>
> Para este arranque rápido, não altere quaisquer outros valores no *appsettings.jsficheiro.*
>
> #### <a name="step-4-build-and-run-the-application"></a>Passo 4: compilar e executar a aplicação
> 
> Construa e execute a aplicação no Visual Studio selecionando o menu **Debug** > **Start Debugging**, ou premindo a `F5` chave.
> 
> É solicitado para as suas credenciais, e depois solicitado para consentir com as permissões que a sua aplicação requer. **Selecione Aceite** na solicitação de consentimento.
> 
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Diálogo de consentimento mostrando as permissões que a aplicação está solicitando do utilizador >":::
> 
> Depois de consentir com as permissões solicitadas, a aplicação apresenta que fez login com sucesso usando as suas credenciais de Diretório Azure Ative.
> 
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Web browser exibindo a aplicação web em execução e o utilizador assinou em":::

## <a name="more-information"></a>Mais informações

Esta secção fornece uma visão geral do código necessário para iniciar seduções nos utilizadores. Esta visão geral pode ser útil para entender como o código funciona, principais argumentos, e também se você quiser adicionar o início de sôr-in a uma aplicação core ASP.NET existente.

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Classe de arranque

O middleware *Microsoft.AspNetCore.Authentication* utiliza uma `Startup` classe executada quando o processo de hospedagem iniciais:

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

O `AddAuthentication()` método configura o serviço para adicionar autenticação baseada em cookies, que é usada em cenários de navegador e para definir o desafio para OpenID Connect.

A linha que contém `.AddMicrosoftIdentityWebApp` adiciona a autenticação da plataforma de identidade da Microsoft à sua aplicação. Em seguida, é configurado para iniciar sação usando o ponto final da plataforma de identidade da Microsoft com base nas informações `AzureAD` na secção doappsettings.js* no* ficheiro de configuração:

| *appsettings.jsna* chave | Descrição                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **Identificação da aplicação (cliente)** da aplicação registada no portal Azure.                                                                                       |
| `Instance`             | Serviço de ficha de segurança (STS) para o utilizador autenticar. Este valor é tipicamente `https://login.microsoftonline.com/` , indicando a nuvem pública Azure. |
| `TenantId`             | Nome do seu inquilino ou do seu ID do inquilino (um GUID), ou *comum* para assinar em utilizadores com contas de trabalho ou escola ou contas pessoais da Microsoft.                             |

O `Configure()` método contém dois métodos importantes `app.UseCookiePolicy()` `app.UseAuthentication()` e, que permitem a sua funcionalidade nomeada.

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Pode proteger um controlador ou métodos de controlador utilizando o `[Authorize]` atributo. Este atributo restringe o acesso ao controlador ou métodos apenas permitindo utilizadores autenticados, o que significa que o desafio de autenticação pode ser iniciado para aceder ao controlador se o utilizador não for autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

O repo GitHub que contém este tutorial ASP.NET Core inclui instruções e mais amostras de código que mostram como:

- Adicionar autenticação a uma nova aplicação core web ASP.NET
- Ligue para o Microsoft Graph, outros APIs da Microsoft ou para as suas próprias APIs web
- Adicionar autorização
- Inscreva-se nos utilizadores em nuvens nacionais ou com identidades sociais

> [!div class="nextstepaction"]
> [ASP.NET tutoriais de aplicativos web Core no GitHub](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
