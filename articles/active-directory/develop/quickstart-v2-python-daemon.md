---
title: Daemon Python da plataforma de identidade da Microsoft | Azure
description: Saiba como um processo do Python pode obter um token de acesso e chamar uma API protegida pelo ponto de extremidade da plataforma de identidade da Microsoft, usando a própria identidade do aplicativo
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
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68715186c624cc5ce8702073129752487a8cea63
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964016"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Início rápido: adquirir um token e chamar Microsoft Graph API de um aplicativo de console do Python usando a identidade do aplicativo

Neste guia de início rápido, escreva um aplicativo Python que obtém um token de acesso usando a identidade do aplicativo e, em seguida, chama o Microsoft Graph API para exibir uma [lista de usuários](https://docs.microsoft.com/graph/api/user-list) no diretório. Esse cenário é útil para situações em que um trabalho não assistido e sem periféricos ou um serviço do Windows precisa ser executado com uma identidade de aplicativo, em vez de uma identidade de usuário.

> [!div renderon="docs"]
> ![mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, você precisa de:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) ou [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Você tem duas opções para iniciar seu aplicativo de início rápido: Express (opção 1 abaixo) e manual (opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs) .
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
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **registrar um aplicativo** for exibida, insira as informações de registro do aplicativo. 
> 1. Na seção **nome** , insira um nome de aplicativo significativo que será exibido para os usuários do aplicativo, por exemplo `Daemon-console`, em seguida, selecione **registrar** para criar o aplicativo.
> 1. Depois de registrado, selecione o menu **certificados & segredos** .
> 1. Em **segredos do cliente**, selecione **+ novo segredo do cliente**. Dê um nome a ele e selecione **Adicionar**. Copie o segredo em um local seguro. Você precisará dele para usar em seu código.
> 1. Agora, selecione o menu **permissões de API** , selecione **+ Adicionar um** botão de permissão, selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicativo**.
> 1. Em nó de **usuário** , selecione **User. Read. All**e, em seguida, selecione **adicionar permissões**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Baixe e configure seu aplicativo de início rápido
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
> Para que o exemplo de código para este guia de início rápido funcione, você precisa criar um segredo do cliente e adicionar a permissão **User. Read. All** do aplicativo do API do Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-python-project"></a>Etapa 2: baixar seu projeto Python

[Baixe o projeto do daemon do Python](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

#### <a name="step-3-configure-your-python-project"></a>Etapa 3: configurar seu projeto Python

1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
1. Navegue até a subpasta **1-Call-MsGraph-WithSecret "** .
1. Edite **Parameters. JSON** e substitua os valores dos campos `authority`, `client_id`e `secret` pelo seguinte trecho:

    ```json
    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    "client_id": "Enter_the_Application_Id_Here",
    "secret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Gerar um novo segredo do cliente]()
    
    > [!div class="sxs-lookup" renderon="portal"]
    > > [!NOTE]
    > > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.
    
    > [!div renderon="docs"]
    >> Onde:
    >> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
    >> * `Enter_the_Tenant_Id_Here`-Substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, contoso.Microsoft.com)
    >> * `Enter_the_Client_Secret_Here`-Substitua esse valor pelo segredo do cliente criado na etapa 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Para localizar os valores de **ID do aplicativo (cliente)** , **ID do diretório (locatário)** , vá para a página **visão geral** do aplicativo na portal do Azure. Para gerar uma nova chave, acesse **certificados & página segredos** .
    
#### <a name="step-4-admin-consent"></a>Etapa 4: consentimento do administrador

Se você tentar executar o aplicativo neste ponto, receberá o erro *HTTP 403-Proibido* : `Insufficient privileges to complete the operation`. Esse erro ocorre porque qualquer *permissão somente de aplicativo* requer o consentimento do administrador: um administrador global do seu diretório deve dar consentimento ao seu aplicativo. Selecione uma das opções abaixo, dependendo da sua função:

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
>> Onde:
>> * `Enter_the_Tenant_Id_Here`-Substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, contoso.Microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

#### <a name="step-5-run-the-application"></a>Etapa 5: executar o aplicativo

Você precisará instalar as dependências deste exemplo uma vez

```console
pip install -r requirements.txt
```

Em seguida, execute o aplicativo via prompt de comando ou console:

```console
python confidential_client_secret_sample.py parameters.json
```

Você deve ver no console a saída de algum fragmento JSON que representa uma lista de usuários em seu diretório do Azure AD.

> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto sem formatação aos arquivos do projeto, por motivos de segurança, é recomendável que você use um certificado em vez de um segredo do cliente antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) no mesmo repositório do GitHub para este exemplo, mas na segunda pasta **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Mais informações

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Conforme descrito, este início rápido solicita tokens usando a identidade própria do aplicativo em vez de permissões delegadas. O fluxo de autenticação usado nesse caso é conhecido como *[fluxo OAuth de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)* . Para obter mais informações sobre como usar o MSAL Python com aplicativos daemon, consulte [Este artigo](scenario-daemon-overview.md).

 Você pode instalar o MSAL Python executando o comando Pip a seguir.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```Python
import msal
```

Em seguida, inicialize a MSAL com o código seguinte:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Onde: ||
> |---------|---------|
> | `config["secret"]` | É o segredo do cliente criado para o aplicativo no portal do Azure. |
> | `config["client_id"]` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `config["authority"]`    | O ponto final STS para o utilizador autenticar. Geralmente <https://login.microsoftonline.com/{tenant}> para nuvem pública, em que {Tenant} é o nome do seu locatário ou sua ID de locatário.|

Para obter mais informações, consulte a [documentação de referência para `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token usando a identidade do aplicativo, use `AcquireTokenForClient` método:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Onde:| |
> |---------|---------|
> | `config["scope"]` | Contém os escopos solicitados. Para clientes confidenciais, isso deve usar o formato semelhante a `{Application ID URI}/.default` para indicar que os escopos que estão sendo solicitados são aqueles definidos estaticamente no conjunto de objetos do aplicativo no portal do Azure (por Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs Web personalizadas, `{Application ID URI}` é definido em **expor uma** seção de API no registro de aplicativo do portal do Azure (versão prévia). |

Para obter mais informações, consulte a [documentação de referência para `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os aplicativos daemon, consulte a página de aterrissagem do cenário

> [!div class="nextstepaction"]
> [Aplicativo daemon que chama APIs da Web](scenario-daemon-overview.md)

Para o tutorial do aplicativo daemon, consulte:

> [!div class="nextstepaction"]
> [Tutorial do console Python do daemon](https://github.com/Azure-Samples/ms-identity-python-daemon)

Saiba mais sobre as permissões e o consentimento:

> [!div class="nextstepaction"]
> [Permissões e consentimento](v2-permissions-and-consent.md)

Para saber mais sobre o fluxo de autenticação para este cenário, consulte o fluxo de credenciais do cliente OAuth 2,0:

> [!div class="nextstepaction"]
> [Fluxo OAuth de credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
