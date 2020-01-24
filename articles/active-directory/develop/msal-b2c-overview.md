---
title: Utilize mSAL com diretório ativo Azure B2CLearn  Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) permite que as aplicações interoperassem com o Azure AD B2C e adquiram fichas para ligar para APIs web seguros. Estas APIs web podem ser Microsoft Graph, outras APIs da Microsoft, APIs web de outros, ou a sua própria Web API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696457"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Utilize a Biblioteca de Autenticação da Microsoft para interoperar com o Diretório Ativo Azure B2C

A Microsoft Authentication Library (MSAL) permite que os desenvolvedores de aplicações autentiquem os utilizadores com identidades sociais e locais utilizando o [Azure Ative Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C é um serviço de gestão de identidade. Ao utilizá-lo, pode personalizar e controlar a forma como os clientes se inscrevem, fazem o insessão e gerem os seus perfis quando utilizam as suas aplicações.

O Azure AD B2C também lhe permite marcar e personalizar o UI das suas aplicações para proporcionar uma experiência perfeita aos seus clientes.

Este tutorial demonstra como usar o MSAL para interoperar com o Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou o seu próprio [inquilino Azure AD B2C, crie](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)agora um. Você também pode usar um inquilino Azure AD B2C existente.

## <a name="javascript"></a>Javascript

Os seguintes passos demonstram como uma aplicação de uma página única pode usar O Azure AD B2C para se inscrever, iniciar sessão e chamar uma API web protegida.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

Para implementar a autenticação, primeiro é necessário registar a sua candidatura. Consulte [O registo do seu pedido](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) para etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Descarregue a aplicação da amostra

Descarregue a amostra como um ficheiro zip, ou clone-a a partir do GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Configurar a autenticação

1. Abra o ficheiro **index.html** na amostra.

1. Configure a amostra com o ID do cliente e a chave que gravou anteriormente ao registar a sua aplicação. Altere as seguintes linhas de código substituindo os valores pelos nomes do seu diretório e APIs:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

O nome do fluxo do [utilizador](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) neste tutorial é **B2C_1_signupsignin1**. Se estiver a usar um nome de fluxo de utilizador diferente, detete o valor da **autoridade** para esse nome.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Passo 4: Configure a sua aplicação para utilizar `b2clogin.com`

Pode utilizar `b2clogin.com` no lugar de `login.microsoftonline.com` como URL redirecionado. Fá-lo no seu pedido Azure AD B2C quando configura um fornecedor de identidade para inscrição e inscrição.

A utilização de `b2clogin.com` no contexto de `https://your-tenant-name.b2clogin.com/your-tenant-guid` tem os seguintes efeitos:

- Os serviços da Microsoft consomem menos espaço no cabeçalho dos cookies.
- Os seus URLs já não incluem uma referência à Microsoft. Por exemplo, a sua aplicação Azure AD AD B2C provavelmente refere-se a `login.microsoftonline.com`.

 Para utilizar `b2clogin.com`, precisa de atualizar a configuração da sua aplicação.  

- Desloque a propriedade **validada DaAutoridade** para `false`, de modo a que possam ocorrer redirecionamentos usando `b2clogin.com`.

O exemplo que se segue mostra como pode definir a propriedade:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> A sua aplicação Azure AD B2C provavelmente refere-se a `login.microsoftonline.com` em vários locais, tais como referências de fluxo de utilizador e pontos finais simbólicos. Certifique-se de que o seu ponto final de autorização, ponto final simbólico e emitente foram atualizados para utilizar `your-tenant-name.b2clogin.com`.

Siga [esta amostra Do MSAL JavaScript](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) sobre como utilizar a pré-visualização MSAL para JavaScript (MSAL.js). A amostra obtém um sinal de acesso e chama uma API segura por Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalização da interface de utilizador](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)