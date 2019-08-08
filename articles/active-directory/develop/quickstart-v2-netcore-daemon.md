---
title: Daemon do .NET Core da plataforma Microsoft Identity | Azure
description: Saiba como um processo do .NET Core pode obter um token de acesso e chamar uma API protegida pelo ponto de extremidade da plataforma de identidade da Microsoft usando a própria identidade do aplicativo
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a2b6d01802fd819471a9cfb382166e6293261ca
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852882"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-console-app-using-apps-identity"></a>Início rápido: Adquirir um token e chamar Microsoft Graph API de um aplicativo de console usando a identidade do aplicativo

Neste guia de início rápido, você aprenderá a escrever um aplicativo .NET Core que pode obter um token de acesso usando a própria identidade do aplicativo e, em seguida, chamar a API de Microsoft Graph para exibir uma [lista de usuários](https://docs.microsoft.com/graph/api/user-list) no diretório. Esse cenário é útil para situações em que um trabalho não assistido e sem periféricos ou um serviço do Windows precisa ser executado com uma identidade de aplicativo, em vez de uma identidade de usuário.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer o [.NET Core 2,2](https://www.microsoft.com/net/download/dotnet-core/2.2).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a aplicação de início rápido:
> * Express [Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Manual [Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código
>
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) .
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **registrar um aplicativo** for exibida, insira as informações de registro do aplicativo. 
> 1. Na seção **nome** , insira um nome de aplicativo significativo que será exibido aos usuários do aplicativo, por exemplo `Daemon-console`, e, em seguida, selecione **registrar** para criar o aplicativo.
> 1. Depois de registrado, selecione o menu **certificados & segredos** .
> 1. Em **segredos do cliente**, selecione **+ novo segredo do cliente**. Dê um nome a ele e selecione **Adicionar**. Copie o segredo em um local seguro. Você precisará dele para usar em seu código.
> 1. Agora, selecione o menu **permissões de API** , selecione **+ Adicionar um** botão de permissão, selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicativo**.
> 1. Em nó de **usuário** , selecione **User. Read. All**e, em seguida, selecione **adicionar permissões**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Baixe e configure seu aplicativo de início rápido
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código para este guia de início rápido funcione, você precisa criar um segredo do cliente e adicionar a permissão **User. Read. All** do aplicativo do API do Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Passo 2: Baixe seu projeto do Visual Studio

[Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Passo 3: Configurar seu projeto do Visual Studio

1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
1. Abra a solução no Visual Studio- **daemon-console. sln** (opcional).
1. Edit **appSettings. JSON** e substitua os valores dos campos `ClientId` `Tenant` e `ClientSecret` pelo seguinte:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Gerar um novo segredo do cliente]()
    
    > [!div class="sxs-lookup" renderon="portal"]
    > > [!NOTE]
    > > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.
    
    > [!div renderon="docs"]
    >> Em que:
    >> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
    >> * `Enter_the_Tenant_Id_Here`-Substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, contoso.Microsoft.com)
    >> * `Enter_the_Client_Secret_Here`-Substitua esse valor pelo segredo do cliente criado na etapa 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Para localizar os valores de **ID do aplicativo (cliente)** , **ID do diretório (locatário)** , vá para a página **visão geral** do aplicativo na portal do Azure. Para gerar uma nova chave, acesse **certificados & página segredos** .
    
#### <a name="step-4-admin-consent"></a>Passo 4: Consentimento de admin

Se você tentar executar o aplicativo neste ponto, receberá o erro *HTTP 403-Proibido* : `Insufficient privileges to complete the operation`. Isso acontece porque qualquer *permissão somente de aplicativo* requer o consentimento do administrador, o que significa que um administrador global do seu diretório deve dar consentimento ao seu aplicativo. Selecione uma das opções abaixo, dependendo da sua função:

##### <a name="global-tenant-administrator"></a>Administrador de locatário global

> [!div renderon="docs"]
> Se você for um administrador de locatário global, acesse a página **permissões de API** no registro do aplicativo do portal do Azure (versão prévia) e selecione **conceder consentimento de administrador para {nome do locatário}** (em que {nome do locatário} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se você for um administrador global, acesse a página **permissões de API** , selecione **conceder consentimento de administrador para Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ir para a página permissões de API]()

##### <a name="standard-user"></a>Usuário padrão

Se você for um usuário padrão do seu locatário, precisará pedir a um administrador global para conceder consentimento de administrador para seu aplicativo. Para fazer isso, forneça a seguinte URL ao seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here`-Substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, contoso.Microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

> [!NOTE]
> Você pode ver o erro *' AADSTS50011: Nenhum endereço de resposta é registrado para o aplicativo* ' depois de conceder consentimento ao aplicativo usando a URL anterior. Isso acontece porque este aplicativo e a URL não têm um URI de redirecionamento. ignore o erro.

#### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Se você estiver usando o Visual Studio, pressione **F5** para executar o aplicativo, caso contrário, execute o aplicativo via prompt de comando ou console:

```console
cd {ProjectFolder}\daemon-console
dotnet run
```

> Em que:
> * *{ProjectFolder}* é a pasta onde você extraiu o arquivo zip. Exemplo de **C:\Azure-Samples\active-Directory-dotnetcore-daemon-v2**

Você deve ver uma lista de usuários em seu diretório do Azure AD como resultado.

> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto sem formatação aos arquivos do projeto, por motivos de segurança, é recomendável que você use um certificado em vez de um segredo do cliente antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) no repositório do GitHub para este exemplo.

## <a name="more-information"></a>Mais informações

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Conforme descrito, este início rápido solicita tokens usando a identidade própria do aplicativo em vez de permissões delegadas. O fluxo de autenticação usado nesse caso é conhecido como *[fluxo OAuth de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)* . Para obter mais informações sobre como usar o MSAL.NET com o fluxo de credenciais de cliente, consulte [Este artigo](https://aka.ms/msal-net-client-credentials).

 Você pode instalar o MSAL.NET executando o seguinte comando no **console do Gerenciador de pacotes**do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

Como alternativa, se você não estiver usando o Visual Studio, poderá executar o seguinte comando para adicionar o MSAL ao seu projeto:

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
> | `config.ClientSecret` | É o segredo do cliente criado para o aplicativo no portal do Azure. |
> | `config.ClientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `config.Authority`    | Adicional O ponto de extremidade STS para o usuário autenticar. Geralmente <https://login.microsoftonline.com/{tenant}> , para nuvem pública, em que {Tenant} é o nome do seu locatário ou sua ID de locatário.|

Para obter mais informações, consulte a [documentação de referência `ConfidentialClientApplication` para](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.iconfidentialclientapplication?view=azure-dotnet)

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token usando a identidade do aplicativo, `AcquireTokenForClient` use o método:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Em que:| |
> |---------|---------|
> | `scopes` | Contém os escopos solicitados. Para clientes confidenciais, isso deve usar o formato semelhante a `{Application ID URI}/.default` para indicar que os escopos que estão sendo solicitados são aqueles definidos estaticamente no conjunto de objetos de aplicativo no portal do Azure ( `{Application ID URI}` por Microsoft Graph `https://graph.microsoft.com`, aponta para). Para APIs Web personalizadas, `{Application ID URI}` é definido em **expor uma** seção de API no registro do aplicativo do portal do Azure (versão prévia). |

Para obter mais informações, consulte a [documentação de referência `AcquireTokenForClient` para](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient?view=azure-dotnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Exemplo de daemon do .NET Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

Saiba mais sobre as permissões e o consentimento:

> [!div class="nextstepaction"]
> [Permissões e consentimento](v2-permissions-and-consent.md)

Para saber mais sobre o fluxo de autenticação para este cenário, consulte o fluxo de credenciais do cliente OAuth 2,0:

> [!div class="nextstepaction"]
> [Fluxo OAuth de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)

> [!div class="nextstepaction"]
> [Fluxos de credencial do cliente com MSAL.NET](https://aka.ms/msal-net-client-credentials)

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
