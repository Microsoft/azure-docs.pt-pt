---
title: 'Quickstart: Ligue para o Microsoft Graph de um daemon Python Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende como um processo Python pode obter um token de acesso e chamar uma API protegida pelo ponto final da plataforma de identidade Microsoft, usando a própria identidade da app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 8e947a7db7b10746ebd01726fd2103cf3e5c8df6
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533213"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Python usando a identidade da app

Neste quickstart, escreva uma aplicação Python que obtém um token de acesso usando a identidade da aplicação e, em seguida, ligue para a Microsoft Graph API para apresentar uma [lista de utilizadores](/graph/api/user-list) no diretório. Este cenário é útil para situações em que um trabalho sem cabeça, sem supervisão ou um serviço de janelas precisa de ser executado com uma identidade de aplicação, em vez da identidade de um utilizador.

> [!div renderon="docs"]
> ![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer esta amostra, precisa:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) ou [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a sua aplicação de arranque rápido: Express (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao novo [portal Azure - Painel de inscrições](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs) da App.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando aparecer uma página de **inscrição,** insira as informações de registo do seu pedido.
> 1. Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por `Daemon-console` exemplo, selecione **Registar-se** para criar a aplicação.
> 1. Uma vez registado, selecione o menu **Certificados & segredos.**
> 1. Sob **os segredos do Cliente,** selecione **+ Novo segredo de cliente.** Dê-lhe um nome e **selecione Adicionar**. Copie o segredo num local seguro. Vai precisar dele para usar no seu código.
> 1. Agora, selecione o menu **API Permissões,** selecione + Adicione um botão **de permissão,** selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicação**.
> 1. No nó **do utilizador,** selecione **User.Read.All** e, em seguida, selecione **Adicionar permissões**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Faça o download e configuure a sua app quickstart
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
> Para que a amostra de código para este arranque rápido funcione, é necessário criar um segredo de cliente e adicionar a permissão de aplicação do **Graph API.Read.All.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-your-python-project"></a>Passo 2: Descarregue o seu projeto Python

> [!div renderon="docs"]
> [Descarregue o projeto Python Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Passo 3: Configurar o seu projeto Python
>
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Navegue para a sub-pasta **1-Call-MsGraph-WithSecret".**
> 1. Editar **parameters.js** e substituir os valores dos `authority` `client_id` campos, e `secret` com o seguinte corte:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
>    - `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino Ou** Inquilino (por exemplo, contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here` - substituir este valor pelo segredo do cliente criado no passo 1.
>
> > [!TIP]
> > Para encontrar os valores de ID de **aplicação (cliente),** **ID de diretório (inquilino),** aceda à página **geral** da aplicação no portal Azure. Para gerar uma nova chave, aceda à página **de segredos & Certificados.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento administrativo

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento administrativo

Se tentar executar a aplicação neste momento, receberá *HTTP 403 - Erro proibido:* `Insufficient privileges to complete the operation` . Este erro acontece porque qualquer *permissão apenas de aplicação* requer o consentimento do Administrador: um administrador global do seu diretório deve dar consentimento à sua aplicação. Selecione uma das opções abaixo, dependendo da sua função:

##### <a name="global-tenant-administrator"></a>Administrador de inquilino global

> [!div renderon="docs"]
> Se for administrador de inquilino global, vá à página **de Permissões da API** no Registo de Pedidos do Portal Azure (Pré-visualização) e selecione **o consentimento de administração de subvenção para {Nome do Inquilino}** (Onde {Nome do Inquilino} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se for um administrador global, vá à página **de permissões da API** selecionar **o consentimento de administração do Grant para Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Aceda à página de Permissões da API]()

##### <a name="standard-user"></a>Utilizador padrão

Se é um utilizador padrão do seu inquilino, então precisa pedir a um administrador global que conceda consentimento administrativo para a sua aplicação. Para isso, dê o seguinte URL ao seu administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Em que:
>> * `Enter_the_Tenant_Id_Here`- substituir este valor pelo **nome** **de Inquilino Ou** Inquilino (por exemplo, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Terá de instalar as dependências desta amostra uma vez.

```console
pip install -r requirements.txt
```

Em seguida, executar a aplicação através do pedido de comando ou consola:

```console
python confidential_client_secret_sample.py parameters.json
```

Você deve ver na saída da consola algum fragmento Json que representa uma lista de utilizadores no seu diretório AD Azure.

> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros do projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como usar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) no mesmo repositório GitHub para esta amostra, mas na segunda pasta **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Mais informações

### <a name="msal-python"></a>Pitão MSAL

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) é a biblioteca usada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Como descrito, este quickstart solicita fichas usando a própria identidade da aplicação em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como *[o fluxo de credenciais de clientes](v2-oauth2-client-creds-grant-flow.md)*. Para obter mais informações sobre como utilizar o MSAL Python com aplicações daemon, consulte [este artigo.](scenario-daemon-overview.md)

 Pode instalar o MSAL Python executando o seguinte comando pip.

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

> | Em que: |Description |
> |---------|---------|
> | `config["secret"]` | É o segredo do cliente criado para a aplicação no Portal Azure. |
> | `config["client_id"]` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `config["authority"]`    | O ponto final STS para o utilizador autenticar. Normalmente `https://login.microsoftonline.com/{tenant}` para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id do seu inquilino.|

Para mais informações, consulte a [documentação de referência para `ConfidentialClientApplication` ](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token usando a identidade da app, use `AcquireTokenForClient` o método:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Em que:| Description |
> |---------|---------|
> | `config["scope"]` | Contém os âmbitos solicitados. Para clientes confidenciais, este deve utilizar o formato semelhante `{Application ID URI}/.default` para indicar que os âmbitos que estão a ser solicitados são os que estão definidos estáticamente no objeto da aplicação definido no Portal Azure (para o Microsoft Graph, aponta `{Application ID URI}` `https://graph.microsoft.com` para). Para APIs web personalizado, `{Application ID URI}` é definido em Expor uma secção **API** no Registo de Aplicação do Portal Azure (Pré-visualização). |

Para mais informações, consulte a [documentação de referência para `AcquireTokenForClient` ](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações daemon, consulte a página de aterragem do cenário

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)
