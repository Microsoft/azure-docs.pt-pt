---
title: Daemon de .NET Core de v2 do Azure AD | Documentos da Microsoft
description: Saiba como um processo de .NET Core pode obter um token de acesso e chamar uma API protegida por um ponto final v2.0 do Azure Active Directory usando a identidade da aplicação
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
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c63269630d0ed74d1b17edbc5cb9e787499604e
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200529"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-apps-identity"></a>Início rápido: Adquirir um token e chamar o Microsoft Graph API a partir de uma aplicação de consola com a identidade da aplicação

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, irá aprender a escrever uma aplicação .NET Core que pode obter um token de acesso com a identidade da aplicação e, em seguida, chamar a API do Microsoft Graph para apresentar uma [lista de utilizadores](https://docs.microsoft.com/graph/api/user-list) no diretório. Este cenário é útil para situações em que a tarefa sem periféricos e autônoma ou um serviço do windows precisa ser executado com uma identidade de aplicação, em vez da identidade de um utilizador.

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-netcore-daemon/netcore-daemon-intro-updated.png)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer [.NET Core 2.1](https://www.microsoft.com/net/download/dotnet-core/2.1).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao [Portal do Azure - Registo de Aplicação (Pré-visualização)](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs).
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **Registos de aplicações (Pré-visualização)** > **Novo registo**.
> 1. Na **Name** , digite um nome de aplicativo significativo que será apresentado aos utilizadores da aplicação, por exemplo `Daemon-console`, em seguida, selecione **registar** para criar o aplicativo.
> 1. Depois de registado, selecione o **certificados e segredos** menu.
> 1. Sob **segredos de cliente**, selecione **+ novo segredo do cliente**. Dê a ele um nome e selecionar **adicionar**. Copie o segredo num local seguro. Irá precisar dele para utilizar no seu código.
> 1. Agora, selecione o **permissões de API** menu, selecione **+ adicionar uma permissão** botão, selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicação**.
> 1. Sob **usuário** nó, selecione **User.Read.All**, em seguida, selecione **adicionar permissões**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Transferir e configurar a sua aplicação de início rápido
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a sua aplicação no portal do Azure
> Para o código de exemplo para este início rápido funcionar, terá de criar um segredo do cliente e adicionar a Graph API **User.Read.All** permissão da aplicação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Efetuar estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-windows-desktop/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Transfira o seu projeto do Visual Studio

[Transfira o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o seu projeto do Visual Studio

1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
1. Abra a solução no Visual Studio – **daemon console.sln** (opcional).
1. Editar **appSettings** e substitua os valores dos campos `ClientId`, `Tenant` e `ClientSecret` com o seguinte:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Gerar um novo segredo do cliente]()
    
    > [!div renderon="docs"]
    >> Em que:
    >> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
    >> * `Enter_the_Tenant_Id_Here` -substituir este valor com o **Id do inquilino** ou **nome do inquilino** (por exemplo, contoso.microsoft.com)
    >> * `Enter_the_Client_Secret_Here` -substituir este valor com o segredo de cliente criado no passo 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Para localizar os valores da **ID da aplicação (cliente)**, **ID de diretório (inquilino)**, aceda à aplicação **descrição geral** página no portal do Azure. Para gerar uma nova chave, aceda a **certificados e segredos** página.
    
#### <a name="step-4-admin-consent"></a>Passo 4: Consentimento de admin

Se tentar executar a aplicação neste momento, receberá *HTTP 403 - Proibido* erro: `Insufficient privileges to complete the operation`. Isto acontece porque qualquer *permissão só de aplicação* necessita de consentimento de administrador, o que significa que um administrador global do seu diretório tem de dar consentimento à sua aplicação. Selecione uma das opções abaixo dependendo de sua função:

##### <a name="global-tenant-administrator"></a>Administrador de inquilinos global

> [!div renderon="docs"]
> Se for um administrador de inquilinos global, aceda a **permissões de API** página no registo de aplicação do Portal do Azure (pré-visualização) e selecione **conceder autorização de administrador para {nome do inquilino}** (onde é {nome do inquilino} o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se for um administrador global, aceda a **permissões de API** página select **conceder autorização de administrador para Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Vá para a página de permissões de API]()

##### <a name="standard-user"></a>Usuário padrão

Se for um usuário padrão do seu inquilino, terá de pedir a um administrador global para conceder o consentimento de administrador para a sua aplicação. Para fazer isso, dê o seguinte URL para o seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here` -substituir este valor com o **Id do inquilino** ou **nome do inquilino** (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

> [!NOTE]
> Poderá ver o erro *' AADSTS50011: Nenhum endereço de resposta está registado para a aplicação '* depois de conceder permissão à aplicação com o URL anterior. Isso acontecer porque esta aplicação e o URL não têm um redirecionamento URI - ignore o erro.

#### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Se estiver a utilizar o Visual Studio, prima **F5** para executar a aplicação, caso contrário, execute o aplicativo por meio de linha de comandos ou a consola:

```console
cd {ProjectFolder}\daemon-console
dotnet run
```

> Em que:
> * *{ProjectFolder}*  é a pasta onde extraiu o ficheiro zip. Example **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Deverá ver uma lista de utilizadores no diretório do Azure AD, como resultado.

> [!IMPORTANT]
> Esta aplicação de início rápido utiliza um segredo do cliente para identificar a próprio como confidencial de cliente. Uma vez que o segredo do cliente é adicionado como um texto sem formatação para os ficheiros de projeto, por motivos de segurança, recomenda-se que utilize um certificado em vez de um segredo do cliente antes de considerar a aplicação como o aplicativo de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório do GitHub para este exemplo.

## <a name="more-information"></a>Mais informações

### <a name="msalnet"></a>MSAL.NET

A MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca utilizada para iniciar sessão dos utilizadores e solicitar tokens utilizados para aceder a uma API protegida pelo Microsoft Azure Active Directory (Azure AD). Conforme descrito, este início rápido pedir tokens utilizando a identidade da própria aplicação em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como  *[credenciais de cliente que o fluxo de oauth](v2-oauth2-client-creds-grant-flow.md)*. Para obter mais informações sobre como utilizar MSAL.NET com fluxo de credenciais de cliente, consulte [este artigo](https://aka.ms/msal-net-client-credentials).

 Pode instalar MSAL.NET executando o seguinte comando no Visual Studio **Package Manager Console**:

```powershell
Install-Package Microsoft.Identity.Client
```

Em alternativa, se não estiver a utilizar o Visual Studio, pode executar o seguinte comando para adicionar a MSAL ao seu projeto:

```console
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize a MSAL com o código seguinte:

```csharp
ClientCredential clientCredentials = new ClientCredential(secret: config.ClientSecret);

var app = new ConfidentialClientApplication(
    clientId: config.ClientId, 
    authority: config.Authority, 
    redirectUri: "https://daemon", 
    clientCredential: clientCredentials, 
    userTokenCache: null, 
    appTokenCache: new TokenCache()
);
```

> | Em que: ||
> |---------|---------|
> | `secret` | O segredo do cliente é criado para a aplicação no Portal do Azure. |
> | `clientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `Authority`    | (Opcional) O ponto de extremidade STS para o utilizador autenticar. Normalmente, <https://login.microsoftonline.com/{tenant}> para a nuvem pública, em que {inquilino} é o nome do seu inquilino ou o seu inquilino do ID.|
> | `redirectUri`  | URL em que os utilizadores são enviados após a autenticação. Neste caso, porque se trata de uma aplicação de consola/não-interativa, este parâmetro não é utilizado |
> | `clientCredentials`  | O objeto de credenciais de cliente, que contém o segredo ou o certificado |
> | `userTokenCache`  | Instância de uma cache de token para o utilizador. Neste caso, uma vez que esta aplicação é executada no contexto da aplicação e não pelo usuário, este valor é nulo|
> | `appTokenCache`  | Instância de uma cache de token para a aplicação|

Para obter mais informações, consulte o [documentação de referência `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.-ctor?view=azure-dotnet)

### <a name="requesting-tokens"></a>Pedir tokens

Para pedir um token com a identidade da aplicação, utilize `AcquireTokenForClientAsync` método:

```csharp
result = await app.AcquireTokenForClientAsync(scopes);
```

> |Em que:| |
> |---------|---------|
> | `scopes` | Contém os âmbitos de pedido. Para clientes confidenciais, isso deve usar o formato semelhante a `{Application ID URI}/.default` para indicar que os âmbitos que está a ser solicitada é aquelas definidas estaticamente no objeto aplicação definida no Portal do Azure (para o Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para as APIs da Web personalizado, `{Application ID URI}` é definido em **expor uma API** secção registo de aplicação do Portal do Azure (pré-visualização). |

Para obter mais informações, consulte o [documentação de referência `AcquireTokenForClientAsync`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclientasync?view=azure-dotnet#Microsoft_Identity_Client_ConfidentialClientApplication_AcquireTokenForClientAsync_System_Collections_Generic_IEnumerable_System_String__)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre permissões e consentimento:

> [!div class="nextstepaction"]
> [Permissões e consentimento](v2-permissions-and-consent.md)

Para saber mais sobre o fluxo de autenticação para este cenário, consulte o fluxo de credenciais de cliente Oauth 2.0:

> [!div class="nextstepaction"]
> [Fluxo do Oauth de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)

> [!div class="nextstepaction"]
> [Fluxos de credencial de cliente com MSAL.NET](https://aka.ms/msal-net-client-credentials)
