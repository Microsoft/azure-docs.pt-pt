---
title: Chamar um ASP.NET Web API protegido pelo Azure AD – plataforma de identidade da Microsoft
description: Neste guia de início rápido, saiba como chamar uma API Web ASP.NET protegida por Azure Active Directory de um aplicativo de área de trabalho do Windows (WPF). O cliente do WPF autentica um usuário, solicita um token de acesso e chama a API da Web.
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
ms.date: 11/20/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: c558d45702498e6c1164d7ee1731e80ff195349e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328417"
---
# <a name="build-a-multi-tenant-daemon-with-the-microsoft-identity-platform-endpoint"></a>Criar um daemon multilocatário com o ponto de extremidade da plataforma Microsoft Identity

Neste tutorial, você aprenderá a usar a plataforma de identidade da Microsoft para acessar os dados de clientes de negócios da Microsoft em um processo de execução longa e não interativa. O daemon de exemplo usa a [concessão de credenciais de cliente OAuth2](v2-oauth2-client-creds-grant-flow.md) para adquirir um token de acesso, que ele usa para chamar o [Microsoft Graph](https://graph.microsoft.io) e acessar dados organizacionais.

O aplicativo é criado como um aplicativo MVC ASP.NET e usa o middleware OWIN OpenID Connect para conectar usuários.  Seu componente "daemon" neste exemplo é um controlador de API, que, quando chamado, efetua pull em uma lista de usuários no locatário do Azure AD do cliente de Microsoft Graph.  Esse `SyncController.cs` é disparado por uma chamada AJAX no aplicativo Web e usa a [biblioteca de autenticação da Microsoft (MSAL) para .net](msal-overview.md) para adquirir um token de acesso para Microsoft Graph.

Para um aplicativo de daemon de console mais simples, confira o [início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).

## <a name="scenario"></a>Cenário

Como o aplicativo é um aplicativo multilocatário destinado para uso por qualquer cliente comercial da Microsoft, ele deve fornecer uma maneira para que os clientes "se inscrevam" ou "conectem" o aplicativo aos dados da empresa.  Durante o fluxo de conexão, um administrador da empresa primeiro concede **permissões de aplicativo** diretamente ao aplicativo para que ele possa acessar dados da empresa de maneira não interativa, sem a presença de um usuário conectado.  A maior parte da lógica neste exemplo mostra como alcançar esse fluxo de conexão usando o ponto de extremidade de [consentimento do administrador](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) da plataforma de identidade.

![Topologia](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Para obter mais informações sobre os conceitos usados neste exemplo, certifique-se de ler a [documentação de protocolo de credenciais de cliente de ponto de extremidade da plataforma de identidade](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar o exemplo neste guia de início rápido, você precisará de:

- [Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/)
- Um locatário do Azure Active Directory (Azure AD). Para obter mais informações sobre como obter um locatário do Azure AD, consulte [como obter um locatário do Azure ad](quickstart-create-new-tenant.md)
- Uma ou mais contas de usuário em seu locatário do Azure AD. Este exemplo não funcionará com um conta Microsoft (anteriormente, conta do Windows Live). Portanto, se você tiver entrado no [portal do Azure](https://portal.azure.com) com um conta Microsoft e nunca tiver criado uma conta de usuário em seu diretório antes, precisará fazer isso agora.

## <a name="clone-or-download-this-repository"></a>Clonar ou baixar este repositório

No Shell ou na linha de comando:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ou [Baixe o exemplo em um arquivo zip](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-the-sample-application-with-your-azure-ad-tenant"></a>Registrar o aplicativo de exemplo com seu locatário do Azure AD

Há um projeto neste exemplo. Para registrá-lo, você pode:

- Siga as etapas [registrar o exemplo com seu locatário Azure Active Directory](#register-the-sample-application-with-your-azure-ad-tenant) e [Configurar o exemplo para usar seu locatário do Azure ad](#choose-the-azure-ad-tenant-for-the-applications)
- Ou use scripts do PowerShell que:
  - Cria **automaticamente** os aplicativos do Azure AD e os objetos relacionados (senhas, permissões, dependências) para você
  - Modifique os arquivos de configuração dos projetos do Visual Studio.

Se você quiser usar essa automação:

1. No Windows, execute o PowerShell e navegue até a raiz do diretório clonado
1. Na execução do PowerShell:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Execute o script para criar seu aplicativo do Azure AD e configure o código do aplicativo de exemplo de acordo.
1. Na execução do PowerShell:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   > Outras maneiras de executar os scripts são descritas em [scripts de criação de aplicativo](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)

1. Abra a solução do Visual Studio e clique em Iniciar para executar o código.

Se você não quiser usar essa automação, siga as etapas abaixo.

### <a name="choose-the-azure-ad-tenant-for-the-applications"></a>Escolha o locatário do Azure AD para os aplicativos

Como primeira etapa, você precisará:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD, selecione seu perfil no canto superior direito no menu na parte superior da página e, em seguida, **Alterne o diretório**.
   Altere a sessão do portal para o locatário do Azure AD desejado.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrar o aplicativo cliente (dotNet-Web-daemon-v2)

1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecione **novo registro**.
1. Quando a **página Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
   - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `dotnet-web-daemon-v2`.
   - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional**.
   - Na seção URI de redirecionamento (opcional), selecione **Web** na caixa de combinação e insira os seguintes URIs de redirecionamento:
       - `https://localhost:44316/`
       - `https://localhost:44316/Account/GrantPermissions` se houver mais de um URI de redirecionamento, você precisará adicioná-los da guia **autenticação** posteriormente depois que o aplicativo tiver sido criado com êxito.
1. Selecione **Registar** para criar a aplicação.
1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde. Você precisará dela para configurar o arquivo de configuração do Visual Studio para este projeto.
1. Na lista de páginas da aplicação, selecione **Autenticação**.
   - Na seção **Configurações avançadas** , defina a **URL de logout** para `https://localhost:44316/Account/EndSession`
   - Na seção **Configurações avançadas** | **concessão implícita** , verifique **tokens de acesso** e **tokens de ID** , pois este exemplo requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja habilitado para conectar o usuário e chamar uma API.
1. Selecione **Guardar**.
1. Na página **certificados & segredos** , na seção **segredos do cliente** , escolha **novo segredo do cliente**:

   - Digite uma descrição de chave (de instância `app secret`),
   - Selecione uma duração de chave de **em 1 ano**, **em 2 anos**ou **nunca expirar**.
   - Quando você pressionar o botão **Adicionar** , o valor da chave será exibido, copiará e salvará o valor em um local seguro.
   - Você precisará dessa chave posteriormente para configurar o projeto no Visual Studio. Esse valor de chave não será exibido novamente, nem poderá ser recuperado por outros meios, portanto, registre-o assim que estiver visível na portal do Azure.
1. Na lista de páginas do aplicativo, selecione permissões de **API**
   - Clique no botão **Adicionar uma permissão** e, em seguida,
   - Verifique se a guia **APIs da Microsoft** está selecionada
   - Na seção *APIs da Microsoft comumente usadas* , clique em **Microsoft Graph**
   - Na seção **permissões de aplicativo** , verifique se as permissões corretas estão marcadas: **User. Read. All**
   - Selecione o botão **adicionar permissões**

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Configurar o exemplo para usar seu locatário do Azure AD

Nas etapas a seguir, "ClientID" é o mesmo que "ID do aplicativo" ou "AppId".

Abrir a solução no Visual Studio para configurar os projetos

### <a name="configure-the-client-project"></a>Configurar o projeto do cliente

Se você tiver usado os scripts de instalação, as seguintes alterações serão aplicadas para você.

1. Abrir o arquivo de `UserSync\Web.Config`
1. Localize a chave do aplicativo `ida:ClientId` e substitua o valor existente pela ID do aplicativo (clientId) do aplicativo `dotnet-web-daemon-v2` copiado do portal do Azure.
1. Localize a chave do aplicativo `ida:ClientSecret` e substitua o valor existente pela chave que você salvou durante a criação do aplicativo `dotnet-web-daemon-v2`, na portal do Azure.

## <a name="run-the-sample"></a>Executar o exemplo

Limpe a solução, recompile a solução e execute o aplicativo usersync e comece entrando como um administrador em seu locatário do Azure AD.  Se você não tiver um locatário do Azure AD para teste, você pode [seguir estas instruções](quickstart-create-new-tenant.md) para obter um.

Quando você entrar, o aplicativo primeiro solicitará permissão para conectá-lo & ler seu perfil de usuário.  Esse consentimento permite que o aplicativo garanta que você é um usuário comercial.

![Consentimento do usuário](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Em seguida, o aplicativo tentará sincronizar uma lista de usuários do seu locatário do Azure AD por meio do Microsoft Graph.  Se não for possível fazer isso, ele solicitará que você (o administrador de locatários) Conecte seu locatário ao aplicativo.

O aplicativo pedirá permissão para ler a lista de usuários em seu locatário.

![Consentimento do administrador](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

**Você será desconectado do aplicativo após a concessão da permissão**. Isso é feito para garantir que os tokens de acesso existentes para o Graph sejam removidos do cache de token. Depois de entrar novamente, o novo token obtido terá as permissões necessárias para fazer chamadas para o MS Graph.
Quando você conceder a permissão, o aplicativo será capaz de consultar os usuários em qualquer ponto.  Você pode verificar isso clicando no botão **sincronizar usuários** na página usuários, atualizando a lista de usuários.  Tente adicionar ou remover um usuário e ressincronizar a lista (mas observe que ela apenas sincroniza a primeira página de usuários!).

## <a name="about-the-code"></a>Sobre o código

O código relevante para este exemplo está nos seguintes arquivos:

- Entrada inicial: `App_Start\Startup.Auth.cs`, `Controllers\AccountController.cs`.  Em particular, as ações no controlador têm um atributo autorizar, que força o usuário a entrar. O aplicativo usa o [fluxo de código de autorização](v2-oauth2-auth-code-flow.md) para conectar o usuário.
- Sincronizando a lista de usuários para o repositório local na memória: `Controllers\SyncController.cs`
- Exibindo a lista de usuários do repositório local na memória: `Controllers\UserController.cs`
- Aquisição de permissões do administrador de locatários usando o ponto de extremidade de consentimento do administrador: `Controllers\AccountController.cs`

## <a name="recreate-this-sample-app"></a>Recriar este aplicativo de exemplo

1. No Visual Studio, crie um novo projeto de `ASP.NET Web Application (.NET Framework)` de `Visual C#`. Na próxima tela, escolha o modelo de projeto `MVC`. Além disso, adicione referências de pasta e núcleo para `Web API` como você adicionaria um controlador de API da Web mais tarde.  Deixe o modo de autenticação escolhido do projeto como o padrão, ou seja, `No Authentication`".
2. Selecione o projeto na janela **Gerenciador de soluções** e pressione a tecla **F4** para trazer as propriedades do projeto. Nas propriedades do projeto, defina **SSL habilitado** para ser `True`. Observe a **URL do SSL**. Você precisará dela ao configurar o registro desse aplicativo no portal do Azure.
3. Adicione o seguinte NuGets de middleware ASP.Net OWIN: `Microsoft.Owin.Security.ActiveDirectory`, `Microsoft.Owin.Security.Cookies` e `Microsoft.Owin.Host.SystemWeb`, `Microsoft.IdentityModel.Protocol.Extensions`, `Microsoft.Owin.Security.OpenIdConnect` e `Microsoft.Identity.Client`. 
4. Na pasta `App_Start`, crie uma classe `Startup.Auth.cs`. Remova `.App_Start` do nome do namespace.  Substitua o código da classe `Startup` pelo código do mesmo arquivo do aplicativo de exemplo.  Certifique-se de obter toda a definição de classe!  A definição muda de `public class Startup` para `public partial class Startup`
5. Em `Startup.Auth.cs` resolver referências ausentes adicionando instruções `using` conforme sugerido pelo Visual Studio IntelliSense.
6. Clique com o botão direito do mouse no projeto, selecione Adicionar, selecione "classe" e, na caixa de pesquisa, digite "OWIN".  "Classe de inicialização OWIN" será exibida como uma seleção; Selecione-o e nomeie a classe `Startup.cs`.
7. Em `Startup.cs`, substitua o código da classe `Startup` pelo código do mesmo arquivo do aplicativo de exemplo.  Novamente, observe que as alterações de definição de `public class Startup` para `public partial class Startup`.
8. Na pasta, adicione uma nova classe chamada `MsGraphUser.cs`.  Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
9. Adicione um novo **controlador MVC 5-vazio** chamado `AccountController`. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
10. Adicione um novo **controlador MVC 5-vazio** chamado `UserController`. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
11. Adicione um novo **controlador da API Web 2-vazio** chamado `SyncController`. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
12. Para a interface do usuário, na pasta `Views\Account`, adicione três **exibições vazias (sem modelo)** chamadas `GrantPermissions`, `Index` e `UserMismatch` e uma denominada `Index` na pasta `Views\User`. Substitua a implementação pelo conteúdo do arquivo do mesmo nome do exemplo.
13. Atualize o `Shared\_Layout.cshtml` e o `Home\Index.cshtml` para vincular corretamente as várias exibições juntas.

## <a name="deploy-this-sample-to-azure"></a>Implantar este exemplo no Azure

Este projeto tem projetos de API Web/WebApp. Para implantá-los nos sites do Azure, você precisará, para cada um, para:

- criar um site do Azure
- publicar o aplicativo Web/APIs Web no site da Web e
- atualize seus clientes para chamar o site em vez de IIS Express.

### <a name="create-and-publish-the-dotnet-web-daemon-v2-to-an-azure-web-site"></a>Criar e publicar o `dotnet-web-daemon-v2` em um site do Azure

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Clique em `Create a resource` no canto superior esquerdo, selecione **web** --> **aplicativo Web**e dê um nome ao seu site, por exemplo, `dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Depois disso, selecione o `Subscription`, `Resource Group``App service plan and Location`. `OS` será o **Windows** e `Publish` será o **código**.
1. Clique em `Create` e aguarde até que o serviço de aplicativo seja criado.
1. Depois de obter a notificação de `Deployment succeeded`, clique em `Go to resource` para navegar até o serviço de aplicativo recém-criado.
1. Depois que o site for criado, localize-o no **painel** e clique nele para abrir a tela de **visão geral** **dos serviços de aplicativos** .
1. Na guia **visão geral** do serviço de aplicativo, baixe o perfil de publicação clicando no link **obter perfil de publicação** e salve-o.  Outros mecanismos de implantação, como do controle do código-fonte, também podem ser usados.
1. Alterne para o Visual Studio e vá para o projeto dotnet-Web-daemon-v2.  Clique com o botão direito do mouse no projeto na Gerenciador de Soluções e selecione **publicar**.  Clique em **importar perfil** na barra inferior e importe o perfil de publicação que você baixou anteriormente.
1. Clique em **Configurar** e, na `Connection tab`, atualize a URL de destino para que ela seja uma `https` na URL do Home Page, por exemplo [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Clique em **Seguinte**.
1. Na guia Configurações, verifique se `Enable Organizational Authentication` não está selecionado.  Clique em **Guardar**. Clique em **publicar** na tela principal.
1. O Visual Studio publicará o projeto e abrirá automaticamente um navegador para a URL do projeto.  Se você vir a página da Web padrão do projeto, a publicação foi bem-sucedida.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Atualizar o registro do aplicativo de locatário do Azure AD para `dotnet-web-daemon-v2`

1. Navegue de volta para a [portal do Azure](https://portal.azure.com).
No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e, em seguida, selecione **registros de aplicativo**.
1. Na tela resultante, selecione o aplicativo `dotnet-web-daemon-v2`.
1. Na **autenticação** | para seu aplicativo, atualize os campos de URL de logout com o endereço do seu serviço, por exemplo [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net)
1. No menu *identidade visual* , atualize a **URL da Home Page**, para o endereço do seu serviço, por exemplo [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net). Guarde a configuração.
1. Adicione a mesma URL na lista de valores do menu *autenticação-> redirecionamento de URIs* . Se você tiver várias URLs de redirecionamento, verifique se há uma nova entrada usando o URI do serviço de aplicativo para cada URL de redirecionamento.

## <a name="community-help-and-support"></a>Ajuda e suporte da Comunidade

Use [Stack Overflow](http://stackoverflow.com/questions/tagged/msal) para obter suporte da Comunidade.
Faça suas perguntas em Stack Overflow primeiro e procure problemas existentes para ver se alguém fez sua pergunta antes.
Verifique se suas perguntas ou comentários estão marcados com [`adal` `msal` `dotnet`].

Se você encontrar e detectar um bug no exemplo, gere o problema em [problemas do GitHub](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Se você encontrar um bug no MSAL.NET, gere o problema em [problemas do GitHub MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Para fornecer uma recomendação, visite a [página de voz do usuário](https://feedback.azure.com/forums/169401-azure-active-directory)a seguir.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre os diferentes [fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md) aos quais a plataforma de identidades da Microsoft dá suporte.

Para obter mais informações, consulte a seguinte documentação conceitual:

- [Aluguel em Azure Active Directory](single-and-multi-tenant-apps.md)
- [Compreender as experiências de consentimento da aplicação do Azure AD](application-consent-experience.md)
- [Como entrar em qualquer Azure Active Directory usuário usando o padrão de aplicativo multilocatário](howto-convert-app-to-be-multi-tenant.md)
- [Entender o consentimento do usuário e do administrador](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Objetos de aplicativo e entidade de serviço no Azure Active Directory](app-objects-and-service-principals.md)
- [Início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](quickstart-register-app.md)
- [Início rápido: configurar um aplicativo cliente para acessar APIs Web](quickstart-configure-app-access-web-apis.md)
- [Adquirindo um token para um aplicativo com fluxos de credencial do cliente](msal-client-applications.md)

Para um aplicativo de daemon de console de vários locatários mais simples, confira o [início rápido do daemon do .NET Core](quickstart-v2-netcore-daemon.md).
