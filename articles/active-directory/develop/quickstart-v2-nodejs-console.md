---
title: 'Quickstart: Ligue para o Microsoft Graph a partir de uma aplicação de consola Node.js | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você descarrega e execute uma amostra de código que mostra como uma aplicação de consola Node.js pode obter um token de acesso e chamar uma API protegida por uma plataforma de identidade da Microsoft, usando a própria identidade da aplicação
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653750"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Quickstart: Adquira um token e ligue para a Microsoft Graph API a partir de uma aplicação de consola Node.js usando a identidade da app

Neste arranque rápido, descarrega e executa uma amostra de código que demonstra como uma aplicação de consola Node.js pode obter um token de acesso usando a identidade da aplicação para ligar para a API do Microsoft Graph e apresentar uma [lista de utilizadores](/graph/api/user-list) no diretório. A amostra de código demonstra como um trabalho sem supervisão ou serviço windows pode funcionar com uma identidade de aplicação, em vez da identidade de um utilizador.

Este quickstart utiliza a Biblioteca de Autenticação da [Microsoft para Node.js (nó MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) com a [concessão de credenciais](v2-oauth2-client-creds-grant-flow.md)do cliente .

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/)
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registe-se e descarregue a aplicação da amostra
>
> Siga os passos abaixo para começar.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Passo 1: Registar o pedido
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Procure e selecione **Azure Active Directory**.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `msal-node-cli` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Selecione **Registar**.
> 1. Em **Gerir**, selecione **Certificados e segredos**.
> 1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente,** insira um nome e, em seguida, selecione **Add**. Grave o valor secreto num local seguro para ser utilizado num passo posterior.
> 1. Em **Gestão**, selecione **Permissões API**  >  **Adicione uma permissão**. Selecione **Microsoft Graph**.
> 1. Selecione **permissões de aplicação**.
> 1. No nó **do utilizador,** selecione **User.Read.All** e, em seguida, selecione **Adicionar permissões**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Descarregue e configuure a aplicação de amostras
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal Azure
> Para que a amostra de código para este arranque rápido funcione, é necessário criar um segredo de cliente e adicionar a permissão de aplicação do **Graph API.Read.All.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-netcore-daemon/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Passo 2: Descarregue o projeto de amostra Node.js

> [!div renderon="docs"]
> [Descarregue a amostra de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descarregue a amostra de código](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Passo 3: Configurar o projeto de amostra Node.js
>
> 1. Extrair o ficheiro zip para uma pasta local próxima da raiz do disco, por exemplo, *C:/Azure-Samples*.
> 1. *Edite.env* e substitua os valores dos `TENANT_ID` `CLIENT_ID` campos, e `CLIENT_SECRET` pelo seguinte corte:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Em que:
>    - `Enter_the_Application_Id_Here` - é o **ID de Aplicação (cliente)** do pedido que registou anteriormente. Encontre este ID no painel de **visão geral** do registo da aplicação no portal Azure.
>    - `Enter_the_Tenant_Id_Here`- substituir este valor pelo **ID** do **Inquilino** ou pelo Nome do Inquilino (por exemplo, contoso.microsoft.com).  Encontre estes valores no painel de **visão geral** do registo da aplicação no portal Azure.
>    - `Enter_the_Client_Secret_Here` - substituir este valor pelo segredo do cliente que criou anteriormente. Para gerar uma nova chave, utilize **Certificados & segredos** nas definições de registo de aplicações no portal Azure.
>
> > [!WARNING]
> > Qualquer segredo de texto simples no código fonte representa um risco acrescido de segurança. Este artigo usa um segredo de cliente de texto simples apenas para a simplicidade. Use [credenciais](active-directory-certificate-credentials.md) de certificado em vez de segredos de cliente nas suas aplicações confidenciais de clientes, especialmente as que pretende implementar para a produção.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passo 3: Consentimento administrativo

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passo 4: Consentimento administrativo

Se tentar executar a aplicação neste momento, receberá *HTTP 403 - Erro proibido:* `Insufficient privileges to complete the operation` . Este erro ocorre porque qualquer *permissão apenas de aplicação* requer **consentimento administrativo:** um administrador global do seu diretório deve dar consentimento à sua aplicação. Selecione uma das opções abaixo, dependendo da sua função:

##### <a name="global-tenant-administrator"></a>Administrador de inquilino global

> [!div renderon="docs"]
> Se for administrador de inquilinos globais, vá à página **de Permissões da API** no Registo de Candidatura do portal Azure e selecione **o consentimento de administração de Concessão para {Nome do Inquilino}** (onde {Nome do Inquilino} é o nome do seu diretório).

> [!div renderon="portal" class="sxs-lookup"]
> Se for um administrador global, vá à página **de permissões da API** selecionar **o consentimento de administração do Grant para Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Aceda à página de Permissões da API]()

##### <a name="standard-user"></a>Utilizador padrão

Se é um utilizador padrão do seu inquilino, então precisa pedir a um administrador global que conceda **consentimento** administrativo para a sua aplicação. Para isso, dê o seguinte URL ao seu administrador:

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

Localize a pasta de raiz da amostra (onde `package.json` reside) num pedido de comando ou consola. Terá de instalar as dependências desta amostra uma vez:

```console
npm install
```

Em seguida, executar a aplicação através do pedido de comando ou consola:

```console
node . --op getUsers
```

Você deve ver na saída da consola algum fragmento JSON que representa uma lista de utilizadores no seu diretório AD Azure.

## <a name="about-the-code"></a>Sobre o código

Em seguida, são discutidos alguns dos aspetos importantes da aplicação da amostra.

### <a name="msal-node"></a>Nó MSAL

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) é a biblioteca usada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Como descrito, este quickstart solicita tokens por permissões de aplicação (usando a própria identidade do pedido) em vez de permissões delegadas. O fluxo de autenticação utilizado neste caso é conhecido como [fluxo de credenciais de cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md). Para obter mais informações sobre como utilizar o Nó MSAL com aplicações daemon, consulte [Cenário: Aplicação Daemon](scenario-daemon-overview.md).

 Pode instalar o nó MSAL executando o seguinte comando npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```javascript
const msal = require('@azure/msal-node');
```

Em seguida, inicialize a MSAL com o código seguinte:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Em que: |Description |
> |---------|---------|
> | `clientId` | É o **ID de Aplicação (cliente)** da aplicação registada no portal do Azure. Pode encontrar este valor na página **Descrição geral** da aplicação no portal do Azure. |
> | `authority`    | O ponto final STS para o utilizador autenticar. Normalmente `https://login.microsoftonline.com/{tenant}` para nuvem pública, onde {inquilino} é o nome do seu inquilino ou do seu id do seu inquilino.|
> | `clientSecret` | É o segredo do cliente criado para a aplicação no Portal Azure. |

Para mais informações, consulte a [documentação de referência para `ConfidentialClientApplication` ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Pedir tokens

Para solicitar um token usando a identidade da app, use `acquireTokenByClientCredential` o método:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Em que:| Description |
> |---------|---------|
> | `tokenRequest` | Contém os âmbitos solicitados. Para clientes confidenciais, este deve utilizar o formato semelhante `{Application ID URI}/.default` para indicar que os âmbitos que estão a ser solicitados são os que estão definidos estáticamente no objeto da aplicação definido no Portal Azure (para o Microsoft Graph, aponta `{Application ID URI}` `https://graph.microsoft.com` para). Para APIs web personalizado, `{Application ID URI}` é definido em Expor uma secção **API** no Registo de Aplicação do Portal Azure. |
> | `tokenResponse` | A resposta contém um sinal de acesso para os âmbitos solicitados. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o desenvolvimento de aplicações daemon/consola com o MSAL Node, consulte o tutorial:

> [!div class="nextstepaction"]
> [Aplicação Daemon que chama APIs web](tutorial-v2-nodejs-console.md)