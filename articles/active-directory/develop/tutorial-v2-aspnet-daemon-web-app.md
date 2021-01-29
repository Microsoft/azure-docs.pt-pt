---
title: 'Tutorial: Construa um daemon multi-inquilino que aceda aos dados de negócios do Microsoft Graph | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, aprenda a chamar uma API web ASP.NET protegida pelo Azure Ative Directory a partir de uma aplicação de desktop do Windows (WPF). O cliente WPF autentica um utilizador, solicita um token de acesso e liga para a API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: bc7893746cbb98a2d4adc4dabb39e22d015ab2c8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050401"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Tutorial: Construa um daemon multi-inquilino que usa a plataforma de identidade da Microsoft

Neste tutorial, você descarrega e execute uma aplicação web ASP.NET daemon que demonstra usar as credenciais de cliente OAuth 2.0 para obter um token de acesso para ligar para a Microsoft Graph API.

Neste tutorial:

> [!div class="checklist"]
> * Integrar uma app daemon com a plataforma de identidade da Microsoft
> * Conceder permissões de pedidos diretamente à app por um administrador
> * Obtenha um token de acesso para ligar para a Microsoft Graph API
> * Ligue para a Microsoft Graph API.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/).
- Um inquilino do Azure AD. Para mais informações, consulte [Como obter um inquilino AZure AD.](quickstart-create-new-tenant.md)
- Uma ou mais contas de utilizador no seu inquilino AZure AD. Esta amostra não funciona com uma conta microsoft. Se se inscreveu no [portal Azure](https://portal.azure.com) com uma conta microsoft e nunca criou uma conta de utilizador no seu diretório, faça-o agora.

## <a name="scenario"></a>Scenario

A aplicação foi construída como uma aplicação MVC ASP.NET. Utiliza o middleware OWIN OpenID Connect para iniciar sílsi nos utilizadores.

O componente "daemon" desta amostra é um controlador API, `SyncController.cs` . Quando o controlador é chamado, ele puxa uma lista de utilizadores no inquilino Azure Ative Directory (Azure AD) do cliente do Microsoft Graph. `SyncController.cs` é desencadeada por uma chamada AJAX na aplicação web. Utiliza a [Microsoft Authentication Library (MSAL) para .NET](msal-overview.md) adquirir um token de acesso para o Microsoft Graph.

Uma vez que a aplicação é uma aplicação multi-inquilina para clientes empresariais da Microsoft, deve fornecer uma forma de os clientes se "inscreverem" ou "ligarem" a aplicação aos dados da empresa. Durante o fluxo de ligação, um administrador da empresa concede primeiro permissões de aplicação diretamente à app para que possa aceder aos *dados* da empresa de forma não interativa, sem a presença de um utilizador inscrito. A maior parte da lógica desta amostra mostra como alcançar este fluxo de conexão utilizando o ponto final de consentimento da plataforma de [identidade.](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

![O diagrama mostra a App UserSync com três itens locais ligados ao Azure, com o Start dot Auth a adquirir um símbolo interativamente para ligar ao Azure A D, o AccountController obter o consentimento administrativo para ligar ao Azure A D e o utilizador de leitura SyncController para se ligar ao Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Para obter mais informações sobre os conceitos utilizados nesta amostra, leia a documentação do protocolo de credenciais do [cliente para a plataforma de identidade.](v2-oauth2-client-creds-grant-flow.md)

## <a name="clone-or-download-this-repository"></a>Clone ou descarregue este repositório

A partir da sua concha ou linha de comando, insira este comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [descarregue a amostra num ficheiro zip.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip)

## <a name="register-your-application"></a>Registar a aplicação

Esta amostra tem um projeto. Para registar o pedido com o seu inquilino AZure AD, você pode:

- Siga os passos no [Registo da amostra com o seu inquilino Azure Ative Directory](#register-the-client-app-dotnet-web-daemon-v2) e [Configure a amostra para utilizar o seu inquilino Azure AD](#choose-the-azure-ad-tenant).
- Utilize scripts PowerShell que:
  - *Crie automaticamente* as aplicações AD AD do Azure e objetos relacionados (palavras-passe, permissões, dependências) para si.
  - Modifique os ficheiros de configuração dos projetos do Estúdio Visual.

Se quiser utilizar a automatização:

1. No Windows, gere o PowerShell e vai para a raiz do diretório clonado.
1. Execute este comando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Execute o script para criar a sua aplicação AD Azure e configuure o código da aplicação da amostra em conformidade:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Outras formas de executar scripts são descritas em [scripts de criação de aplicativos.](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Abra a solução Visual Studio e selecione **Comece** a executar o código.

Se não quiser utilizar a automatização, utilize os passos nas seguintes secções.

### <a name="choose-the-azure-ad-tenant"></a>Escolha o inquilino AZure AD

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registar a aplicação do cliente (dotnet-web-daemon-v2)

1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Introduza um **Nome** para a sua aplicação, por `dotnet-web-daemon-v2` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Na secção **tipos de conta suportada,** selecione **Contas em qualquer diretório organizacional**.
1. Na secção **URI de redirecionamento (opcional),** selecione **Web** na caixa de combinação e introduza `https://localhost:44316/` e como `https://localhost:44316/Account/GrantPermissions` URIs de redirecionamento.

    Se existirem mais de dois URIs de redirecionamento, terá de adicioná-los mais **tarde,** depois de a aplicação ser criada com sucesso.
1. Selecione **Registar** para criar a aplicação.
1. Na **página** geral da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para utilização posterior. Vai precisar dele para configurar o ficheiro de configuração do Estúdio Visual para este projeto.
1. Em **Gestão**, **selecione Autenticação**.
1. Desa estação **URL de logotipo do canal** frontal para `https://localhost:44316/Account/EndSession` .
1. Na secção **de concessão implícita,** selecione **Tokens de acesso** e **fichas de identificação**. Esta amostra requer que o [fluxo de subvenção implícito](v2-oauth2-implicit-grant-flow.md) seja habilitado a assinar no utilizador e a chamar uma API.
1. Selecione **Guardar**.
1. Em **Gerir**, selecione **Certificados e segredos**.
1. Na secção **de segredos** do Cliente, selecione **Novo segredo de cliente.** 
1. Introduza uma descrição chave (por exemplo, **app secret).**
1. Selecione uma duração chave de qualquer **um em 1 ano**, Em **2 anos**, ou nunca **expira**.
1. Selecione **Adicionar**. Grave o valor da chave num local seguro. Mais tarde, vai precisar desta chave para configurar o projeto no Visual Studio.
1. Em **Gestão**, selecione **permissões API**  >  **Adicione uma permissão**.
1. Na secção **APIs** da Microsoft, selecione **Microsoft Graph**.
1. Na secção **permissões de aplicação,** certifique-se de que as permissões certas são selecionadas: **User.Read.All**.
1. **Selecione Permissões de adicionar**.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configure a amostra para usar o seu inquilino AZure AD

Nos seguintes passos, **o ClientID** é o mesmo que "ID de aplicação" ou **AppId.**

Abra a solução no Visual Studio para configurar os projetos.

### <a name="configure-the-client-project"></a>Configure o projeto do cliente

Se utilizar os scripts de configuração, terão sido aplicadas as seguintes alterações para si.

1. Abra o ficheiro **UserSync\Web.Config.**
1. Encontre a chave da aplicação **ida:ClientId**. Substitua o valor existente pelo ID de aplicação da aplicação **dotnet-web-daemon-v2** copiada do portal Azure.
1. Encontre a chave da aplicação **ida:ClientSecret**. Substitua o valor existente pela chave que guardou durante a criação da aplicação **dotnet-web-daemon-v2** no portal Azure.

## <a name="run-the-sample"></a>Executar o exemplo

Limpe a solução, reconstrua a solução, execute a aplicação UserSync e, em seguida, inscreva-se como administrador no seu inquilino Azure AD. Se você não tem um inquilino AZure AD para testes, você pode [seguir estas instruções](quickstart-create-new-tenant.md) para obter um.

Quando faz o seu sessão, a aplicação pede-lhe primeiro permissão para o iniciar e ler o seu perfil de utilizador. Este consentimento permite que a app garanta que é um utilizador de negócios.

![Consentimento do utilizador](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

A aplicação tenta então sincronizar uma lista de utilizadores do seu inquilino AZure AD, via Microsoft Graph. Se não puder, pede-lhe (o administrador do inquilino) que ligue o seu inquilino à app.

A aplicação pede então permissão para ler a lista de utilizadores no seu inquilino.

![Consentimento do administrador](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Depois de conceder permissão, é assinado fora da aplicação. Esta sedção garante que quaisquer fichas de acesso existentes para o Microsoft Graph sejam removidas da cache simbólica. Quando iniciar novamente, o token fresco que é obtido terá as permissões necessárias para fazer chamadas para o Microsoft Graph.


Quando conceder a permissão, a aplicação pode então consultar os utilizadores em qualquer ponto. Pode verificar isto selecionando o botão **Sync Users** e refrescando a lista de utilizadores. Tente adicionar ou remover um utilizador e ressívar a lista. (Mas note que a aplicação sincroniza apenas a primeira página dos utilizadores.)

## <a name="about-the-code"></a>Sobre o código

O código relevante para esta amostra encontra-se nos seguintes ficheiros:

- **App_Start\Startup.Auth.cs,** **Controllers\AccountController.cs**: Início de sposição. Em particular, as ações no controlador têm um atributo **Autorizado,** o que obriga o utilizador a iniciar sôm. O pedido utiliza o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md) para assinar no utilizador.
- **Controladores\SyncController.cs**: Sincronizar a lista de utilizadores para a loja de memória local.
- **Controladores\UserController.cs**: Exibindo a lista de utilizadores da loja de memória local.
- **Controladores\Controladores\Controlador de Contacontrolador.cs**: Aquisição de permissões ao administrador do arrendatário utilizando o ponto final de consentimento administrativo.

## <a name="re-create-the-sample-app"></a>Re-criar a aplicação de amostra

1. No Visual Studio, crie um novo projeto **Visual C#** **ASP.NET Web Application (.NET Framework).**
1. No ecrã seguinte, escolha o modelo de projeto **MVC.** Adicione também as referências de pasta e núcleo para **a API Web,** porque adicionará um controlador API web mais tarde. Deixe o modo de autenticação escolhido pelo projeto como padrão: **Sem Autenticação**.
1. Selecione o projeto na janela **'Solução Explorer'** e selecione a tecla **F4.**
1. Nas propriedades do projeto, definir **SSL Enabled** to **True**. Note a informação em **URL SSL**. Vai precisar dele ao configurar o registo desta candidatura no portal Azure.
1. Adicione os seguintes pacotes nuGet de middleware OWIN ASP.NET:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. Na pasta **App_Start:**
   1. Criar uma classe chamada **Startup.Auth.cs.**
   1. Remover **. App_Start** do nome do espaço.
   1. Substitua o código da classe **Startup** pelo código do mesmo ficheiro da aplicação da amostra.
   Certifique-se de tomar toda a definição de classe. A definição muda de **classe pública Startup** para classe parcial pública **Startup.**
1. Em **Startup.Auth.cs**, resolva referências em falta adicionando **declarações** como sugerido pelo Visual Studio IntelliSense.
1. Clique com o botão direito no projeto, **selecione Add** e, em seguida, selecione **Class**.
1. Na caixa de pesquisa, **insira OWIN**. **A classe OWIN Startup** aparece como uma seleção. Selecione-o e nomeie a classe **Startup.cs**.
1. Em **Startup.cs**, substitua o código da classe **Startup** pelo código do mesmo ficheiro da aplicação da amostra. Mais uma vez, note que a definição muda de **classe pública Startup** para classe parcial pública **Startup**.
1. Na pasta **Modelos,** adicione uma nova classe chamada **MsGraphUser.cs**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Adicione um novo **Controlador MVC 5 -** Instância vazia chamada **Controlador de Conta**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Adicione um novo **controlador MVC 5 -** Instância vazia chamada **UserController**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Adicione um novo **controlador Web API 2 -** Instância vazia chamada **SyncController**. Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Para a interface do utilizador, na pasta **Views\Account,** adicione três **visualizações vazias (sem modelo) Versões** chamadas **GrantPermissions**, **Index** e **UserMismatch**. Adicione e um **índice** nomeado na pasta **Views\User.** Substitua a implementação pelo conteúdo do ficheiro com o mesmo nome da amostra.
1. Atualizar **\_ Layout.cshtml** e **Home\Index.cshtml** para ligar corretamente as várias vistas em conjunto.

## <a name="deploy-the-sample-to-azure"></a>Desloque a amostra para Azure

Este projeto tem aplicativos web e projetos web API. Para os implementar nos websites da Azure, tome os seguintes passos para cada um:

1. Crie um website Azure.
1. Publique a aplicação web e as APIs web no site.
1. Atualize os clientes para ligar para o site em vez do IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Criar e publicar dotnet-web-daemon-v2 para um site da Azure

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Selecione **Web**  >  **Web App** e, em seguida, dê ao seu site um nome. Por exemplo, diga-lhe **dotnet-web-daemon-v2-contoso.azurewebsites.net.**
1. Selecione as informações para **Subscrição,** **Grupo de Recursos** e Plano de Serviço de **Aplicação e localização.** **O SO** é **Windows**, e **publicar** é **código.**
1. Selecione **Criar** e esperar que o serviço de aplicações seja criado.
1. Quando receber a notificação **conseguida da Implementação,** selecione **Ir para** o recurso para ir ao serviço de aplicações recém-criado.
1. Depois de o site ser criado, encontre-o no **Dashboard** e selecione-o para abrir o **ecrã** geral do serviço de aplicações.
1. A partir do **separador Visão Geral** do serviço de aplicações, descarregue o perfil de publicação selecionando o link **de perfil de publicação Get** e guarde-o. Pode utilizar outros mecanismos de implantação, tais como a implantação a partir do controlo de origem.
1. Mude para Visual Studio e depois:
   1. Vá ao projeto **dotnet-web-daemon-v2.**
   1. Clique com o botão direito no projeto no Solution Explorer e, em seguida, **selecione Publicar**.
   1. Selecione **Import Profile** na barra inferior e importe o perfil de publicação que descarregou anteriormente.
1. Selecione **Configurar**.
1. No separador **'Ligação',** atualize o URL de destino para que utilize "https". Por exemplo, usar `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` . Selecione **Seguinte**.
1. No separador **Definições,** certifique-se de que **ativar a autenticação organizacional.**
1. Selecione **Guardar**. Selecione **Publicar** no ecrã principal.

O Visual Studio publicará o projeto e abrirá automaticamente um navegador para o URL do projeto. Se vir a página web padrão do projeto, a publicação foi bem sucedida.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Atualizar o registo de pedido de inquilino Azure AD para dotnet-web-daemon-v2

1. Volte para o <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure.</a>
1. No painel esquerdo, selecione o serviço **Azure Ative Directory** e, em seguida, selecione **registos de Aplicações**.
1. Selecione a aplicação **dotnet-web-daemon-v2.**
1. Na página **autenticação** para a sua aplicação, atualize os campos **URL de logotipo do canal frontal** com o endereço do seu serviço. Por exemplo, usar `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession` .
1. A partir do menu **Branding,** atualize o **URL da página inicial** para o endereço do seu serviço. Por exemplo, usar `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` .
1. Guarde a configuração.
1. Adicione o mesmo URL na lista de valores do menu  >  **URIs de redirecionamento de** autenticação. Se tiver urLs de redirecionamento múltiplo, certifique-se de que há uma nova entrada que utiliza o URI do serviço de aplicações para cada URL de redirecionamento.

## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não for necessário, elimine o objeto da aplicação que criou no Passo de Inscrição da [sua aplicação.](#register-your-application)  Para remover o pedido, siga as instruções em [Remover uma aplicação da autoria de si ou da sua organização.](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)

## <a name="get-help"></a>Obter ajuda

Use [o Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-ad-msal.html) para obter apoio da comunidade.
Faça as suas perguntas sobre o Microsoft Q&A primeiro, e navegue nos problemas existentes para ver se alguém já fez a sua pergunta antes.
Certifique-se de que as suas perguntas ou comentários estão marcados com "azure-ad-adal-depreciação", "azure-ad-msal" e "dotnet-standard".

Se encontrar um bug na amostra, por favor levante a questão sobre [questões do GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se encontrar um bug no MSAL.NET, por favor levante a questão sobre [MSAL.NET Problemas do GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Para fornecer uma recomendação, aceda à [página Voz](https://feedback.azure.com/forums/169401-azure-active-directory)do Utilizador .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a construção de aplicações daemon que usam a plataforma de identidade da Microsoft para aceder a APIs web protegidas:

> [!div class="nextstepaction"]
> [Cenário: Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)
