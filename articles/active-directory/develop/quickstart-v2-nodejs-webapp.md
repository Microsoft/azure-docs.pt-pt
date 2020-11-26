---
title: 'Quickstart: Adicione o início de singing do utilizador a uma aplicação web Node.js [ Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste arranque rápido, aprende-se a implementar a autenticação numa aplicação web Node.js utilizando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: 643305057490cc550a5a8e39a892297b000cbc8e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169414"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Quickstart: Adicione o sinal na utilização do OpenID Connect a uma aplicação web Node.js

Neste arranque rápido, descarrega e execute uma amostra de código que demonstra como configurar a autenticação OpenID Connect numa aplicação web construída com Node.js com o Express. A amostra foi concebida para funcionar em qualquer plataforma.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js. ](https://nodejs.org/en/download/)

## <a name="register-your-application"></a>Registar a aplicação
1. Inscreva-se no [portal Azure](https://portal.azure.com/) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. Se a sua conta estiver presente em mais de um inquilino da AZure AD:
    - Selecione o seu perfil a partir do menu no canto superior direito da página e, em seguida, altere o **diretório**.
    - Mude a sua sessão para o inquilino Azure AD onde pretende criar a sua candidatura.

1. Navegue para [o Azure Ative Directory > registos da App](https://go.microsoft.com/fwlink/?linkid=2083908) para registar a sua aplicação.

1. Selecione **Novo registo.**

1. Quando aparecer a página **de inscrição** do Registo, insira as informações de registo da sua aplicação:
    - Na secção **Nome,** insira um nome significativo que será apresentado aos utilizadores da aplicação. Por exemplo: MyWebApp
    - Na secção **tipos de contas suportadas,** selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.

    Se existirem mais de um REdireccionamento de URIs, terá de os adicionar no **separador Autenticação** mais tarde, depois de a aplicação ter sido criada com sucesso.

1. Selecione **Registar-se** para criar a aplicação.

1. Na **página** geral da aplicação, encontre o valor de **ID da Aplicação (cliente)** e grave-o para mais tarde. Você precisará deste valor para configurar a aplicação mais tarde neste projeto.

1. Na lista de páginas da aplicação, selecione **Autenticação**.
    - Na secção **Redirecionar URIs,** selecione **Web** na caixa de combinação e introduza o seguinte redireccionamento URI: `http://localhost:3000/auth/openid/return`
    - Na secção **definições Avançadas,** defina **o URL de logout** para `https://localhost:3000` .
    - Nas **definições avançadas > secção de subvenção implícita,** verifique **os tokens de identificação,** uma vez que esta amostra requer que o fluxo de [subvenção implícito](./v2-oauth2-implicit-grant-flow.md) seja ativado para iniciar a sposição do utilizador.

1. Selecione **Guardar**.

1. A partir da página **Certificados & segredos,** na secção segredos do **Cliente,** escolha **novo segredo de cliente.**
    - Introduza uma descrição chave (por exemplo, o segredo da aplicação).
    - Selecione uma duração chave de qualquer **um em 1 ano, em 2 anos,** ou **nunca expira**.
    - Quando clicar no botão **Adicionar,** o valor da chave será exibido. Copie o valor da chave e guarde-o num local seguro.

    Mais tarde vai precisar desta chave para configurar a aplicação. Este valor chave não será exibido novamente, nem recuperável por qualquer outro meio, por isso grave-o assim que for visível a partir do portal Azure.

## <a name="download-the-sample-application-and-modules"></a>Descarregue a aplicação da amostra e os módulos

Em seguida, clone a amostra repo e instale os módulos NPM.

Da sua concha ou linha de comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

ou

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

A partir do diretório de raiz do projeto, executar o comando:

`$ npm install`

## <a name="configure-the-application"></a>Configurar a aplicação

Forneça os parâmetros `exports.creds` em config.js conforme instruído.

* Atualize `<tenant_name>` com o nome do inquilino `exports.identityMetadata` Azure AD do formato \* .onmicrosoft.com.
* Atualização `exports.clientID` com o ID da aplicação anotado a partir do registo da aplicação.
* Atualização `exports.clientSecret` com o segredo da Aplicação anotado a partir do registo da aplicação.
* Atualização `exports.redirectUrl` com o Redirect URI anotado a partir do registo da aplicação.

**Configuração opcional para aplicações de produção:**

* Atualize `exports.destroySessionUrl` em config.js, se quiser utilizar um diferente `post_logout_redirect_uri` .

* Desacito `exports.useMongoDBSessionStore` em config.js verdadeiro, se pretender utilizar [mongoDB](https://www.mongodb.com) ou outras [lojas de sessão compatíveis](https://github.com/expressjs/session#compatible-session-stores).
A loja de sessão predefinido nesta amostra é `express-session` . A loja de sessão predefinido não é adequada para a produção.

* Atualização `exports.databaseUri` , se pretender utilizar a loja de sessão mongoDB e uma base de dados diferente URI.

* Atualização `exports.mongoDBSessionMaxAge` . Aqui pode especificar quanto tempo pretende manter uma sessão no mongoDB. A unidade é a segunda.

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Inicie o serviço mongoDB. Se estiver a utilizar a loja de sessão mongoDB nesta aplicação, tem de [instalar o MongoDB](http://www.mongodb.org/) e iniciar o serviço primeiro. Se estiver a utilizar a loja de sessão predefinida, pode saltar este passo.

Execute a aplicação utilizando o seguinte comando a partir da sua linha de comando.

```
$ node app.js
```

**A saída do servidor é difícil de entender?:** Usamos `bunyan` para fazer login nesta amostra. A consola não fará muito sentido para si, a menos que também instale bunyan e execute o servidor como por cima, mas encaneie-o através do binário bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Acabou-se!

Terá um servidor a funcionar com sucesso `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário de aplicações web que a plataforma de identidade da Microsoft suporta:
> [!div class="nextstepaction"]
> [Aplicação web que assina no cenário dos utilizadores](scenario-web-app-sign-user-overview.md)