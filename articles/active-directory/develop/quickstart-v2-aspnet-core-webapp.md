---
title: Plataforma de identidade do Microsoft início rápido para aplicações web ASP.NET Core | Azure
description: Aprenda a implementar Microsoft início de sessão numa aplicação Web do ASP.NET Core através do OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e51437a99217316ead50d4075be52f089225e618
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190858"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Início rápido: Adicionar início de sessão com a Microsoft a uma aplicação web ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste início rápido, ficará a saber como uma aplicação web ASP.NET Core pode iniciar sessão em contas pessoais (hotmail.com, outlook.com, outras pessoas) e profissionais e escolares contas a partir de qualquer instância do Azure Active Directory (Azure AD).

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá para o [portal do Azure – registos de aplicações](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação num só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar manualmente as informações de registo da aplicação à sua solução, siga estes passos:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página.
> 1. Selecione **novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AspNetCore-Quickstart`.
>    - Na **URI de redirecionamento**, adicione `https://localhost:44321/`e selecione **registar**.
> 1. Selecione o **autenticação** menu e, em seguida, adicione as seguintes informações:
>    - Na **URIs de redirecionamento**, adicione `https://localhost:44321/signin-oidc`e selecione **guardar**.
>    - Na **definições avançadas** secção, defina **URL de fim de sessão** para `https://localhost:44321/signout-oidc`.
>    - Sob **concessão implícita**, verifique **tokens de ID**.
>    - Selecione **Guardar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua aplicação no portal do Azure
> Para o código de exemplo para este início rápido funcionar, terá de adicionar URLs de resposta como `https://localhost:44321/` e `https://localhost:44321/signin-oidc`, adicione o URL de fim de sessão como `https://localhost:44321/signout-oidc`e solicitar tokens de ID para ser emitidos pelo ponto final de autorização.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-aspnet-core-project"></a>Passo 2: Transfira o seu projeto ASP.NET Core

- [Transferir a solução do Visual Studio 2019](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o seu projeto do Visual Studio

1. Por exemplo, a extrair o ficheiro zip para uma pasta local dentro da pasta de raiz - **C:\Azure-Samples**
1. Se usar o Visual Studio 2019, abra a solução no Visual Studio (opcional).
1. Editar a **appSettings** ficheiro. Encontrar `ClientId` e atualize o valor de `ClientId` com o **ID de aplicação (cliente)** valor da aplicação que registou. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here` -é o **ID da aplicação (cliente)** para a aplicação que registou no portal do Azure. Pode encontrar **ID da aplicação (cliente)** da aplicação **descrição geral** página.
> - `Enter_the_Tenant_Info_Here` -é uma das seguintes opções:
>   - Se a sua aplicação suportar **contas apenas neste diretório organizacional**, substitua este valor com o **ID do inquilino** ou **nome do inquilino** (por exemplo, contoso.microsoft.com)
>   - Se a sua aplicação suportar **Contas em qualquer diretório organizacional**, substitua este valor por `organizations`
>   - Se a sua aplicação suportar **Todos os utilizadores com contas Microsoft**, substitua este valor por `common`
>
> > [!TIP]
> > Para encontrar os valores do **ID da Aplicação (cliente)**, o **ID de Diretório (inquilino)** e os **Tipos de conta suportados**, vá para a página **Descrição geral** da aplicação no portal do Azure.

## <a name="more-information"></a>Mais informações

Esta secção fornece uma descrição geral do código necessário para iniciar sessão dos utilizadores. Esta descrição geral pode ser útil para entender como o código funciona, os principais argumentos e também se pretende adicionar início de sessão a uma aplicação ASP.NET Core existente.

### <a name="startup-class"></a>Classe de inicialização

*Microsoft.AspNetCore.Authentication* middleware usa uma classe de inicialização que é executada quando o processo de hospedagem inicializa:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

O método `AddAuthentication` configura o serviço para adicionar a autenticação baseada em cookies, o que é utilizada em cenários de navegador e definir o desafio com OpenID Connect. 

A linha que contém `.AddAzureAd` adiciona a autenticação de plataforma de identidade do Microsoft à sua aplicação. Em seguida, é configurado para iniciar sessão com o ponto de extremidade de plataforma de identidade Microsoft.

> |Onde  |  |
> |---------|---------|
> | ClientId  | ID da aplicação (cliente) da aplicação registada no portal do Azure. |
> | Autoridade | O ponto de extremidade STS para autenticar o usuário. Normalmente, isto é <https://login.microsoftonline.com/{tenant}/v2.0> para a nuvem pública, em que {inquilino} é o nome do seu inquilino ou o ID de inquilino, ou *comuns* para obter uma referência para o ponto de extremidade comum (utilizado para aplicações multi-inquilino) |
> | TokenValidationParameters | Uma lista de parâmetros para a validação do token. Neste caso, `ValidateIssuer` está definido como `false` para indicar que ele pode aceitar inícios de sessão de qualquer pessoais, ou contas escolares ou profissionais. |


> [!NOTE]
> Definição `ValidateIssuer = false` é uma simplificação para este início rápido. Em aplicativos reais terá de validar o emissor.
> Veja os exemplos para compreender como fazê-lo.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou o método de um controlador

Pode proteger um controlador ou métodos de controlador usando o `[Authorize]` atributo. Este atributo restringe o acesso para o controlador ou métodos, permitindo apenas que usuários autenticados, que significa que o desafio de autenticação pode ser iniciado para acessar o controlador, se o usuário não está autenticado.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Veja o repositório do GitHub para este tutorial de ASP.NET Core para obter mais informações, incluindo instruções sobre como adicionar autenticação para um aplicativo ASP.NET Core Web totalmente novo, como chamar o Microsoft Graph e outras APIs da Microsoft, como chamar as suas próprias APIs, como adicionar autorização, como iniciar sessão dos utilizadores em clouds nacionais ou com identidades sociais e muito mais:

> [!div class="nextstepaction"]
> [Tutorial de aplicação Web do núcleo do ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
