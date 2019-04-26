---
title: Proteger uma API Web com o Azure AD| Microsoft Docs
description: Aprenda a compilar uma API Web Node.js REST que se integra no Azure AD para autenticação.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f72cbd719cea585144be3757f0791a74bde452ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299197"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Início rápido: Proteger uma Web API com o Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Neste início rápido, vai aprender a proteger um ponto final [Restify](http://restify.com/) de API com o [Passport](http://passportjs.org/), com o módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) para processar a comunicação com o Azure Active Directory (Azure AD).

O âmbito deste início rápido abrange as preocupações relativas à proteção dos pontos finais da API. As preocupações de iniciar sessão e manter os tokens de autenticação não são implementadas aqui e são da responsabilidade de uma aplicação cliente. Para obter detalhes que envolvem uma implementação de cliente, reveja [Início de sessão e fim de sessão de uma aplicação Web Node.js com o Azure AD](quickstart-v1-openid-connect-code.md).

O exemplo de código completo associado a este artigo está disponível no [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos.

### <a name="create-the-sample-project"></a>Criar o projeto de exemplo

A aplicação de servidor requer algumas dependências de pacote para suportar o Restify e o Passport, bem como informações de conta que são transmitidas para o Azure AD.

Para começar, adicione o seguinte código a um ficheiro com o nome `package.json`:

```Shell
{
  "name": "active-directory-webapi-nodejs",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "4.0.0",
    "restify": "7.7.0"
  }
}
```

Assim que `package.json` estiver criado, execute `npm install` na sua linha de comandos para instalar as dependências de pacote.

#### <a name="configure-the-project-to-use-active-directory"></a>Configurar o projeto para utilizar o Active Directory

Para começar a configurar a aplicação, existem alguns valores específicos de conta, que pode obter a partir da CLI do Azure. A forma mais fácil de começar a utilizar a CLI é utilizar o Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Introduza o seguinte comando no cloud shell:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Os [argumentos](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) do comando `create` incluem:

| Argumento  | Descrição |
|---------|---------|
|`display-name` | Nome amigável do registo |
|`homepage` | URL no qual os utilizadores podem iniciar sessão e utilizar a aplicação |
|`identifier-uris` | URIs exclusivos separados por espaços, que o Azure AD pode utilizar para esta aplicação |

Antes de poder ligar ao Azure Active Directory, precisará das seguintes informações:

| Name  | Descrição | Nome da Variável no Ficheiro de Configuração |
| ------------- | ------------- | ------------- |
| Nome do Inquilino  | O [nome do inquilino](quickstart-create-new-tenant.md) que pretende utilizar para autenticação | `tenantName`  |
| ID de Cliente  | O ID de Cliente é o termo de OAuth utilizado para o _ID da Aplicação_ do AAD. |  `clientID`  |

Na resposta do registo no Azure Cloud Shell, copie o valor `appId` e crie um novo ficheiro com o nome `config.js`. Em seguida, adicione o código seguinte e substitua os valores pelos tokens entre parênteses:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```

Para obter mais informações sobre as definições de configuração individuais, reveja a documentação do módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

### <a name="implement-the-server"></a>Implementar o servidor

O [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) módulo apresenta duas estratégias de autenticação: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) e [portador](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) estratégias. O servidor implementado neste artigo utiliza a estratégia de portador para proteger o ponto final da API.

### <a name="step-1-import-dependencies"></a>Passo 1: Dependências de importação

Crie um novo ficheiro com o nome `app.js` e cole o seguinte texto:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require ('restify').plugins
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

Nesta secção do código:

- O `restify` e módulos de plug-ins são referenciados para configurar um servidor do Restify.
- Os módulos `passport` e `passport-azure-ad` são responsáveis por comunicar com o Azure AD.
- A variável `config` é inicializada com os valores do ficheiro `config.js` criado no passo anterior.
- É criada uma matriz para o `authenticatedUserTokens` armazenar os tokens de utilizador à medida que são transmitidos para pontos finais seguros.
- O `serverPort` é definido a partir da porta do ambiente de processo ou do ficheiro de configuração.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Passo 2: Criar uma instância de uma estratégia de autenticação

Ao proteger um ponto final, tem de fornecer uma estratégia responsável por determinar se o pedido atual é ou não proveniente de um utilizador autenticado. Aqui a variável `authenticatonStrategy` é uma instância da classe `passport-azure-ad` de `BearerStrategy`. Adicione o seguinte código após as declarações `require`.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```

Esta implementação utiliza o registo automático, adicionando os tokens de autenticação na matriz do `authenticatedUserTokens`, se ainda não existirem.

Depois de criar uma nova instância da estratégia, deve passá-la ao Passport através do método `use`. Adicione o seguinte código a `app.js` para utilizar a estratégia no Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Passo 3: Configuração do servidor

Com a estratégia de autenticação definida, pode agora configurar o servidor do Restify com algumas configurações básicas e defini-lo para utilizar o Passport para segurança.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Este servidor é inicializado e configurado para analisar cabeçalhos de autorização e, em seguida, é definido para utilizar o Passport.

### <a name="step-4-define-routes"></a>Passo 4: Definir rotas

Agora pode definir rotas e decidir quais pretende proteger com o Azure AD. Esse projeto inclui duas rotas nas quais o nível de raiz é aberto e a rota `/api` é definida para exigir a autenticação.

Em `app.js`, adicione o seguinte código para a rota de nível de raiz:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

A rota de raiz permite todos os pedidos através da rota e devolve uma mensagem que inclui um comando para testar a rota `/api`. Por outro lado, a rota `/api` permanece bloqueada com [`passport.authenticate`](http://passportjs.org/docs/authenticate). Adicione o seguinte código após a rota de raiz.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Esta configuração permite apenas pedidos autenticados, que incluem um acesso de token de portador à `/api`. A opção de `session: false` é utilizada para desativar as sessões de pedirem que um token seja passado com cada pedido à API.

Por fim, o servidor está definido para escutar na porta configurada, ao chamar o método `listen`.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Executar o exemplo

Agora que o servidor está implementado, pode iniciar o servidor ao abrir uma linha de comandos e introduzir:

```shell
npm start
```

Com o servidor em execução, pode submeter um pedido ao servidor para testar os resultados. Para demonstrar a resposta da rota de raiz, abra uma shell do bash e introduza o seguinte código:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Se tiver configurado o servidor corretamente, a resposta deve ter um aspeto semelhante a:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Em seguida, pode testar a rota que requer autenticação ao introduzir o seguinte comando na sua shell do bash:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Se tiver configurado o servidor corretamente, o servidor deverá responder com o estado `Unauthorized`.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Agora que criou uma API segura, pode implementar um cliente que é capaz de passar os tokens de autenticação à API.

## <a name="next-steps"></a>Passos Seguintes

* Tem de implementar um equivalente de cliente para ligar ao servidor que processa o início de sessão, ao terminar a sessão e gerir os tokens. Para obter exemplos baseados em código, consulte as aplicações cliente em [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) e [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android).
* Para obter um tutorial passo-a-passo, veja [Início de sessão e fim de sessão de uma aplicação Web Node.js com o Azure AD](quickstart-v1-openid-connect-code.md).
