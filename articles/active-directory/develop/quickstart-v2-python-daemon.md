---
title: Plataforma de identidade da Microsoft Python daemon  Azure
description: Saiba como um processo Python pode obter um token de acesso e chamar um API protegido pelo ponto final da plataforma de identidade da Microsoft, usando a própria identidade da app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Python
ms.openlocfilehash: f80f586c783293f87e3b7de469eff07d2e4802d8
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160902"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Quickstart: Adquira um símbolo e ligue para o Microsoft Graph API a partir de uma aplicação de consola Python usando a identidade da app

Neste arranque rápido, escreva uma aplicação Python que obtenha um sinal de acesso usando a identidade da aplicação, e depois ligue para o Microsoft Graph API para apresentar uma [lista de utilizadores](https://docs.microsoft.com/graph/api/user-list) no diretório. Este cenário é útil para situações em que o trabalho sem cabeça, sem supervisão ou um serviço de janelas precisa de ser executado com uma identidade de aplicação, em vez da identidade de um utilizador.

> [!div renderon="docs"]
> ![Mostra como a aplicação de amostras gerada por este quickstart funciona](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, precisa de:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) ou [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido

> [!div renderon="docs" class="sxs-lookup"]
>
> Tem duas opções para iniciar a sua aplicação quickstart: Express (Opção 1 abaixo) e Manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá ao novo portal Azure - Painel de inscrições de [aplicativos.](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs)
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

#### <a name="step-2-download-your-python-project"></a>Passo 2: Descarregue o seu projeto Python

> [!div renderon="docs"]
> [Descarregue o projeto Python daemon](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código]()

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
> #### <a name="step-3-configure-your-python-project"></a>Passo 3: Configure o seu projeto Python
> 
> 1. Extraia o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
> 1. Navegue para a subpasta **1-Call-MsGraph-WithSecret".**
> 1. Editar **parâmetros.json** e substituir os valores dos campos `authority`, `client_id`e `secret` pelo seguinte corte:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é o **ID da Aplicação (cliente)** que registou.
>    - `Enter_the_Tenant_Id_Here` - substitua este valor pelo nome **id do arrendatário** ou **do inquilino** (por exemplo, contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here` - substitua este valor pelo segredo do cliente criado no passo 1.
>
> > [!TIP]
> > Para encontrar os valores de Id de **Aplicação (cliente),** **Id de Diretório (inquilino),** vá à página de **visão geral** da aplicação no portal Azure. Para gerar uma nova chave, vá à página **de Certificados e segredos.**
    
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento do administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento do administrador

Se tentar executar a aplicação neste momento, receberá *HTTP 403 -* Erro proibido: `Insufficient privileges to complete the operation`. Este erro ocorre porque qualquer *permissão apenas de aplicações* requer consentimento da Admin: um administrador global do seu diretório deve dar consentimento à sua aplicação. Selecione uma das opções abaixo, dependendo do seu papel:

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

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passo 4: Executar a aplicação

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passo 5: Executar a aplicação

Você precisará instalar as dependências desta amostra uma vez

```console
pip install -r requirements.txt
```

Em seguida, executar a aplicação através de pedido de comando ou consola:

```console
python confidential_client_secret_sample.py parameters.json
```

Deve ver na saída da consola algum fragmento de Json que representa uma lista de utilizadores no seu diretório Azure AD.

> [!IMPORTANT]
> Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Uma vez que o segredo do cliente é adicionado como um texto simples aos seus ficheiros de projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) no mesmo repositório GitHub para esta amostra, mas na segunda pasta **2-Call-MsGraph-WithCertificate**

## <a name="more-information"></a>Mais informações

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) é a biblioteca usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade microsoft. Como descrito, este quickstart solicita tokens usando a própria identidade da aplicação em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como fluxo de credenciais de *[cliente.](v2-oauth2-client-creds-grant-flow.md)* Para obter mais informações sobre como usar o MSAL Python com aplicações daemon, consulte [este artigo](scenario-daemon-overview.md).

 Pode instalar o MSAL Python executando o seguinte comando do pip.

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

> | Em que: ||
> |---------|---------|
> | `config["secret"]` | É o segredo do cliente criado para a aplicação no Portal Azure. |
> | `config["client_id"]` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `config["authority"]`    | O ponto final STS para o utilizador autenticar. Normalmente <https://login.microsoftonline.com/{tenant}> para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu inquilino ID.|

Para mais informações, por favor leia as [informações sobre](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication) a documentação de referência para `ConfidentialClientApplication`

### <a name="requesting-tokens"></a>Solicitar tokens

Para solicitar um símbolo utilizando a identidade da app, utilize `AcquireTokenForClient` método:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Em que:| |
> |---------|---------|
> | `config["scope"]` | Contém os âmbitos solicitados. Para clientes confidenciais, este deve utilizar o formato semelhante ao `{Application ID URI}/.default` para indicar que os âmbitos que estão a ser solicitados são os definidos estáticamente definidos no objeto da aplicação definido no Portal Azure (para o Microsoft Graph, `{Application ID URI}` aponta para `https://graph.microsoft.com`). Para APIs web personalizados, `{Application ID URI}` é definido em Expor uma secção **DeAPi** no Registo de Aplicação do Portal Azure (Pré-visualização). |

Para mais informações, por favor leia as [informações sobre](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) a documentação de referência para `AcquireTokenForClient`

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre aplicações daemon, consulte a página de aterragem do cenário

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](scenario-daemon-overview.md)

Para o tutorial de aplicação daemon, consulte:

> [!div class="nextstepaction"]
> [Tutorial de consola Daemon Python](https://github.com/Azure-Samples/ms-identity-python-daemon)

Saiba mais sobre permissões e consentimento:

> [!div class="nextstepaction"]
> [Permissões e Consentimento](v2-permissions-and-consent.md)

Para saber mais sobre o fluxo de auth para este cenário, consulte o fluxo de credenciais de cliente Oauth 2.0:

> [!div class="nextstepaction"]
> [Credenciais de cliente Oauth flow](v2-oauth2-client-creds-grant-flow.md)

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
