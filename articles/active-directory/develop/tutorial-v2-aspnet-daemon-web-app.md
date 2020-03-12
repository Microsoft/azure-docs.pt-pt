---
title: Construa um daemon multiinquilino que usa o ponto final da plataforma de identidade da Microsoft
description: Neste tutorial, aprenda a chamar uma API web ASP.NET protegida pelo Azure Ative Directory a partir de uma aplicação de desktop Windows (WPF). O cliente WPF autentica um utilizador, solicita um sinal de acesso e chama a Web API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 635b12cc2ffc4d318eaaa74fffc17e4ce4d58c0b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129961"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Tutorial: Construa um daemon multiinquilino que usa o ponto final da plataforma de identidade da Microsoft

Neste tutorial, aprende-se a usar a plataforma de identidade da Microsoft para aceder aos dados dos clientes empresariais da Microsoft num processo de longa duração e não interativo. O daemon da amostra usa a bolsa de credenciais de [cliente OAuth2](v2-oauth2-client-creds-grant-flow.md) para adquirir um sinal de acesso. O daemon usa então o símbolo para ligar para o [Microsoft Graph](https://graph.microsoft.io) e aceder a dados organizacionais.

> [!div class="checklist"]
> * Integrar uma app daemon com a plataforma de identidade da Microsoft
> * Conceder permissões de pedido diretamente para a app por um administrador
> * Obtenha um sinal de acesso para ligar para a Microsoft Graph API
> * Ligue para a API do Microsoft Graph.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

A aplicação é construída como uma aplicação ASP.NET MVC. Utiliza o middleware OWIN OpenID Connect para iniciar sessão nos utilizadores.  

O componente "daemon" nesta amostra é um controlador API, `SyncController.cs`. Quando o controlador é chamado, ele puxa uma lista de utilizadores no inquilino Azure Ative Directory (Azure AD) do cliente da Microsoft Graph. `SyncController.cs` é desencadeada por uma chamada do AJAX na aplicação web. Utiliza a [Microsoft Authentication Library (MSAL) para .NET](msal-overview.md) adquirir um token de acesso para o Microsoft Graph.

>[!NOTE]
> Se for novidade na plataforma de identidade da Microsoft, recomendamos que comece com o [quickstart .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Cenário

Uma vez que a aplicação é uma aplicação multiarrendatária para clientes empresariais da Microsoft, deve fornecer uma forma de os clientes "inscreverem-se" ou "ligarem" a aplicação aos dados da sua empresa. Durante o fluxo de ligação, um administrador da empresa concede pela primeira vez *permissões* de aplicação diretamente à app para que possa aceder aos dados da empresa de forma não interativa, sem a presença de um utilizador inscrito. A maior parte da lógica nesta amostra mostra como alcançar este fluxo de conexão utilizando o ponto final de [consentimento](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) da plataforma de identidade.

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Para obter mais informações sobre os conceitos utilizados nesta amostra, leia a documentação do protocolo de credenciais do [cliente para o ponto final da plataforma de identidade.](v2-oauth2-client-creds-grant-flow.md)

## <a name="prerequisites"></a>Pré-requisitos

Para executar a amostra neste arranque rápido, você precisará:

- [Estúdio Visual 2017 ou 2019.](https://visualstudio.microsoft.com/downloads/)
- Um inquilino do Azure AD. Para mais informações, consulte [Como obter um inquilino da AD Azure.](quickstart-create-new-tenant.md)
- Uma ou mais contas de utilizador no seu inquilino Azure AD. Esta amostra não funcionará com uma conta Microsoft (anteriormente conta Windows Live). Se inscreveu no [portal Azure](https://portal.azure.com) com uma conta Microsoft e nunca criou uma conta de utilizador no seu diretório, tem de o fazer agora.

## <a name="clone-or-download-this-repository"></a>Clone ou descarregue este repositório

A partir da sua concha ou linha de comando, insira este comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [descarregue a amostra num ficheiro postal.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)

## <a name="register-your-application"></a>Registar a sua aplicação

Esta amostra tem um projeto. Para registar o pedido junto do seu inquilino Azure AD, pode:

- Siga os passos em Registar a amostra com o seu inquilino do [Diretório Ativo Azure](#register-your-application) e [configure a amostra para utilizar o seu inquilino Azure AD](#choose-the-azure-ad-tenant).
- Utilize scripts PowerShell que:
  - Crie *automaticamente* as aplicações da AD Azure e objetos relacionados (palavras-passe, permissões, dependências) para si.
  - Modificar os ficheiros de configuração dos projetos do Estúdio Visual.

Se quiser utilizar a automatização:

1. No Windows, execute a PowerShell e vá para a raiz do diretório clonado.
1. Execute este comando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Execute o script para criar a sua aplicação Azure AD e configure o código da aplicação da amostra em conformidade:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Outras formas de executar scripts são descritas em scripts de criação de [aplicativos.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Abra a solução Visual Studio e selecione **Iniciar** a execução do código.

Se não quiser utilizar a automatização, utilize os passos nas seguintes secções.

### <a name="choose-the-azure-ad-tenant"></a>Escolha o inquilino da AD Azure

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. Se a sua conta estiver em mais de um inquilino DaD Azure, selecione o seu perfil no menu no topo da página e, em seguida, selecione **diretório Switch**.
1. Mude a sua sessão do portal para o desejado inquilino da AD Azure.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registe a aplicação do cliente (dotnet-web-daemon-v2)

1. Vá à página de [registos](https://go.microsoft.com/fwlink/?linkid=2083908) da App na plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **Novo registo**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação. Por exemplo, introduza **dotnet-web-daemon-v2**.
   - Na secção de tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional**.
   - Na secção **Redirecionamento URI (opcional),** selecione **Web** na caixa de combinação e introduza as seguintes URIs redirecionais:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Se houver mais de dois URIs redirecionados, terá de adicioná-los mais tarde ao separador **Autenticação,** depois de a aplicação ser criada com sucesso.
1. Selecione **Registar** para criar a aplicação.
1. Na página **de Visão Geral** da aplicação, encontre o valor de ID da **Aplicação (cliente)** e grave-o para mais tarde. Vai precisar dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto.
1. Na lista de páginas da aplicação, selecione **Autenticação**. Em seguida:
   - Na secção **de definições Avançadas,** coloque **o URL de logout** para **https://localhost:44316/Account/EndSession** .
   - Nas **definições avançadas** > secção **de subvenção implícita,** selecione **fichas** de acesso e **fichas de IDENTIFICAção**. Esta amostra requer que o fluxo implícito de [subvenção](v2-oauth2-implicit-grant-flow.md) seja ativado para assinar no utilizador e chamar uma API.
1. Selecione **Guardar**.
1. A partir da página **Certificados e segredos,** na secção **de segredos** do Cliente, selecione **novo segredo de cliente.** Em seguida:

   1. Introduza uma descrição chave (por exemplo, segredo de **aplicação),**
   1. Selecione uma duração chave de **qualquer um em 1 ano,** em 2 **anos,** ou **nunca expire**.
   1. Selecione o botão **Adicionar.** 
   1. Quando o valor-chave aparecer, copie e guarde-o num local seguro. Mais tarde, vai precisar desta chave para configurar o projeto no Estúdio Visual. Não será exibido novamente ou recuperado por qualquer outro meio.
1. Na lista de páginas da aplicação, selecione **permissões API**. Em seguida:
   1. Selecione o botão **Adicionar uma permissão**.
   1. Certifique-se de que o separador **APIs** da Microsoft está selecionado.
   1. Na secção APIs da **Microsoft comumente utilizada,** selecione **Microsoft Graph**.
   1. Na secção **de permissões da Aplicação,** certifique-se de que as permissões certas são selecionadas: **User.Read.All**.
   1. Selecione o botão **adicionar permissões.**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configure a amostra para usar o seu inquilino Azure AD

Nos seguintes passos, o **ClientID** é o mesmo que "ID de aplicação" ou **AppId**.

Abra a solução no Estúdio Visual para configurar os projetos.

### <a name="configure-the-client-project"></a>Configure o projeto do cliente

Se usou os scripts de configuração, serão aplicadas as seguintes alterações para si.

1. Abra o ficheiro **UserSync\Web.Config.**
1. Encontre a chave da aplicação **ida:ClientId**. Substitua o valor existente pelo ID de aplicação da aplicação **dotnet-web-daemon-v2** copiada do portal Azure.
1. Encontre a chave da aplicação **ida:ClientSecret**. Substitua o valor existente pela chave que guardou durante a criação da aplicação **dotnet-web-daemon-v2** no portal Azure.

## <a name="run-the-sample"></a>Executar o exemplo

Limpe a solução, reconstrua a solução, execute a aplicação UserSync e, em seguida, inscreva-se como administrador no seu inquilino Azure AD. Se não tiver um inquilino da AD Azure para testes, pode [seguir estas instruções](quickstart-create-new-tenant.md) para obter uma.

Quando faz o início o seu início, a aplicação pede-lhe pela primeira vez permissão para iniciar sessão e ler o seu perfil de utilizador. Este consentimento permite que a app garanta que é um utilizador de negócios.

![Consentimento do utilizador](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

A aplicação tenta então sincronizar uma lista de utilizadores do seu inquilino Azure AD, via Microsoft Graph. Se não puder, pede-lhe (o administrador do inquilino) que ligue o seu inquilino à app.

A aplicação pede então permissão para ler a lista de utilizadores do seu inquilino.

![Consentimento do administrador](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Depois de conceder permissão, está assinado na aplicação. Este sign-out garante que quaisquer fichas de acesso existentes para o Microsoft Graph são removidas da cache simbólica. Quando voltar a entrar, o símbolo fresco que é obtido terá as permissões necessárias para fazer chamadas para o Microsoft Graph.


Quando concede a permissão, a aplicação pode então consultar os utilizadores em qualquer momento. Pode verificar isto selecionando o botão **Sync Users** e refrescando a lista de utilizadores. Tente adicionar ou remover um utilizador e resincronizar a lista. (Mas note que a aplicação sincroniza apenas a primeira página dos utilizadores.)

## <a name="about-the-code"></a>Sobre o código

O código relevante para esta amostra encontra-se nos seguintes ficheiros:

- **App_Start\Startup.Auth.cs**, **Controladores\AccountController.cs**: Iniciar sessão. Em particular, as ações no controlador têm um atributo **Autorizado,** que obriga o utilizador a iniciar o contrato. A aplicação utiliza o fluxo de [código de autorização](v2-oauth2-auth-code-flow.md) para assinar no utilizador.
- **Controladores\SyncController.cs**: Sincronizar a lista de utilizadores na loja local de memória.
- **Controladores\UserController.cs**: Exibir a lista de utilizadores da loja local de memória.
- **Controladores\AccountController.cs**: Aquisição de permissões do administrador do inquilino utilizando o ponto final do consentimento do administrador.

## <a name="re-create-the-sample-app"></a>Recriar a aplicação de amostra

1. No Visual Studio, crie um novo projeto de Aplicação Web **visual C#**  **ASP.NET (.NET Framework).** 
1. No ecrã seguinte, escolha o modelo de projeto **MVC.** Adicione também pastas e referências fundamentais para **Web API,** porque adicionará um controlador Web API mais tarde. Deixe o modo de autenticação escolhido do projeto como predefinido: **Sem Autenticação**.
1. Selecione o projeto na janela **Solution Explorer** e selecione a tecla **F4.** 
1. Nas propriedades do projeto, coloque **o SSL Habilitado** para **O Verdadeiro**. Note a informação no **URL SSL**. Você precisará dele ao configurar o registo desta aplicação no portal Azure.
1. Adicione os seguintes pacotes ASP.NET de middleware NuGet: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. Na pasta **App_Start:**
   1. Crie uma classe chamada **Startup.Auth.cs.** 
   1. Retire **. App_Start** do nome do espaço de nome. 
   1. Substitua o código para a classe **Startup** com o código do mesmo ficheiro da aplicação de amostra.       
   Certifique-se de tomar toda a definição de classe. A definição muda de startup de **classe pública** para classe pública classe **parcial Startup.**
1. Em **Startup.Auth.cs**, resolva as referências em falta adicionando **declarações como** sugerido pelo Visual Studio IntelliSense.
1. Clique no projeto à direita, selecione **Adicionar**, e, em seguida, selecione **Class**.
1. Na caixa de pesquisa, introduza **OWIN**. **A classe OWIN Startup** aparece como uma seleção. Selecione-o e nomeie a classe **Startup.cs**.
1. Em **Startup.cs,** substitua o código para a classe **Startup** pelo código do mesmo ficheiro da aplicação da amostra. Mais uma vez, note que a definição muda de startup de **classe pública** para classe parcial **pública Startup**.
1. Na pasta **Models,** adicione uma nova classe chamada **MsGraphUser.cs**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Adicione um novo **Controlador MVC 5 - Instância vazia** chamada **AccountController**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Adicione um novo **controlador MVC 5 - Instância vazia** chamada **UserController**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Adicione um novo **controlador Web API 2 - Instância vazia** chamada **SyncController**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Para a interface do utilizador, na pasta **Views\Account,** adicione três situações **de visualização vazias (sem modelo)** chamadas **GrantPermissions**, **Index**, e **UserMismatch**. Adicione e um **índice** nomeado na pasta **Views\User.** Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Update **Shared\_Layout.cshtml** e **Home\Index.cshtml** para ligar corretamente as várias vistas.

## <a name="deploy-the-sample-to-azure"></a>Implante a amostra para Azure

Este projeto tem web app e projetos web API. Para implantá-los em websites do Azure, dê os seguintes passos para cada um:

1. Crie um site Azure.
1. Publique a aplicação web e as APIs web no site.
1. Atualize os clientes para ligar para o site em vez do IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Criar e publicar dotnet-web-daemon-v2 para um site azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No canto superior esquerdo, selecione **Criar um recurso**.
1. Selecione **Web** > **Web App**e, em seguida, dê um nome ao seu website. Por exemplo, **nomeie-o dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Selecione as informações para **Subscrição,** **Grupo de Recursos,** plano de serviço de **aplicações e localização.** **OS** é **Windows**, e **Publicar** é **Código**.
1. Selecione **Criar** e aguarde a criação do serviço de aplicações.
1. Quando receber a notificação bem sucedida da **Implementação,** selecione **Ir a recorrer** para ir ao serviço de aplicações recém-criado.
1. Depois de criado o site, encontre-o no **Dashboard** e selecione-o para abrir o ecrã **overview** do serviço de aplicações.
1. A partir do separador **Overview** do serviço de aplicações, descarregue o perfil de publicação selecionando o link de **perfil get publish** e guarde-o. Pode utilizar outros mecanismos de implantação, tais como a implantação a partir do controlo de fonte.
1. Mude para Visual Studio e depois:
   1. Vá ao projeto **dotnet-web-daemon-v2.** 
   1. Clique no projeto no Solution Explorer e, em seguida, **selecione Publicar**.
   1. Selecione **Import Profile** na barra inferior e importe o perfil de publicação que descarregou anteriormente.
1. **Selecione Configurar**.
1. No separador **Ligação,** atualize o URL de destino de modo a que utilize "https". Por exemplo, use [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Selecione **Seguinte**.
1. No separador **Definições,** certifique-se de que **a autenticação organizacional ativa** está limpa.  
1. Selecione **Guardar**. Selecione **Publicar** no ecrã principal.

O Visual Studio publicará o projeto e abrirá automaticamente um navegador para o URL do projeto. Se vir a página padrão do projeto, a publicação foi bem sucedida.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Atualizar o registo de candidatura de inquilino da Azure AD para dotnet-web-daemon-v2

1. Regresse ao [portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure** e, em seguida, selecione **as inscrições**da App .
1. Selecione a aplicação **dotnet-web-daemon-v2.**
1. Na página **de Autenticação** para a sua aplicação, atualize os campos DE URL de **Logout** com o endereço do seu serviço. Por exemplo, use [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. A partir do menu **Branding,** atualize o URL da **página inicial** para o endereço do seu serviço. Por exemplo, use [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Guarde a configuração.
1. Adicione o mesmo URL na lista de valores do menu De **autenticação** > **Redirecionamento DE URIs.** Se tiver vários URLs redirecionados, certifique-se de que existe uma nova entrada que utiliza o URI do serviço de aplicações para cada URL de redirecionamento.

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, elimine o objeto de aplicação que criou no registo da sua etapa de [aplicação.](#register-your-application)  Para remover a aplicação, siga as instruções em [Remover uma aplicação da autoria de si ou da sua organização](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Obter ajuda

Use [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) para obter apoio da comunidade.
Faça as suas perguntas sobre stack overflow primeiro, e navegue nos problemas existentes para ver se alguém já fez a sua pergunta antes.
Certifique-se de que as suas perguntas ou comentários estão marcados com "adal", "msal" e "dotnet".

Se encontrar um inseto na amostra, por favor levante a questão sobre as questões do [GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se encontrar um bug em MSAL.NET, por favor levante a questão sobre [MSAL.NET Questões GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Para fornecer uma recomendação, vá à [página de Voz do Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os diferentes [fluxos de autenticação e cenários](authentication-flows-app-scenarios.md) de aplicação que a plataforma de identidade da Microsoft suporta.

Para mais informações, consulte a seguinte documentação conceptual:

- [Arrendamento em Diretório Ativo Azure](single-and-multi-tenant-apps.md)
- [Compreender as experiências de consentimento da aplicação do Azure AD](application-consent-experience.md)
- [Inscreva-se em qualquer utilizador do Diretório Ativo Azure utilizando o padrão de aplicação multiarrendatária](howto-convert-app-to-be-multi-tenant.md)
- [Compreender o consentimento do utilizador e da administração](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objetos principais de aplicação e serviço no Diretório Ativo azure](app-objects-and-service-principals.md)
- [Quickstart: Registe uma aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md)
- [Quickstart: Configure uma aplicação de cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md)
- [Aquisição de um símbolo para uma aplicação com fluxos de credenciais de cliente](msal-client-applications.md)

Para uma aplicação de daemon de consola multiarrendatária mais simples, consulte o [.NET Core daemon quickstart](quickstart-v2-netcore-daemon.md).
