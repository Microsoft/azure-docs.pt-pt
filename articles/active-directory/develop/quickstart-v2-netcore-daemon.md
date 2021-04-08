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
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022930"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Quickstart: Obtenha um token e ligue para a Microsoft Graph API usando a identidade de uma aplicação de consola

Neste arranque rápido, descarrega e executa uma amostra de código que demonstra como uma aplicação de consola .NET Core pode obter um token de acesso para ligar para a API do Microsoft Graph e apresentar uma [lista de utilizadores](/graph/api/user-list) no diretório. A amostra de código também demonstra como um trabalho ou um serviço Windows pode funcionar com uma identidade de aplicação, em vez da identidade de um utilizador. A aplicação de consola de amostra neste quickstart também é uma aplicação daemon, por isso é uma aplicação confidencial do cliente.

> [!div renderon="docs"]
> O seguinte diagrama mostra como funciona a aplicação da amostra:
>
> ![Diagrama que mostra como funciona a aplicação da amostra gerada por este quickstart.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Pré-requisitos

Este arranque rápido requer [.NET Core 3.1](https://www.microsoft.com/net/download/dotnet-core) mas também funcionará com .NET Core 5.0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registe-se e descarregue a app

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para começar a construir a sua aplicação: configuração automática ou manual.
>
> ### <a name="automatic-configuration"></a>Configuração automática
>
> Se quiser registar-se e configurar automaticamente a sua aplicação e, em seguida, descarregar a amostra de código, siga estes passos:
>
> 1. Aceda à página do <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">portal Azure para o registo de aplicações.</a>
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação num clique.
>
> ### <a name="manual-configuration"></a>Configuração manual
>
> Se pretender configurar manualmente a sua aplicação e a sua amostra de código, utilize os seguintes procedimentos.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal </span> Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar a inscrição.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Para **nome,** insira um nome para a sua candidatura. Por exemplo, insira **a consola Daemon**. Os utilizadores da sua aplicação verão este nome e poderão alterá-lo mais tarde.
> 1. Selecione **Registar** para criar a aplicação.
> 1. Em **Gerir**, selecione **Certificados e segredos**.
> 1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente,** insira um nome e, em seguida, selecione **Add**. Grave o valor secreto num local seguro para ser utilizado num passo posterior.
> 1. Em **Gestão**, selecione **Permissões API**  >  **Adicione uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicação**.
> 1. No nó **do utilizador,** selecione **User.Read.All** e, em seguida, selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Faça o download e configuure a sua app quickstart
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
> Para que a amostra de código neste arranque rápido funcione, crie um segredo de cliente e adicione a permissão de pedido da API do **Gráfico.Read.All.**
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
> Executar o projeto utilizando o Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar o projeto do Visual Studio
>
> 1. Extraia o ficheiro .zip para uma pasta local que está perto da raiz do disco. Por exemplo, extrair para *C:\Azure-Samples*.
>
>    Recomendamos extrair o arquivo num diretório perto da raiz da sua unidade para evitar erros causados por limitações de comprimento do caminho no Windows.
>
> 1. Abra a solução em Visual Studio: *1-Call-MSGraph\daemon-consola.sln* (opcional).
> 1. Em *appsettings.js,* substitua os valores `Tenant` `ClientId` de, e `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    Neste código:
>    - `Enter_the_Application_Id_Here` é o ID de aplicação (cliente) para a aplicação que registou.
        Para encontrar os valores para o ID da aplicação (cliente) e o ID do diretório (inquilino), **aceda** à página geral da aplicação no portal Azure.
>    - `Enter_the_Tenant_Id_Here`Substitua-o pelo ID do inquilino ou pelo nome do inquilino (por exemplo, `contoso.microsoft.com` ).
>    - `Enter_the_Client_Secret_Here`Substitua-o pelo segredo do cliente que criou no passo 1.
    Para gerar uma nova chave, aceda à página **certificados & segredos.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento administrativo

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento administrativo

Se tentar executar a aplicação neste momento, receberá um *ERRO HTTP 403 - Proibido:* "Privilégios insuficientes para completar a operação." Este erro acontece porque qualquer permissão apenas de aplicação requer que um administrador global do seu diretório dê consentimento à sua aplicação. Selecione uma das seguintes opções, dependendo do seu papel.

##### <a name="global-tenant-administrator"></a>Administrador de inquilino global

> [!div renderon="docs"]
> Se você é um administrador inquilino global, vá para **aplicações da Enterprise** no portal Azure. Selecione o registo da sua aplicação e selecione **Permissões** na secção de **Segurança** do painel esquerdo. Em seguida, selecione o grande botão marcado o **consentimento de administração grant para {Nome do Inquilino}** (onde **{Nome do Inquilino}** é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se for administrador global, vá à página **de Permissões** da API e selecione **o consentimento administrativo do Grant para Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Aceda à página de Permissões da API]()

##### <a name="standard-user"></a>Utilizador padrão

Se você é um utilizador padrão do seu inquilino, peça a um administrador global para conceder consentimento administrativo para a sua aplicação. Para isso, dê o seguinte URL ao seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Naquela URL:
> * `Enter_the_Tenant_Id_Here`Substitua-o pelo ID do inquilino ou pelo nome do inquilino (por exemplo, `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` é o ID de aplicação (cliente) para a aplicação que registou.

Poderá ver o erro "AADSTS50011: Nenhum endereço de resposta está registado para a aplicação" depois de conceder o consentimento à aplicação utilizando o URL anterior. Este erro acontece porque esta aplicação e o URL não têm um URI redirecionado. Pode ignorá-lo.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Se estiver a utilizar o Visual Studio ou o Visual Studio para Mac, prima **F5** para executar a aplicação. Caso contrário, execute a aplicação através de pedido de comando, consola ou terminal:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Neste código:
* `{ProjectFolder}` é a pasta onde extraiu o ficheiro .zip. Um exemplo é `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Como resultado, deverá consultar uma lista de utilizadores no Azure Ative Directory.

Esta aplicação quickstart usa um segredo de cliente para se identificar como um cliente confidencial. O segredo do cliente é adicionado como um ficheiro de texto simples aos seus ficheiros do projeto. Por razões de segurança, recomendamos que utilize um certificado em vez de um segredo de cliente antes de considerar o pedido como um pedido de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório GitHub para esta amostra.

## <a name="more-information"></a>Mais informações
Esta secção fornece uma visão geral do código necessário para iniciar seduções nos utilizadores. Esta visão geral pode ser útil para entender como o código funciona, quais são os principais argumentos e como adicionar o início de sôming a uma aplicação de consola .NET Core existente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Como funciona a amostra
>
> ![Diagrama que mostra como funciona a aplicação da amostra gerada por este quickstart.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

A Microsoft Authentication Library (MSAL, no pacote [Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client) é a biblioteca que é usada para assinar nos utilizadores e solicitar fichas para aceder a uma API protegida pela plataforma de identidade Microsoft. Este quickstart solicita fichas usando a própria identidade do pedido em vez de permissões delegadas. O fluxo de autenticação neste caso é conhecido como um [fluxo de credenciais de cliente OAuth .](v2-oauth2-client-creds-grant-flow.md) Para obter mais informações sobre como utilizar MSAL.NET com o fluxo de credenciais do cliente, consulte [este artigo](https://aka.ms/msal-net-client-credentials).

 Pode instalar MSAL.NET executando o seguinte comando na Consola Visual Studio Package Manager:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize o MSAL utilizando o seguinte código:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Elemento | Descrição |
 |---------|---------|
 | `config.ClientSecret` | O segredo do cliente criado para a aplicação no portal Azure. |
 | `config.ClientId` | O ID de candidatura (cliente) para a aplicação registada no portal Azure. Pode encontrar este valor na página **geral** da aplicação no portal Azure. |
 | `config.Authority`    | (Opcional) O ponto final do serviço de fichas de segurança (STS) para o utilizador autenticar. Normalmente é `https://login.microsoftonline.com/{tenant}` para a nuvem pública, onde está o nome do seu inquilino ou da `{tenant}` identificação do seu inquilino.|

Para mais informações, consulte a [documentação de referência para `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token utilizando a identidade da aplicação, utilize o `AcquireTokenForClient` método:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Elemento| Descrição |
|---------|---------|
| `scopes` | Contém os âmbitos solicitados. Para clientes confidenciais, este valor deve utilizar um formato semelhante a `{Application ID URI}/.default` . Este formato indica que os âmbitos solicitados são os que estão definidos estáticamente no objeto da aplicação definido no portal Azure. Para o Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com` . Para APIs web personalizado, `{Application ID URI}` é definido no portal Azure, em Registo de **Aplicação (Pré-visualização)**  >  **Expor uma API**. |

Para mais informações, consulte a [documentação de referência para `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações daemon, consulte a visão geral do cenário:

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)
