---
title: Adicionar o OIDC entrar em um aplicativo Web node. js-plataforma Microsoft Identity | Azure
description: Saiba como implementar a autenticação em um aplicativo Web node. js usando o OpenID Connect.
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
ms.openlocfilehash: 4aa0cce83f9adc8c648656899ec6dc12d498e26b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160453"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Início rápido: Adicionar entrada usando o OpenID Connect a um aplicativo Web node. js

Neste guia de início rápido, você aprenderá a configurar a autenticação do OpenID Connect em um aplicativo Web criado usando o Node. js com o Express. O exemplo foi projetado para ser executado em qualquer plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, será necessário:

* Instale node.js de http://nodejs.org/

* Ou uma [conta Microsoft](https://www.outlook.com) ou programa de [desenvolvedores do Office 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registar a sua aplicação 
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD:
    - Selecione o seu perfil a partir do menu no canto superior direito da página e, em seguida, **mude de diretório**.
    - Altere a sessão para o locatário do Azure AD no qual você deseja criar seu aplicativo.

1. Navegue para as inscrições do [Azure Ative Directory > App](https://go.microsoft.com/fwlink/?linkid=2083908) para registar a sua aplicação.

1. Selecione **Novo registo.**

1. Quando aparecer uma página de **inscrição,** introduza as informações de registo da sua aplicação:
    - Na secção **Nome,** introduza um nome significativo que será exibido aos utilizadores da aplicação. Por exemplo: myWebApp
    - Na secção de tipos de **conta suportada,** selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .

    Se houver mais de um URIs redirecionado, terá de adicioná-las a partir do separador **Autenticação** mais tarde, depois de a aplicação ter sido criada com sucesso.

1. Selecione **Register** para criar a aplicação.

1. Na página **de Visão Geral** da aplicação, encontre o valor de ID da **Aplicação (cliente)** e grave-o para mais tarde. Você precisará desse valor para configurar o aplicativo posteriormente neste projeto.

1. Na lista de páginas da aplicação, selecione **Autenticação**.
    - Na secção **Redirecionamento de URIs,** selecione **Web** na caixa de combinação e introduza o seguinte redirecionamento URI: `http://localhost:3000/auth/openid/return`
    - Na secção **de definições Avançadas,** coloque o URL de **logout** para `http://localhost:3000`.
    - Na secção **de definições avançadas > Implicit grant,** verifique as fichas de **identificação,** uma vez que esta amostra requer que o fluxo de [subvenção implícita](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) seja ativado para iniciar sessão no utilizador.

1. Selecione **Guardar**.

1. A partir da página **Certificados e segredos,** na secção **de segredos** do Cliente, escolha **novo segredo de cliente.**
    - Insira uma descrição de chave (por exemplo, segredo do aplicativo).
    - Selecione uma duração chave de **qualquer um em 1 ano, em 2 anos,** ou nunca **expire**.
    - Quando clicar no botão **Adicionar,** o valor da chave será visualizado. Copie o valor da chave e salve-o em um local seguro.

    Você precisará dessa chave posteriormente para configurar o aplicativo. Este valor-chave não voltará a ser apresentado, nem será recuperável por qualquer outro meio, pelo que o grave assim que for visível a partir do portal Azure.

## <a name="download-the-sample-application-and-modules"></a>Baixar o aplicativo de exemplo e os módulos

Em seguida, clone o repositório de exemplo e instale os módulos NPM.

No Shell ou na linha de comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

ou

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

No diretório raiz do projeto, execute o comando:

`$ npm install`  

## <a name="configure-the-application"></a>Configurar o aplicativo

Forneça os parâmetros em `exports.creds` em config.js conforme instruído.

* Atualização `<tenant_name>` em `exports.identityMetadata` com o nome de inquilino da AD Azure do formato \*.onmicrosoft.com.
* Atualização `exports.clientID` com o ID de aplicação anotado a partir do registo da aplicação.
* Atualizar `exports.clientSecret` com o segredo de aplicação anotado a partir do registo da aplicação.
* Atualização `exports.redirectUrl` com o Redirect URI anotado a partir do registo da aplicação.

**Configuração opcional para aplicações de produção:**

* Atualize `exports.destroySessionUrl` em config.js, se quiser utilizar um `post_logout_redirect_uri`diferente .

* Coloque `exports.useMongoDBSessionStore` em config.js para verdade, se quiser utilizar [mongoDB](https://www.mongodb.com) ou outras [lojas de sessão compatíveis](https://github.com/expressjs/session#compatible-session-stores).
A loja de sessão predefinida nesta amostra é `express-session`. O repositório de sessão padrão não é adequado para produção.

* Atualização `exports.databaseUri`, se quiser utilizar a loja de sessões mongoDB e uma base de dados diferente URI.

* Atualizar `exports.mongoDBSessionMaxAge`. Aqui você pode especificar por quanto tempo deseja manter uma sessão no mongoDB. A unidade é a segunda (s).

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Inicie o serviço mongoDB. Se estiver a utilizar a loja de sessões mongoDB nesta aplicação, tem de [instalar o mongoDB](http://www.mongodb.org/) e iniciar o serviço primeiro. Se você estiver usando o repositório de sessão padrão, poderá ignorar esta etapa.

Execute o aplicativo usando o comando a seguir na linha de comando.

```
$ node app.js
```

**A saída do servidor é difícil** de entender? Usamos `bunyan` para fazer login nesta amostra. O console não fará muito sentido para você, a menos que você também instale o Bunyan e execute o servidor, como acima, mas redirecione-o por meio do binário Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Acabou-se!

Terá um servidor a funcionar com sucesso na `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário do aplicativo Web compatível com a plataforma de identidade da Microsoft:
> [!div class="nextstepaction"]
> [Aplicação web que assina no cenário dos utilizadores](scenario-web-app-sign-user-overview.md)
