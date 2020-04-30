---
title: Adicione o sintetizar o OIDC numa aplicação Web Node.js - plataforma de identidade da Microsoft Azure
description: Aprenda a implementar a autenticação numa aplicação web Node.js utilizando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 1ff92b8a9477800477ebb2d79145ddaa78831f30
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536068"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Quickstart: Adicione o sinal na utilização do OpenID Connect para uma aplicação web Node.js

Neste arranque rápido, aprenderá a configurar a autenticação OpenID Connect numa aplicação web construída com o Node.js com express. A amostra foi concebida para ser executada em qualquer plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará:

* Instalar Nó.js dehttp://nodejs.org/

* Ou uma [conta Microsoft](https://www.outlook.com) ou programa de [desenvolvedores do Office 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registar a sua aplicação
1. Inscreva-se no [portal Azure](https://portal.azure.com/) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. Se a sua conta estiver presente em mais de um inquilino da AD Azure:
    - Selecione o seu perfil a partir do menu no canto superior direito da página e, em seguida, **mude de diretório**.
    - Mude a sua sessão para o inquilino da AD Azure onde pretende criar a sua candidatura.

1. Navegue para [o Diretório Ativo do Azure > registos](https://go.microsoft.com/fwlink/?linkid=2083908) da App para registar a sua aplicação.

1. Selecione **Novo registo.**

1. Quando aparecer uma página de **inscrição,** introduza as informações de registo da sua aplicação:
    - Na secção **Nome,** introduza um nome significativo que será exibido aos utilizadores da aplicação. Por exemplo: MyWebApp
    - Na secção de tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.

    Se houver mais de um URIs redirecionado, terá de adicioná-las a partir do separador **Autenticação** mais tarde, depois de a aplicação ter sido criada com sucesso.

1. Selecione **Register** para criar a aplicação.

1. Na página **de Visão Geral** da aplicação, encontre o valor de ID da **Aplicação (cliente)** e grave-o para mais tarde. Você precisará deste valor para configurar a aplicação mais tarde neste projeto.

1. Na lista de páginas da aplicação, selecione **Autenticação**.
    - Na secção **Redirecionamento de URIs,** selecione **Web** na caixa de combinação e introduza o seguinte redirecionamento URI:`http://localhost:3000/auth/openid/return`
    - Na secção **de definições avançadas,** coloque **o URL de logout** para `http://localhost:3000`.
    - Nas **definições avançadas > secção de subvenção implícita,** verifique os **tokens de ID,** uma vez que esta amostra requer que o fluxo de [subvenção implícita](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) seja ativado para iniciar sessão no utilizador.

1. Selecione **Guardar**.

1. A partir da página **de Certificados & segredos,** na secção **de segredos do Cliente,** escolha **o novo segredo do cliente.**
    - Introduza uma descrição chave (por exemplo, segredo de aplicação).
    - Selecione uma duração chave de **qualquer um em 1 ano, em 2 anos,** ou nunca **expire**.
    - Quando clicar no botão **Adicionar,** o valor da chave será visualizado. Copie o valor-chave e guarde-o num local seguro.

    Mais tarde, vai precisar desta chave para configurar a aplicação. Este valor-chave não voltará a ser apresentado, nem será recuperável por qualquer outro meio, pelo que o grave assim que for visível a partir do portal Azure.

## <a name="download-the-sample-application-and-modules"></a>Descarregue a aplicação da amostra e os módulos

Em seguida, clone o repo da amostra e instale os módulos NPM.

Da sua concha ou linha de comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

ou

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

A partir do diretório raiz do projeto, executar o comando:

`$ npm install`

## <a name="configure-the-application"></a>Configurar a aplicação

Forneça os parâmetros `exports.creds` em config.js conforme instruído.

* `<tenant_name>` Atualização `exports.identityMetadata` com o nome de inquilino \*da AD Azure do formato .onmicrosoft.com.
* Atualização `exports.clientID` com o ID de aplicação anotado a partir do registo da aplicação.
* Atualizar `exports.clientSecret` com o segredo de aplicação anotado a partir do registo da aplicação.
* Atualização `exports.redirectUrl` com o Redirect URI anotado a partir do registo da aplicação.

**Configuração opcional para aplicações de produção:**

* Atualização `exports.destroySessionUrl` em config.js, se quiser `post_logout_redirect_uri`usar um diferente .

* Coloque `exports.useMongoDBSessionStore` em config.js como verdadeiro, se quiser utilizar [mongoDB](https://www.mongodb.com) ou outras [lojas de sessão compatíveis](https://github.com/expressjs/session#compatible-session-stores).
A loja de sessão `express-session`predefinida nesta amostra é . A loja de sessão padrão não é adequada para produção.

* Atualização `exports.databaseUri`, se quiser utilizar a loja de sessões mongoDB e uma base de dados diferente URI.

* Atualização `exports.mongoDBSessionMaxAge`. Aqui pode especificar quanto tempo pretende manter uma sessão no mongoDB. A unidade é segunda.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Inicie o serviço mongoDB. Se estiver a utilizar a loja de sessões mongoDB nesta aplicação, tem de [instalar o mongoDB](http://www.mongodb.org/) e iniciar o serviço primeiro. Se estiver a utilizar a loja de sessão predefinida, pode saltar este passo.

Execute a aplicação utilizando o seguinte comando da sua linha de comando.

```
$ node app.js
```

**A saída do servidor é difícil** de entender? Usamos `bunyan` para fazer login nesta amostra. A consola não fará muito sentido para si a menos que também instale bunyan e execute o servidor como acima, mas encaneie-o através do binário bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Acabou-se!

Terá um servidor a funcionar `http://localhost:3000`com sucesso.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário da aplicação web que a plataforma de identidade da Microsoft suporta:
> [!div class="nextstepaction"]
> [Aplicação web que assina no cenário dos utilizadores](scenario-web-app-sign-user-overview.md)
