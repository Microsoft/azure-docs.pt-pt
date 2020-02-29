---
title: Obter token & call Microsoft Graph com identidade de app de consola / Azure
titleSuffix: Microsoft identity platform
description: Aprenda a obter um símbolo e ligue para um Microsoft Graph API protegido com ele a partir de uma aplicação .NET Core
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f3e1581e2e96c0915fa92a51f60311f90eb79ee5
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160849"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Quickstart: Adquira um símbolo e ligue para a Microsoft Graph API usando a identidade da app de consola

Neste arranque rápido, você aprenderá a escrever uma aplicação .NET Core que pode obter um token de acesso usando a própria identidade da aplicação e, em seguida, ligar para a Microsoft Graph API para apresentar uma [lista de utilizadores](https://docs.microsoft.com/graph/api/user-list) no diretório. Este cenário é útil para situações em que o trabalho sem cabeça, sem supervisão ou um serviço de janelas precisa de ser executado com uma identidade de aplicação, em vez da identidade de um utilizador. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

## <a name="prerequisites"></a>Pré-requisitos

Este arranque rápido requer [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a sua aplicação quickstart: Express (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao novo portal Azure - Painel de inscrições de [aplicativos.](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue na plataforma de identidade da Microsoft para programadores da página de registos de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando aparecer uma página de **inscrição,** insira as informações de registo da sua candidatura. 
> 1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `Daemon-console`, em seguida, selecione **Register** para criar a aplicação.
> 1. Uma vez registado, selecione o menu **Certificados e segredos.**
> 1. Sob **os segredos do Cliente**, selecione + Novo segredo de **cliente.** Dê-lhe um nome e selecione **Adicionar**. Copie o segredo num local seguro. Vai precisar que seja usado no seu código.
> 1. Agora, selecione o menu **DePermissões API,** selecione + Adicione um botão **de permissão,** selecione **Microsoft Graph**.
> 1. Selecione **permissões de pedido**.
> 1. Sob o nó **do Utilizador,** selecione **User.Read.All**e, em seguida, selecione **Adicionar permissões**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Descarregue e configure a sua aplicação quickstart
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
> Para a amostra de código para este início rápido funcionar, você precisa criar um segredo de cliente, e adicionar O Utilizador da API **graph.Read.Todas as** permissões de aplicação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Transfira o seu projeto do Visual Studio

> [!div renderon="docs"]
> [Descarregue o projeto Estúdio Visual](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código]()

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here
    
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Transferir o seu projeto do Visual Studio
> 
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Abra a solução em Visual Studio - **1-Call-MSGraph\daemon-console.sln** (opcional).
> 1. Editar **appssettings.json** e substituir os valores dos campos `ClientId`, `Tenant` e `ClientSecret` com os seguintes:
> 
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>   Em que:
>   - `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
>   - `Enter_the_Tenant_Id_Here` - substitua este valor pelo nome **id do arrendatário** ou **do inquilino** (por exemplo, contoso.microsoft.com)
>   - `Enter_the_Client_Secret_Here` - substitua este valor pelo segredo do cliente criado no passo 1.

> [!div renderon="docs"]
> > [!TIP]
> > Para encontrar os valores de Id de **Aplicação (cliente),** **Id de Diretório (inquilino),** vá à página de **visão geral** da aplicação no portal Azure. Para gerar uma nova chave, vá à página **de Certificados e segredos.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento do administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento do administrador

Se tentar executar a aplicação neste momento, receberá *HTTP 403 -* Erro proibido: `Insufficient privileges to complete the operation`. Isto acontece porque qualquer *permissão apenas de aplicações* requer consentimento da Admin, o que significa que um administrador global do seu diretório deve dar consentimento à sua aplicação. Selecione uma das opções abaixo, dependendo do seu papel:

##### <a name="global-tenant-administrator"></a>Administrador global de inquilinos

> [!div renderon="docs"]
> Se você é um administrador de inquilino global, vá à página **de Permissões API** no Registo de Inscrição de Candidatura do Portal Azure (Pré-visualização) e selecione **Grant admin consent for {Tenant Name}** (Onde {Nome do inquilino} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se você é um administrador global, vá à página **de Permissões API** selecione **o consentimento do administrador grant para Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Vá à página de Permissões DaPi]()

##### <a name="standard-user"></a>Utilizador padrão

Se é um utilizador padrão do seu inquilino, então precisa pedir a um administrador global que conceda consentimento administrativo para a sua candidatura. Para tal, dê o seguinte URL ao seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here` - substitua este valor pelo nome **id do arrendatário** ou **do inquilino** (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

> [!NOTE]
> Pode ver o erro *'AADSTS50011: Não está registado nenhum endereço de resposta para a aplicação'* depois de conceder o consentimento à aplicação utilizando o URL anterior. Isto acontece porque esta aplicação e o URL não têm um URI redirecionado - por favor ignore o erro.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Se estiver a utilizar o Visual Studio, prima **F5** para executar a aplicação, caso contrário, executar a aplicação através de pedido de comando ou consola:

```console
cd {ProjectFolder}\daemon-console\1-Call-Graph
dotnet run
```

> Em que:
> * *{ProjectFolder}* é a pasta onde extraiu o ficheiro zip. Exemplo **C:\Azure-Samples\active-directy-dotnetcore-daemon-v2**

Como resultado, deverá ver uma lista de utilizadores no seu diretório Azure AD.

> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Uma vez que o segredo do cliente é adicionado como um texto simples aos seus ficheiros de projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório GitHub para esta amostra.

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a aplicação de amostragerada por este quickstart](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL[(Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client)é a biblioteca utilizada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade microsoft. Como descrito, este quickstart solicita tokens usando a própria identidade da aplicação em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como fluxo de credenciais de *[cliente.](v2-oauth2-client-creds-grant-flow.md)* Para obter mais informações sobre como usar MSAL.NET com fluxo de credenciais de cliente, consulte [este artigo](https://aka.ms/msal-net-client-credentials).

 Pode instalar MSAL.NET executando o seguinte comando na Consola de Gestor de **Pacotes**do Estúdio Visual:

```powershell
Install-Package Microsoft.Identity.Client
```

Em alternativa, se não estiver a utilizar o Visual Studio, pode executar o seguinte comando para adicionar MSAL ao seu projeto:

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
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
);
```

> | Em que: ||
> |---------|---------|
> | `config.ClientSecret` | É o segredo do cliente criado para a aplicação no Portal Azure. |
> | `config.ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `config.Authority`    | (Opcional) O ponto final do STS para o utilizador autenticar. Normalmente <https://login.microsoftonline.com/{tenant}> para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu inquilino ID.|

Para mais informações, por favor leia as [informações sobre](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.iconfidentialclientapplication?view=azure-dotnet) a documentação de referência para `ConfidentialClientApplication`

### <a name="requesting-tokens"></a>Solicitar tokens

Para solicitar um símbolo utilizando a identidade da app, utilize `AcquireTokenForClient` método:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Em que:| |
> |---------|---------|
> | `scopes` | Contém os âmbitos solicitados. Para clientes confidenciais, este deve utilizar o formato semelhante ao `{Application ID URI}/.default` para indicar que os âmbitos que estão a ser solicitados são os definidos estáticamente definidos no objeto da aplicação definido no Portal Azure (para o Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs web personalizados, `{Application ID URI}` é definido em Expor uma secção **DeAPi** no Registo de Aplicação do Portal Azure (Pré-visualização). |

Para mais informações, por favor leia as [informações sobre](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient?view=azure-dotnet) a documentação de referência para `AcquireTokenForClient`

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações daemon, consulte a página de aterragem do cenário

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)

Para o tutorial de aplicação daemon, consulte:

> [!div class="nextstepaction"]
> [Tutorial de consola Daemon .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

Saiba mais sobre permissões e consentimento:

> [!div class="nextstepaction"]
> [Permissões e Consentimento](v2-permissions-and-consent.md)

Para saber mais sobre o fluxo de auth para este cenário, consulte o fluxo de credenciais de cliente Oauth 2.0:

> [!div class="nextstepaction"]
> [Credenciais de cliente Oauth flow](v2-oauth2-client-creds-grant-flow.md)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
