---
title: Compilar uma API Web .NET que se integra com o Azure AD para autenticação e autorização | Microsoft Docs
description: Como compilar uma API Web MVC .NET que se integra com o Azure AD para autenticação e autorização.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83f5b08e5fee17c0ea5577d4d56d4d3208a818e3
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625297"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Início rápido: Criar uma API que se integra com o Azure AD para autenticação e autorização de web do .NET

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Se estiver a compilar uma aplicação que concede acesso a recursos protegidos, precisa de saber como impedir o acesso indesejado a esses recursos. O Azure Active Directory (Azure AD) torna simples e direto ajudar a proteger uma API Web, através de tokens de acesso de portador do OAuth 2.0 com apenas algumas linhas de código.

Nas aplicações Web ASP.NET, pode conseguir essa proteção através da implementação da Microsoft do middleware OWIN centrado na comunidade e incluído no .NET Framework 4.5. Aqui irá utilizar o OWIN para criar uma API Web de "Lista de Tarefas" que:

* Designa as APIs que serão protegidas.
* Valida que as chamadas à API Web contêm um token de acesso válido.

Neste início rápido, vai compilar a API Lista de Tarefas e aprender a:

1. Registar uma aplicação com o Azure AD.
2. Configurar a aplicação para utilizar o pipeline de autenticação OWIN.
3. Configurar uma aplicação cliente para chamar a API Web.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos:

* [Transfira a estrutura da aplicação](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [transfira o exemplo concluído](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Cada um deles é uma solução do Visual Studio 2013.
* Ter um inquilino do Azure AD no qual irá registar a aplicação. Se ainda não tiver um, [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Passo 1: Registar uma aplicação com o Azure AD

Para ajudar a proteger a aplicação, primeiro tem de criar uma aplicação no seu inquilino e fornecer algumas informações essenciais ao Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Escolha o seu inquilino do Azure AD ao selecionar a sua conta no canto superior direito da página, selecione o painel de navegação **Trocar diretório** e, em seguida, selecione o inquilino adequado.
    * Ignore este passo, se tiver apenas um inquilino do Azure AD na sua conta ou se já tiver selecionado o inquilino adequado do Azure AD.

3. No painel de navegação do lado esquerdo, selecione **Azure Active Directory**.
4. Selecione **registos de aplicações**e, em seguida, selecione **novo registo**.
5. Quando o **registar uma aplicação** é apresentada a página, introduza um nome para a sua aplicação.
Sob **tipos de conta suportados**, selecione **contas em qualquer diretório organizacional e contas Microsoft pessoais**.
6. Selecione o **Web** plataforma sob a **URI de redirecionamento** secção e defina o valor como `https://localhost:44321/` (o local para o qual o Azure AD irá devolver tokens).
7. Quando terminar, selecione **Registar**. Na aplicação **descrição geral** página, tome nota da **ID de aplicação (cliente)** valor.
6. Selecione **expor uma API**, em seguida, atualize o URI de ID de aplicação ao clicar em **definir**. Introduza um identificador específico do inquilino. Por exemplo, introduza `https://contoso.onmicrosoft.com/TodoListService`.
7. Guarde a configuração. Deixe o portal aberto, pois também precisará de registar a aplicação cliente em breve.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Passo 2: Configurar a aplicação para utilizar o pipeline de autenticação OWIN

Para validar os pedidos e tokens recebidos, terá de configurar a aplicação para comunicar com o Azure AD.

1. Para começar, abra a solução e adicione os pacotes NuGet de middleware OWIN ao projeto TodoListService com a Consola do Gestor de Pacotes.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adicione uma classe de arranque do OWIN ao projeto TodoListService com o nome `Startup.cs`.  Clique com o botão direito do rato no projeto, selecione **Adicionar > Novo Item** e, em seguida, procure **OWIN**. O middleware da OWIN vai invocar o método `Configuration(…)` quando a aplicação for iniciada.

3. Altere a declaração de classe para `public partial class Startup`. Já Implementámos parte desta classe por si noutro ficheiro. No método `Configuration(…)`, faça uma chamada a `ConfigureAuth(…)`, para configurar a autenticação da aplicação Web.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Abra o ficheiro `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(…)`. Os parâmetros fornecidos em `WindowsAzureActiveDirectoryBearerAuthenticationOptions` irão servir como coordenadas para a aplicação comunicar com o Azure AD. Para usá-las precisará usar classes a `System.IdentityModel.Tokens` espaço de nomes.

    ```csharp
    using System.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Utilize os atributos `[Authorize]` para ajudar a proteger os controladores e as ações com a autenticação de portador do JSON Web Token (JWT). Decore a classe `Controllers\TodoListController.cs` com uma etiqueta para autorizar, o que força o utilizador a iniciar sessão antes de aceder a essa página.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Quando um autor de chamada autorizado invoca com êxito umas das APIs `TodoListController`, a ação poderá precisar de acesso a informações sobre o autor da chamada. O OWIN fornece acesso às afirmações dentro do token de portador através do objeto `ClaimsPrincpal`.  

6. Um requisito comum para as APIs Web é validar os "âmbitos" presentes no token para garantir que o utilizador consentiu as permissões necessárias para aceder ao Serviço Lista de Tarefas.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Abra o ficheiro `web.config` na raiz do projeto TodoListService e introduza os valores da configuração na secção `<appSettings>`.
    * `ida:Tenant` é o nome do inquilino do Azure AD – por exemplo, contoso.onmicrosoft.com.
    * `ida:Audience` é o URI do ID da Aplicação, que introduziu no portal do Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Passo 3: Configurar uma aplicação cliente e executar o serviço

Antes de poder ver o Serviço Lista de Tarefas, tem de configurar o cliente Lista de Tarefas para que este consiga obter tokens do Azure AD e solicitar o serviço.

1. Regresse ao [portal do Azure](https://portal.azure.com).
1. Crie um novo registo de aplicação no inquilino do Azure AD.  Introduza um **Name** que descreva a aplicação aos utilizadores, introduza `http://TodoListClient/` para o **URI de redirecionamento** valor e selecione **cliente público (ambiente de trabalho e móvel)** no lista pendente.
1. Depois de terminar o registo, o Azure AD atribui um ID exclusivo à sua aplicação. Este valor será necessário nos passos seguintes, por conseguinte, copie-o da página da aplicação.
1. Selecione **permissões de API**, em seguida, **adicionar uma permissão**.  Localize e selecione o para fazer lista serviço, adicione a **user_impersonation acesso TodoListService** permissão sob **permissões delegadas**e, em seguida, selecione **adicionar permissões**.
1. No Visual Studio, abra `App.config` no projeto TodoListClient e, em seguida, introduza os valores de configuração na secção `<appSettings>`.

    * `ida:Tenant` é o nome do inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com.
    * `ida:ClientId` é o ID da aplicação, que copiou do portal do Azure.
    * `todo:TodoListResourceId` é o URI do ID da aplicação Serviço Lista de Tarefas, que introduziu no portal do Azure.

1. Limpe, compile e execute cada projeto.
1. Se ainda não o tiver feito, crie um novo utilizador no seu inquilino com um domínio *.onmicrosoft.com.
1. Inicie sessão no cliente Lista de Tarefas com esse utilizador e adicione algumas tarefas à lista de tarefas do utilizador.

## <a name="next-steps"></a>Passos Seguintes

* Para sua referência, transfira o exemplo concluído (sem os valores de configuração) do [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Agora pode avançar para outros cenários de identidade.
