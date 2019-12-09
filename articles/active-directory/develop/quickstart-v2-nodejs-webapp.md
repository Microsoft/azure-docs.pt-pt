---
title: Guia de início rápido do aplicativo Web OIDC node. js da plataforma Microsoft Identity | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8a73193a4e43ad4c91e23534898d003a310dff5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920604"
---
# <a name="quickstart-add-sign-in-using-oidc-to-a-nodejs-web-app"></a>Início rápido: Adicionar entrada usando OIDC a um aplicativo Web node. js

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste guia de início rápido, você aprenderá a configurar a autenticação do OpenID Connect em um aplicativo Web criado usando o Node. js com o Express. O exemplo foi projetado para ser executado em qualquer plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, será necessário:

* Instalar o Node. js do http://nodejs.org/

* Um [conta Microsoft](https://www.outlook.com) ou um [programa para desenvolvedores do Office 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registar a sua aplicação 
1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD:
    - Selecione seu perfil no menu no canto superior direito da página e, em seguida, alterne o **diretório**.
    - Altere a sessão para o locatário do Azure AD no qual você deseja criar seu aplicativo.

1. Navegue até [Azure Active Directory > registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar seu aplicativo.

1. Selecione **novo registro.**

1. Quando a página **registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
    - Na seção **nome** , insira um nome significativo que será exibido aos usuários do aplicativo. Por exemplo: myWebApp
    - Na seção **tipos de conta com suporte** , selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .

    Se houver mais de um URI de redirecionamento, você precisará adicioná-los na guia **autenticação** posteriormente depois que o aplicativo tiver sido criado com êxito.

1. Selecione **registrar** para criar o aplicativo.

1. Na página **visão geral** do aplicativo, localize o valor da **ID do aplicativo (cliente)** e registre-o para mais tarde. Você precisará desse valor para configurar o aplicativo posteriormente neste projeto.

1. Na lista de páginas da aplicação, selecione **Autenticação**.
    - Na seção **URIs de redirecionamento** , selecione **Web** na caixa de combinação e insira o seguinte URI de redirecionamento: `http://localhost:3000/auth/openid/return`
    - Na seção **Configurações avançadas** , defina **URL de logoff** para `http://localhost:3000`.
    - Na seção **Configurações avançadas > concessão implícita** , verifique os **tokens de ID** , pois este exemplo requer que o [fluxo de concessão implícita](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) esteja habilitado para entrar no usuário.

1. Selecione **Guardar**.

1. Na página **certificados & segredos** , na seção **segredos do cliente** , escolha **novo segredo do cliente**.
    - Insira uma descrição de chave (por exemplo, segredo do aplicativo).
    - Selecione uma duração de chave de **em 1 ano, em 2 anos** ou **nunca expirar**.
    - Quando você clicar no botão **Adicionar** , o valor da chave será exibido. Copie o valor da chave e salve-o em um local seguro.

    Você precisará dessa chave posteriormente para configurar o aplicativo. Esse valor de chave não será exibido novamente, nem poderá ser recuperado por outros meios, portanto, registre-o assim que estiver visível na portal do Azure.

## <a name="download-the-sample-application-and-modules"></a>Baixar o aplicativo de exemplo e os módulos

Em seguida, clone o repositório de exemplo e instale os módulos NPM.

No Shell ou na linha de comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

ou

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

No diretório raiz do projeto, execute o comando:

`$ npm install`  

## <a name="configure-the-application"></a>Configurar o aplicativo

Forneça os parâmetros em `exports.creds` em config. js conforme instruído.

* Atualize `<tenant_name>` no `exports.identityMetadata` com o nome do locatário do Azure AD do formato \*. onmicrosoft.com.
* Atualize `exports.clientID` com a ID do aplicativo anotada no registro do aplicativo.
* Atualize `exports.clientSecret` com o segredo do aplicativo anotado no registro do aplicativo.
* Atualize `exports.redirectUrl` com o URI de redirecionamento anotado do registro do aplicativo.

**Configuração opcional para aplicativos de produção:**

* Atualize `exports.destroySessionUrl` em config. js, se você quiser usar um `post_logout_redirect_uri`diferente.

* Defina `exports.useMongoDBSessionStore` em config. js como true, se você quiser usar o [mongoDB](https://www.mongodb.com) ou outros [armazenamentos de sessão compatíveis](https://github.com/expressjs/session#compatible-session-stores).
O repositório de sessão padrão neste exemplo é `express-session`. O repositório de sessão padrão não é adequado para produção.

* Atualize `exports.databaseUri`, se você quiser usar o repositório de sessão do mongoDB e um URI de banco de dados diferente.

* Atualização `exports.mongoDBSessionMaxAge`. Aqui você pode especificar por quanto tempo deseja manter uma sessão no mongoDB. A unidade é a segunda (s).

## <a name="build-and-run-the-application"></a>Compilar e executar a aplicação

Inicie o serviço mongoDB. Se você estiver usando o repositório de sessão do mongoDB neste aplicativo, precisará [instalar o mongoDB](http://www.mongodb.org/) e iniciar o serviço primeiro. Se você estiver usando o repositório de sessão padrão, poderá ignorar esta etapa.

Execute o aplicativo usando o comando a seguir na linha de comando.

```
$ node app.js
```

**A saída do servidor é difícil de entender?:** Usamos `bunyan` para registro em log neste exemplo. O console não fará muito sentido para você, a menos que você também instale o Bunyan e execute o servidor, como acima, mas redirecione-o por meio do binário Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Terminou!

Você terá um servidor em execução com êxito no `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o cenário do aplicativo Web compatível com a plataforma de identidade da Microsoft:
> [!div class="nextstepaction"]
> [Aplicativo Web que entra no cenário de usuários](scenario-web-app-sign-user-overview.md)
