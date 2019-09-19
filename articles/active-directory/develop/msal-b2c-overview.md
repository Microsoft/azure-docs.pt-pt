---
title: Saiba como os aplicativos podem interoperar com Azure AD B2C usando a biblioteca de autenticação da Microsoft
description: A MSAL (biblioteca de autenticação da Microsoft) permite que os aplicativos interoperem com Azure AD B2C e adquirem tokens para chamar APIs da Web protegidas. Essas APIs da Web podem ser Microsoft Graph, outras APIs da Microsoft, APIs da Web de outras ou sua própria API Web.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eea0fd03b1df49e912a867b0c667ff0fd28c08a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097624"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Usar a biblioteca de autenticação da Microsoft para interoperar com o Azure Active Directory B2C

A MSAL (biblioteca de autenticação da Microsoft) permite que os desenvolvedores de aplicativos autentiquem usuários com identidades sociais e locais usando o [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C é um serviço de gerenciamento de identidade. Ao usá-lo, você pode personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos.

O Azure AD B2C também permite que você marque e personalize a interface do usuário de seus aplicativos para fornecer uma experiência direta aos seus clientes.

Este tutorial demonstra como usar o MSAL para interoperar com Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário de Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crie um agora. Você também pode usar um locatário de Azure AD B2C existente.

## <a name="javascript"></a>JavaScript

As etapas a seguir demonstram como um aplicativo de página única pode usar Azure AD B2C para se inscrever, entrar e chamar uma API Web protegida.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação

Para implementar a autenticação, primeiro você precisa registrar seu aplicativo. Consulte [registrar seu aplicativo](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) para obter etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Transferir a aplicação de exemplo

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Configurar a autenticação

1. Abra o arquivo **index. html** no exemplo.

1. Configure o exemplo com a ID do cliente e a chave que você registrou anteriormente ao registrar seu aplicativo. Altere as seguintes linhas de código substituindo os valores pelos nomes de seu diretório e APIs:

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

O nome do [fluxo do usuário](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) neste tutorial é **B2C_1_signupsignin1**. Se você estiver usando um nome de fluxo de usuário diferente, defina o valor de **autoridade** com esse nome.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Passo 4: Configurar seu aplicativo para usar`b2clogin.com`

Você pode usar `b2clogin.com` o no lugar `login.microsoftonline.com` de como uma URL de redirecionamento. Isso é feito no aplicativo Azure AD B2C quando você configura um provedor de identidade para inscrição e entrada.

O uso `b2clogin.com` do no contexto do `https://your-tenant-name.b2clogin.com/your-tenant-guid` tem os seguintes efeitos:

- Os serviços da Microsoft consomem menos espaço no cabeçalho do cookie.
- Suas URLs não incluem mais uma referência à Microsoft. Por exemplo, seu aplicativo Azure AD B2C provavelmente se refere `login.microsoftonline.com`.

 Para usar `b2clogin.com`o, você precisa atualizar a configuração do seu aplicativo.  

- Defina a propriedade **validateAuthority** como `false`, para que os redirecionamentos `b2clogin.com` usando o possam ocorrer.

O exemplo a seguir mostra como você pode definir a propriedade:

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
> Seu aplicativo Azure ad B2C provavelmente se refere `login.microsoftonline.com` a vários lugares, como referências de fluxo de usuário e pontos de extremidade de token. Verifique se o ponto de extremidade de autorização, o ponto de extremidade do token e o emissor `your-tenant-name.b2clogin.com`foram atualizados para uso.

Siga [Este exemplo de JavaScript do MSAL](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) sobre como usar a visualização do MSAL para JavaScript (MSAL. js). O exemplo obtém um token de acesso e chama uma API protegida por Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalização da interface do usuário](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)