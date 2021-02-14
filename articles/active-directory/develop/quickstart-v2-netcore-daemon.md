---
title: 'Quickstart: Obtenha & chamada & Microsoft Numa aplicação de consola | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como uma aplicação de amostra .NET Core pode usar o fluxo de credenciais do cliente para obter um token e ligar para o Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 99dcd81cd24f762a5c2b55f5f2977aaf61bc26e8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103541"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Quickstart: Adquira um token e ligue para a Microsoft Graph API usando a identidade da app da consola

Neste arranque rápido, descarrega e executa uma amostra de código que demonstra como uma aplicação de consola .NET Core pode obter um token de acesso para ligar para a API do Microsoft Graph e apresentar uma [lista de utilizadores](/graph/api/user-list) no diretório. A amostra de código também demonstra como um trabalho ou um serviço de janelas pode funcionar com uma identidade de aplicação, em vez da identidade de um utilizador. 

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

Este arranque rápido requer [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a sua aplicação de arranque rápido: Express (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">portal Azure - Aplicação registra</a> experiência de arranque rápido.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal </span> Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `Daemon-console` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Selecione **Registar** para criar a aplicação.
> 1. Em **Gerir**, selecione **Certificados e segredos**.
> 1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente,** insira um nome e, em seguida, selecione **Add**. Grave o valor secreto num local seguro para ser utilizado num passo posterior.
> 1. Em **Gestão**, selecione **Permissões API**  >  **Adicione uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicação**.
> 1. No nó **do utilizador,** selecione **User.Read.All** e, em seguida, selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Faça o download e configuure a sua app quickstart
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
> Para que a amostra de código neste arranque rápido funcione, crie um segredo de cliente e adicione a permissão de aplicação da Graph **API.Read.All.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Transfira o seu projeto do Visual Studio

> [!div renderon="docs"]
> [Descarregue o projeto Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Você pode executar o projeto fornecido em Estúdio Visual ou Visual Studio para Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o projeto do Visual Studio
>
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Abra a solução em Visual Studio - **1-Call-MSGraph\daemon-consola.sln** (opcional).
> 1. Editar **appsettings.js** e substituir os valores dos `ClientId` campos, `Tenant` e com os `ClientSecret` seguintes:
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Em que:
>   - `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
>   - `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino Ou** Inquilino (por exemplo, contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here` - substituir este valor pelo segredo do cliente criado no passo 1.

> [!div renderon="docs"]
> > [!TIP]
> > Para encontrar os valores de ID de **aplicação (cliente),** **ID de diretório (inquilino),** aceda à página **geral** da aplicação no portal Azure. Para gerar uma nova chave, aceda à página **de segredos & Certificados.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento administrativo

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento administrativo

Se tentar executar a aplicação neste momento, receberá *HTTP 403 - Erro proibido:* `Insufficient privileges to complete the operation` . Isto acontece porque qualquer *permissão apenas de aplicação* requer o consentimento do Administrador, o que significa que um administrador global do seu diretório deve dar consentimento à sua aplicação. Selecione uma das opções abaixo, dependendo da sua função:

##### <a name="global-tenant-administrator"></a>Administrador de inquilino global

> [!div renderon="docs"]
> Se for administrador de inquilinos **globais,** no Portal Azure navegue para aplicações Enterprise > Selecione o registo da sua aplicação > Escolha **"Permissões"** a partir da secção de Segurança do painel de navegação à esquerda. Selecione o grande botão marcado **o consentimento de administração grant para {Nome do Inquilino}** (Onde {Nome do Inquilino} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se for um administrador global, vá à página **de permissões da API** selecionar **o consentimento de administração do Grant para Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Aceda à página de Permissões da API]()

##### <a name="standard-user"></a>Utilizador padrão

Se você é um utilizador padrão do seu inquilino, peça a um administrador global para conceder consentimento administrativo para a sua aplicação. Para isso, dê o seguinte URL ao seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino Ou** Inquilino (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

> [!NOTE]
> Pode ver o erro *'AADSTS50011: Nenhum endereço de resposta está registado para o pedido'* depois de conceder o consentimento à app utilizando o URL anterior. Isto acontece porque esta aplicação e o URL não têm um URI redirecionado - por favor ignore o erro.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Se estiver a utilizar o Visual Studio ou o Visual Studio para Mac, prima **F5** para executar a aplicação, caso contrário, execute a aplicação através de pedido de comando, consola ou terminal:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> Em que:
> * *{ProjectFolder}* é a pasta onde extraiu o ficheiro zip. Exemplo **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

Como resultado, deverá ver uma lista de utilizadores no seu diretório AD Azure.

> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros do projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório GitHub para esta amostra.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client)é a biblioteca utilizada para assinar nos utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Como descrito, este quickstart solicita fichas usando a própria identidade da aplicação em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como *[o fluxo de credenciais de clientes](v2-oauth2-client-creds-grant-flow.md)*. Para obter mais informações sobre como utilizar MSAL.NET com o fluxo de credenciais do cliente, consulte [este artigo](https://aka.ms/msal-net-client-credentials).

 Pode instalar MSAL.NET executando o seguinte comando na **Consola de Gestor de Pacotes** do Estúdio Visual:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize a MSAL com o código seguinte:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Em que: | Description |
> |---------|---------|
> | `config.ClientSecret` | É o segredo do cliente criado para a aplicação no Portal Azure. |
> | `config.ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `config.Authority`    | (Opcional) O ponto final STS para o utilizador autenticar. Normalmente `https://login.microsoftonline.com/{tenant}` para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id do seu inquilino.|

Para mais informações, consulte a [documentação de referência para `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token usando a identidade da app, use `AcquireTokenForClient` o método:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Em que:| Description |
> |---------|---------|
> | `scopes` | Contém os âmbitos solicitados. Para clientes confidenciais, este deve utilizar o formato semelhante `{Application ID URI}/.default` para indicar que os âmbitos que estão a ser solicitados são os que estão definidos estáticamente no objeto da aplicação definido no Portal Azure (para o Microsoft Graph, aponta `{Application ID URI}` `https://graph.microsoft.com` para). Para APIs web personalizado, `{Application ID URI}` é definido em Expor uma secção **API** no Registo de Aplicação do Portal Azure (Pré-visualização). |

Para mais informações, consulte a [documentação de referência para `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações daemon, consulte a visão geral do cenário:

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)
