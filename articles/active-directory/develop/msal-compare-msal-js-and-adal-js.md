---
title: Diferenças entre a msal e ADAL.js | Azure
description: Saiba mais sobre as diferenças entre a biblioteca de autenticação da Microsoft para JavaScript (msal) e do Azure AD Authentication Library para JavaScript (ADAL.js) e como escolher quais pode ser utilizado.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10b5169d3f06e265b3effa3ec18ad8e4f69959d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121961"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Diferenças entre a MSAL JS e JS da ADAL

Ambos os Microsoft Authentication Library para JavaScript (msal) e do Azure AD Authentication Library para JavaScript (ADAL.js) são utilizados para autenticar as entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalharam com o Azure AD para desenvolvedores (v1.0) autenticar identidades do Azure AD (contas profissionais e escolares) ao pedir tokens através da ADAL. Agora, a utilizar a msal, pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e as contas Microsoft e contas de redes sociais e locais através do Azure AD B2C) através da plataforma de identidade da Microsoft (v2.0).

Este artigo descreve como escolher entre a biblioteca de autenticação da Microsoft para JavaScript (msal) e do Azure AD Authentication Library para JavaScript (ADAL.js) e compara as duas bibliotecas.

## <a name="choosing-between-adaljs-and-msaljs"></a>Escolha entre ADAL.js e msal

Na maioria dos casos que desejar usar a plataforma de identidade da Microsoft e a msal, que é a última geração de bibliotecas de autenticação da Microsoft. Utilizar a msal, adquira tokens para os utilizadores o início de sessão sua aplicação com o Azure AD (contas profissionais e escolares), contas da Microsoft (pessoal) (MSA), ou do Azure AD B2C.

Se já estiver familiarizado com o ponto final de v1.0 (e ADAL.js), pode querer ler [quais são as diferenças sobre o ponto final v2.0?](active-directory-v2-compare.md).

No entanto, ainda tem de utilizar ADAL.js se precisar de iniciar sessão dos utilizadores com versões anteriores do seu aplicativo [serviços de Federação do Active Directory (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Principais diferenças na autenticação com msal

### <a name="core-api"></a>API principal

* Utiliza ADAL.js [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) como a representação de uma instância de ligação da sua aplicação para o servidor de autorização ou o fornecedor de identidade através de um URL de autoridade. Por outro lado, a API de msal foi concebido para a aplicação de cliente do agente de utilizador (uma forma de aplicação de cliente público no qual o código de cliente é executado num agente de usuário, como um navegador da web). Ele fornece o `UserAgentApplication` classe que representa uma instância do contexto de autenticação da aplicação com o servidor de autorização. Para obter mais detalhes, consulte [inicializar com msal](msal-js-initializing-client-applications.md).

* No ADAL.js, os métodos para adquirir tokens são associados a uma autoridade de única definida no `AuthenticationContext`. Em msal, os pedidos de token de aquisição podem demorar a valores de autoridade diferentes ao é definido `UserAgentApplication`. Isso permite que a msal adquirir e tokens de cache em separado para vários inquilinos e contas de utilizador no mesmo aplicativo.

* O método para adquirir e renovar tokens silenciosamente sem avisar aos usuários com o nome `acquireToken` no ADAL.js. Msal, esse método é chamado `acquireTokenSilent` para ser mais descritivo essa funcionalidade.

### <a name="authority-value-common"></a>Valor de autoridade `common`

Na versão 1.0, usando o `https://login.microsoftonline.com/common` autoridade permitirá aos utilizadores iniciar sessão com qualquer conta do Azure AD (para qualquer organização).

Na versão 2.0, usando o `https://login.microsoftonline.com/common` autoridade, permitirá aos utilizadores iniciar sessão com qualquer conta de organização do Azure AD ou uma conta pessoal da Microsoft (MSA). Para restringir o início de sessão para apenas contas do Azure AD (mesmo comportamento que com ADAL.js), tem de utilizar `https://login.microsoftonline.com/organizations`. Para obter detalhes, consulte a `authority` opção de configuração no [inicializar com msal](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Âmbitos para a aquisição de tokens
* Âmbito em vez do parâmetro de recurso em pedidos de autenticação para adquirir tokens

    protocolo de v2.0 utiliza âmbitos em vez de recursos nos pedidos. Em outras palavras, quando seu aplicativo precisa para pedir tokens com permissões para um recurso, como o MS Graph, a diferença nos valores passados para os métodos de biblioteca é o seguinte:

    V1.0: recursos =https://graph.microsoft.com

    v2.0: scope = https://graph.microsoft.com/User.Read

    Pode pedir âmbitos para qualquer recurso de API com o URI da API, no seguinte formato: appidURI/escopo, por exemplo: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Apenas para o MS Graph API, um valor de âmbito `user.read` mapeia para https://graph.microsoft.com/User.Read e pode ser utilizado alternadamente.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Âmbitos de dinâmicos para consentimento incremental.

    Ao criar aplicativos usando a versão 1.0, precisava registar o conjunto completo de permissões (âmbitos estáticos) exigidas pela aplicação para o utilizador autorizar o momento de início de sessão. Na versão 2.0, pode utilizar o parâmetro de âmbito para pedir as permissões no momento em que pretende. Estes são denominados âmbitos dinâmicos. Isto permite ao utilizador fornecer consentimento incremental para âmbitos. Portanto, se no início apenas pretende que o utilizador iniciar sessão na sua aplicação e não é necessário qualquer tipo de acesso, pode fazer isso. Se precisar posteriormente a capacidade de ler o calendário do utilizador, pode, em seguida, solicitar o âmbito de calendário nos métodos acquireToken e obter o consentimento do utilizador. Por exemplo:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Âmbitos para APIs V1.0

    Ao obter tokens para APIs de V1.0 com msal, pode solicitar todos os âmbitos de estáticos registrados na API, acrescentando `.default` para o URI de ID de aplicação da API como âmbito. Por exemplo:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte [comparação de versões v1.0 e v2.0](active-directory-v2-compare.md).
