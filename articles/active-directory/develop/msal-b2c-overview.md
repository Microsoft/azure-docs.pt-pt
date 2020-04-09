---
title: Utilize mSAL com diretório ativo Azure B2CLearn [ Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library for JavaScript (MSAL.js) permite que as aplicações funcionem com o Azure AD B2C e adquiram fichas para ligar para APIs web seguros. Estas APIs web podem ser Microsoft Graph, outras APIs da Microsoft, APIs web de outros, ou a sua própria Web API.
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
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875967"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Utilize a Biblioteca de Autenticação da Microsoft para o JavaScript trabalhar com o Diretório Ativo Azure B2C

A [Microsoft Authentication Library for JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) permite aos desenvolvedores do JavaScript autenticar utilizadores com identidades sociais e locais utilizando o [Diretório Ativo Azure B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Ao utilizar o Azure AD B2C como um serviço de gestão de identidade, pode personalizar e controlar a forma como os clientes se inscrevem, se inscrevem e gerem os seus perfis quando utilizam as suas aplicações.

O Azure AD B2C também lhe permite marcar e personalizar o UI das suas aplicações durante o processo de autenticação de forma a proporcionar uma experiência perfeita aos seus clientes.

Este artigo demonstra como usar mSAL.js para trabalhar com o Azure AD B2C e resume pontos-chave que deve estar ciente. Para uma discussão completa e tutorial, consulte a [Documentação Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou o seu próprio [inquilino Azure AD B2C,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)comece por criar um agora (também pode usar um inquilino Azure AD B2C existente se já tiver um).

Esta demonstração contém duas partes:

- como proteger uma API web.
- como registar uma aplicação de uma única página para autenticar e chamar *a essa* Web API.

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> Neste momento, mSAL.js para Nó ainda está em desenvolvimento (ver o [roteiro).](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) Entretanto, sugerimos a utilização de [um anúncio de passaporte-azure,](https://github.com/AzureAD/passport-azure-ad)uma biblioteca de autenticação para node.js desenvolvida e apoiada pela Microsoft.

Os seguintes passos demonstram como uma **API web** pode usar O Azure AD B2C para se proteger e expor os âmbitos selecionados a uma aplicação do cliente.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

Para proteger a sua Web API com O Azure AD B2C, primeiro precisa de o registar. Consulte [O registo do seu pedido](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) para etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Descarregue a aplicação da amostra

Descarregue a amostra como um ficheiro zip, ou clone-a a partir do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Configurar a autenticação

1. Abra `config.js` o ficheiro na amostra.

2. Configure a amostra com as credenciais de aplicação que obteve anteriormente ao registar a sua aplicação. Altere as seguintes linhas de código substituindo os valores pelos nomes do seu clienteID, anfitrião, inquilino id e nome de apólice.

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Para mais informações, consulte esta [amostra de API web Node.js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

Os seguintes passos demonstram como uma **aplicação de uma página** única pode usar O Azure AD B2C para se inscrever, iniciar sessão e chamar uma API web protegida.

### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação

Para implementar a autenticação, primeiro é necessário registar a sua candidatura. Consulte [O registo do seu pedido](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) para etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Passo 2: Descarregue a aplicação da amostra

Descarregue a amostra como um ficheiro zip, ou clone-a a partir do GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passo 3: Configurar a autenticação

Existem dois pontos de interesse na configuração da sua aplicação:

- Configurar ponto final da API e âmbitos expostos
- Configurar parâmetros de autenticação e âmbitos simbólicos

1. Abra `apiConfig.js` o ficheiro na amostra.

2. Configure a amostra com os parâmetros que obteve anteriormente ao registar a sua API web. Altere as seguintes linhas de código substituindo os valores pelo endereço da sua API web e os âmbitos expostos.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. Abra `authConfig.js` o ficheiro na amostra.

4. Configure a amostra com os parâmetros que obteve anteriormente ao registar a sua aplicação de uma página única. Altere as seguintes linhas de código substituindo os valores pelo seu ClientId, metadados de autoridade e âmbitos de pedidos simbólicos.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Para mais informações, consulte esta [amostra de aplicação de uma página única JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:
- [Fluxos de utilizadores](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Personalização da Experiência do Utilizador](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
