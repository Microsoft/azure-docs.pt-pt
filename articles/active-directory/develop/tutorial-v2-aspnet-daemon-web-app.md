---
title: Criar um daemon multilocatário que usa o ponto de extremidade da plataforma de identidade da Microsoft
description: Neste tutorial, saiba como chamar uma API Web ASP.NET protegida por Azure Active Directory de um aplicativo de área de trabalho do Windows (WPF). O cliente do WPF autentica um usuário, solicita um token de acesso e chama a API da Web.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d884987ed5fb00d4078a38aa37d463a81630ca7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423382"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Tutorial: criar um daemon multilocatário que usa o ponto de extremidade da plataforma Microsoft Identity

Neste tutorial, você aprenderá a usar a plataforma de identidade da Microsoft para acessar os dados de clientes de negócios da Microsoft em um processo de execução longa e não interativa. O daemon de exemplo usa a [concessão de credenciais de cliente OAuth2](v2-oauth2-client-creds-grant-flow.md) para adquirir um token de acesso. Em seguida, o daemon usa o token para chamar [Microsoft Graph](https://graph.microsoft.io) e acessar dados organizacionais.

> [!div class="checklist"]
> * Integrar um aplicativo daemon com a plataforma de identidade da Microsoft
> * Conceder permissões de aplicativo diretamente ao aplicativo por um administrador
> * Obter um token de acesso para chamar a API de Microsoft Graph
> * Chame a API de Microsoft Graph.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

O aplicativo é criado como um aplicativo MVC ASP.NET. Ele usa o middleware OWIN OpenID Connect para conectar usuários.  

O componente "daemon" neste exemplo é um controlador de API, `SyncController.cs`. Quando o controlador é chamado, ele efetua pull em uma lista de usuários no locatário do Azure Active Directory do cliente (Azure AD) do Microsoft Graph. `SyncController.cs` é disparado por uma chamada AJAX no aplicativo Web. Ele usa a [MSAL (biblioteca de autenticação da Microsoft) para .net](msal-overview.md) para adquirir um token de acesso para Microsoft Graph.

Para obter um aplicativo de daemon de console mais simples, consulte o [início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Cenário

Como o aplicativo é um aplicativo multilocatário para clientes de negócios da Microsoft, ele deve fornecer uma maneira para os clientes "inscreverem-se" ou "conectar" o aplicativo aos dados da empresa. Durante o fluxo de conexão, um administrador da empresa primeiro concede *permissões de aplicativo* diretamente ao aplicativo para que ele possa acessar dados da empresa de maneira não interativa, sem a presença de um usuário conectado. A maior parte da lógica neste exemplo mostra como obter esse fluxo de conexão usando o ponto de extremidade de [consentimento do administrador](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) da plataforma de identidade.

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Para obter mais informações sobre os conceitos usados neste exemplo, leia a [documentação do protocolo de credenciais do cliente para o ponto de extremidade da plataforma de identidade](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar o exemplo neste guia de início rápido, você precisará de:

- [Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/).
- Um inquilino do Azure AD. Para obter mais informações, consulte [como obter um locatário do Azure ad](quickstart-create-new-tenant.md).
- Uma ou mais contas de usuário em seu locatário do Azure AD. Este exemplo não funcionará com um conta Microsoft (anteriormente conhecido como conta do Windows Live). Se você tiver entrado no [portal do Azure](https://portal.azure.com) com um conta Microsoft e nunca tiver criado uma conta de usuário em seu diretório, precisará fazer isso agora.

## <a name="clone-or-download-this-repository"></a>Clonar ou baixar este repositório

No Shell ou na linha de comando, digite este comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [Baixe o exemplo em um arquivo zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Registar a sua aplicação

Este exemplo tem um projeto. Para registrar o aplicativo com seu locatário do Azure AD, você pode:

- Siga as etapas em [registrar o exemplo com seu locatário Azure Active Directory](#register-your-application) e [Configure o exemplo para usar seu locatário do Azure ad](#choose-the-azure-ad-tenant).
- Use scripts do PowerShell que:
  - Crie *automaticamente* os aplicativos do Azure AD e os objetos relacionados (senhas, permissões, dependências) para você.
  - Modifique os arquivos de configuração dos projetos do Visual Studio.

Se você quiser usar a automação:

1. No Windows, execute o PowerShell e vá para a raiz do diretório clonado.
1. Execute este comando:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Execute o script para criar seu aplicativo do Azure AD e configure o código do aplicativo de exemplo de acordo:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Outras maneiras de executar scripts são descritas em [scripts de criação de aplicativo](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Abra a solução do Visual Studio e selecione **Iniciar** para executar o código.

Se você não quiser usar a automação, use as etapas nas seções a seguir.

### <a name="choose-the-azure-ad-tenant"></a>Escolher o locatário do Azure AD

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.
1. Se sua conta estiver em mais de um locatário do Azure AD, selecione seu perfil no menu na parte superior da página e selecione **alternar diretório**.
1. Altere a sessão do portal para o locatário do Azure AD desejado.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrar o aplicativo cliente (dotNet-Web-daemon-v2)

1. Vá para a página de [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) na plataforma de identidade da Microsoft para desenvolvedores.
1. Selecione **novo registro**.
1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   - Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo. Por exemplo, digite **dotnet-Web-daemon-v2**.
   - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional**.
   - Na seção **URI de redirecionamento (opcional)** , selecione **Web** na caixa de combinação e insira os seguintes URIs de redirecionamento:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**
          
     Se houver mais de dois URIs de redirecionamento, você precisará adicioná-los da guia **autenticação** mais tarde, depois que o aplicativo for criado com êxito.
1. Selecione **Registar** para criar a aplicação.
1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde. Você precisará dela para configurar o arquivo de configuração do Visual Studio para este projeto.
1. Na lista de páginas da aplicação, selecione **Autenticação**. Em seguida:
   - Na seção **Configurações avançadas** , defina **URL de logoff** para **https://localhost:44316/Account/EndSession** .
   - Na seção **Configurações avançadas** > **concessão implícita** , selecione **tokens de acesso** e **tokens de ID**. Este exemplo requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja habilitado para conectar o usuário e chamar uma API.
1. Selecione **Guardar**.
1. Na página **certificados & segredos** , na seção **segredos do cliente** , selecione **novo segredo do cliente**. Em seguida:

   1. Insira uma descrição da chave (por exemplo, **segredo do aplicativo**),
   1. Selecione uma duração de chave de **em 1 ano**, **em 2 anos**ou **nunca expirar**.
   1. Selecione o botão **Adicionar** . 
   1. Quando o valor da chave for exibido, copie e salve-o em um local seguro. Você precisará dessa chave posteriormente para configurar o projeto no Visual Studio. Ele não será exibido novamente ou poderá ser recuperado por outros meios.
1. Na lista de páginas do aplicativo, selecione permissões de **API**. Em seguida:
   1. Selecione o botão **Adicionar uma permissão**.
   1. Verifique se a guia **Microsoft APIs** está selecionada.
   1. Na seção **APIs da Microsoft comumente usadas** , selecione **Microsoft Graph**.
   1. Na seção **permissões de aplicativo** , verifique se as permissões corretas estão selecionadas: **User. Read. All**.
   1. Selecione o botão **adicionar permissões** .

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurar o exemplo para usar seu locatário do Azure AD

Nas etapas a seguir, **ClientID** é o mesmo que "ID do aplicativo" ou **AppID**.

Abra a solução no Visual Studio para configurar os projetos.

### <a name="configure-the-client-project"></a>Configurar o projeto do cliente

Se você tiver usado os scripts de instalação, as seguintes alterações serão aplicadas para você.

1. Abra o arquivo **UserSync\Web.config** .
1. Localize a chave de aplicativo **ida: ClientID**. Substitua o valor existente pela ID do aplicativo **dotnet-Web-daemon-v2** copiado do portal do Azure.
1. Localize a chave do aplicativo **ida: ClientSecret**. Substitua o valor existente pela chave que você salvou durante a criação do aplicativo **dotnet-Web-daemon-v2** no portal do Azure.

## <a name="run-the-sample"></a>Executar o exemplo

Limpe a solução, recompile a solução, execute o aplicativo usersync e, em seguida, entre como administrador em seu locatário do Azure AD. Se você não tiver um locatário do Azure AD para teste, você pode [seguir estas instruções](quickstart-create-new-tenant.md) para obter um.

Quando você entra, o aplicativo solicita primeiro a permissão para conectá-lo e ler seu perfil de usuário. Esse consentimento permite que o aplicativo garanta que você é um usuário comercial.

![Consentimento do utilizador](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Em seguida, o aplicativo tenta sincronizar uma lista de usuários do seu locatário do Azure AD por meio de Microsoft Graph. Se não puder, ele solicitará que você (o administrador de locatários) Conecte seu locatário ao aplicativo.

O aplicativo solicita permissão para ler a lista de usuários em seu locatário.

![Consentimento do administrador](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Depois de conceder a permissão, você será desconectado do aplicativo. Essa saída garante que todos os tokens de acesso existentes para Microsoft Graph sejam removidos do cache de token. Quando você entra novamente, o novo token obtido terá as permissões necessárias para fazer chamadas para Microsoft Graph.


Quando você concede a permissão, o aplicativo pode consultar os usuários em qualquer ponto. Você pode verificar isso selecionando o botão **sincronizar usuários** e atualizando a lista de usuários. Tente adicionar ou remover um usuário e ressincronizar a lista. (Mas observe que o aplicativo sincroniza apenas a primeira página de usuários.)

## <a name="about-the-code"></a>Sobre o código

O código relevante para este exemplo está nos seguintes arquivos:

- **App_Start \startup.auth.cs**, **Controllers\AccountController.cs**: entrada inicial. Em particular, as ações no controlador têm um atributo **autorizar** , que força o usuário a entrar. O aplicativo usa o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md) para conectar o usuário.
- **Controllers\SyncController.cs**: Sincronizando a lista de usuários para o repositório local na memória.
- **Controllers\UserController.cs**: exibindo a lista de usuários do repositório local na memória.
- **Controllers\AccountController.cs**: adquirir permissões do administrador de locatários usando o ponto de extremidade de consentimento do administrador.

## <a name="re-create-the-sample-app"></a>Recriar o aplicativo de exemplo

1. No Visual Studio, crie um novo **projeto C#**  de **aplicativo Web do Visual ASP.net (.NET Framework)** . 
1. Na próxima tela, escolha o modelo de projeto do **MVC** . Além disso, adicione referências de pasta e núcleo para a **API Web**, pois você adicionará um controlador de API da Web mais tarde. Deixe o modo de autenticação escolhido do projeto como o padrão: **sem autenticação**.
1. Selecione o projeto na janela **Gerenciador de soluções** e selecione a tecla **F4** . 
1. Nas propriedades do projeto, defina **SSL habilitado** como **verdadeiro**. Observe as informações na **URL SSL**. Você precisará dela ao configurar o registro desse aplicativo no portal do Azure.
1. Adicione os seguintes pacotes NuGet ASP.NET OWIN middleware: 
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client 
1. Na pasta **App_Start** :
   1. Crie uma classe chamada **Startup.auth.cs**. 
   1. Remover **. App_Start** do nome do namespace. 
   1. Substitua o código da classe de **inicialização** pelo código do mesmo arquivo do aplicativo de exemplo.       
   Certifique-se de obter a definição de classe inteira. A definição muda de **inicialização de classe pública** para **inicialização de classe parcial pública.**
1. No **Startup.auth.cs**, resolva as referências ausentes adicionando instruções **using** conforme sugerido pelo Visual Studio IntelliSense.
1. Clique com o botão direito do mouse no projeto, selecione **Adicionar**e, em seguida, selecione **classe**.
1. Na caixa de pesquisa, digite **OWIN**. A **classe de inicialização OWIN** aparece como uma seleção. Selecione-o e nomeie a classe **Startup.cs**.
1. No **Startup.cs**, substitua o código da classe de **inicialização** pelo código do mesmo arquivo do aplicativo de exemplo. Novamente, observe que a definição muda de **inicialização de classe pública** para **inicialização de classe parcial pública**.
1. Na pasta **modelos** , adicione uma nova classe chamada **MsGraphUser.cs**. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
1. Adicione um novo **controlador MVC 5 – instância vazia** chamada **AccountController**. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
1. Adicione um novo **controlador MVC 5 – instância vazia** chamada **UserController**. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
1. Adicione um novo **controlador da API Web 2 – instância vazia** chamada **SyncController**. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
1. Para a interface do usuário, na **pasta Views\Account** , adicione três instâncias **de exibições vazias (sem modelo)** chamadas **GrantPermissions**, **index**e **incompatível**. Adicione e um **índice** nomeado na pasta **Views\User** Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
1. Atualize o **layout de\_compartilhado. cshtml** e **Home\Index.cshtml** para vincular corretamente as várias exibições juntas.

## <a name="deploy-the-sample-to-azure"></a>Implantar o exemplo no Azure

Este projeto tem projetos de aplicativo Web e de API Web. Para implantá-los nos sites do Azure, execute as seguintes etapas para cada um:

1. Crie um site do Azure.
1. Publique o aplicativo Web e as APIs Web no site.
1. Atualize os clientes para chamar o site em vez de IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Criar e publicar dotnet-Web-daemon-V2 em um site do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo.
1. Selecione **web** > **aplicativo Web**e dê um nome ao seu site. Por exemplo, nomeie-o como **dotnet-Web-daemon-v2-contoso.azurewebsites.net**.
1. Selecione as informações de **assinatura**, **grupo de recursos**e **plano e local do serviço de aplicativo**. O **sistema operacional** é o **Windows**e **publicar** é o **código**.
1. Selecione **criar** e aguarde até que o serviço de aplicativo seja criado.
1. Quando você receber a notificação **implantação bem-sucedida** , selecione **ir para o recurso** para ir para o serviço de aplicativo criado recentemente.
1. Depois que o site for criado, localize-o no **painel** e selecione-o para abrir a tela de **visão geral** do serviço de aplicativo.
1. Na guia **visão geral** do serviço de aplicativo, baixe o perfil de publicação selecionando o link **obter perfil de publicação** e salve-o. Você pode usar outros mecanismos de implantação, como a implantação do controle do código-fonte.
1. Alterne para o Visual Studio e, em seguida:
   1. Vá para o projeto **dotnet-Web-daemon-v2** . 
   1. Clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **publicar**.
   1. Selecione **importar perfil** na barra inferior e importe o perfil de publicação que você baixou anteriormente.
1. Selecione **Configurar**.
1. Na guia **conexão** , atualize a URL de destino para que ela use "https". Por exemplo, use [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Selecione **Seguinte**.
1. Na guia **configurações** , verifique se **habilitar autenticação organizacional** está desmarcada.  
1. Selecione **Guardar**. Selecione **publicar** na tela principal.

O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto. Se você vir a página da Web padrão do projeto, a publicação foi bem-sucedida.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Atualizar o registro do aplicativo de locatário do Azure AD para dotnet-Web-daemon-v2

1. Regresse ao [portal do Azure](https://portal.azure.com).
1. No painel esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **registros de aplicativo**.
1. Selecione o aplicativo **dotnet-Web-daemon-v2** .
1. Na página de **autenticação** do seu aplicativo, atualize os campos de **URL de logout** com o endereço do seu serviço. Por exemplo, use [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. No menu **identidade visual** , atualize a **URL da Home Page** para o endereço do seu serviço. Por exemplo, use [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Guarde a configuração.
1. Adicione a mesma URL na lista de valores do menu **autenticação** > **redirecionamento de URIs** . Se você tiver várias URLs de redirecionamento, verifique se há uma nova entrada que usa o URI do serviço de aplicativo para cada URL de redirecionamento.

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, exclua o objeto de aplicativo que você criou na etapa [registrar seu aplicativo](#register-your-application) .  Para remover o aplicativo, siga as instruções em [remover um aplicativo criado por você ou sua organização](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Obter ajuda

Use [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) para obter suporte da Comunidade.
Faça suas perguntas em Stack Overflow primeiro e procure problemas existentes para ver se alguém fez sua pergunta antes.
Certifique-se de que suas perguntas ou comentários estão marcados com "Adal", "MSAL" e "dotnet".

Se você encontrar um bug no exemplo, gere o problema em problemas do [GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se você encontrar um bug no MSAL.NET, gere o problema em [problemas do GitHub MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Para fornecer uma recomendação, vá para a [página de voz do usuário](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os diferentes [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md) aos quais a plataforma de identidades da Microsoft dá suporte.

Para obter mais informações, consulte a seguinte documentação conceitual:

- [Aluguel em Azure Active Directory](single-and-multi-tenant-apps.md)
- [Compreender as experiências de consentimento da aplicação do Azure AD](application-consent-experience.md)
- [Entrar em qualquer Azure Active Directory usuário usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md)
- [Entender o consentimento do usuário e do administrador](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objetos do principal de serviço e aplicação no Azure Active Directory](app-objects-and-service-principals.md)
- [Início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md)
- [Início rápido: configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
- [Adquirindo um token para um aplicativo com fluxos de credencial do cliente](msal-client-applications.md)

Para obter um aplicativo de daemon de console multilocatário mais simples, consulte o [início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).
