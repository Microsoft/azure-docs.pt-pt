---
title: Saiba como integrar com o Azure AD B2C com a biblioteca de autenticação da Microsoft (MSAL)
description: Biblioteca de autenticação da Microsoft (MSAL) permite que os desenvolvedores de aplicativos integrar com o Azure AD B2C e adquirir tokens para chamar as APIs da Web protegida. Essas APIs da Web pode ser o Microsoft Graph, outras APIS da Microsoft, as APIs da Web de terceiros ou seu próprio Web API.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546025"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integrar a biblioteca de autenticação da Microsoft (MSAL) com o Azure Active Directory B2C

Biblioteca de autenticação da Microsoft (MSAL) permite que os desenvolvedores de aplicativos autenticar os utilizadores com identidades sociais e locais usando [do Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). O Azure Active Directory (Azure AD) B2C é um serviço de gestão de identidades que lhe permite personalizar e controlar a forma como os clientes se inscrevem, iniciam sessão e gerem os respetivos perfis quando utilizam as suas aplicações.

O Azure Active Directory (Azure AD) B2C também permite-lhe sua marca e personalizar a interface de utilizador (IU) das suas aplicações para fornecer uma experiência totalmente integrada para o seu cliente.

Este tutorial demonstra como utilizar o Microsoft Authentication Library (MSAL) para integrar com o Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver criado o seu [inquilino do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crie uma agora. Pode utilizar um inquilino existente do Azure AD B2C. 

## <a name="javascript"></a>Javascript

Passos a seguir demonstra como uma aplicação de página única pode utilizar o Azure AD B2C para inscrição e início de sessão do utilizador e chamar uma API web protegida.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação

Implementar a autenticação, primeiro terá de registar a sua aplicação. Para registar a sua aplicação, siga [registar a sua aplicação](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) para obter passos detalhados.

### <a name="steps-2-download-applications"></a>Etapas de 2: Baixar aplicativos

Transferir um ficheiro zip ou clonar o exemplo do GitHub.
>clone de Git https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Passos 3: Autenticação

1. Abra o ficheiro Index. HTML no exemplo.

2. Configure o exemplo com o ID da aplicação e a chave que registou anteriormente ao registar a aplicação. Altere as seguintes linhas de código, substituindo os valores com os nomes do seu diretório e APIs:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

O nome da [fluxo de utilizador](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) utilizado neste tutorial é B2C_1_signupsignin1. Se estiver a utilizar um nome de fluxo de utilizador diferente, utilize o seu nome de fluxo de utilizador no valor de autoridade.


### <a name="configure-application-to-use-b2clogincom"></a>Configurar aplicações para utilizar `b2clogin.com`

Pode usar `b2clogin.com` em vez de `login.microsoftonline.com` como um redirecionamento da url, quando configurar um fornecedor de identidade para a inscrição e início de sessão na sua aplicação do Azure Active Directory (Azure AD) B2C.

**`b2clogin.com`** i.e 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` é utilizado para o seguinte:

- Espaço consumido no cabeçalho de cookie por serviços da Microsoft é reduzido.
- Suas URLs deixarão de incluir uma referência à Microsoft. Por exemplo, a aplicação do Azure AD B2C provavelmente refere-se ao login.microsoftonline.com


 Para utilizar "b2clogin.com", terá de atualizar a configuração da sua aplicação.  

1. Atualização ValidateAuthority: definir os **validateAuthority** propriedade `false`. Quando **validateAuthority** é definido como FALSO, redirecionamentos têm permissão para b2clogin.com.

O exemplo seguinte mostra como pode definir a propriedade:
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
> A aplicação do Azure AD B2C refere-se provavelmente a login.microsoftonline.com em vários locais, como suas referências de fluxo de utilizador e os pontos finais de token. Certifique-se de que o ponto final de autorização, o ponto final do token e o emissor foram atualizados para utilizar o seu inquilino name.b2clogin.com.

Siga este [exemplo de MSAL JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) sobre como utilizar a pré-visualização de biblioteca de autenticação do Microsoft para JavaScript (msal) para obter um token de acesso e chamar uma API protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre:

- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalização da interface de utilizador](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)