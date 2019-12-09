---
title: Diferenças entre MSAL. js e ADAL. js
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) e a biblioteca de autenticação do Azure AD para JavaScript (ADAL. js) e como escolher qual delas usar.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3696eb90abbb35f4d989649b4a1198aec69b9a5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916915"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Diferenças entre MSAL JS e ADAL JS

A biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) e a biblioteca de autenticação do Azure AD para JavaScript (ADAL. js) são usadas para autenticar entidades do Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalhou com o Azure AD para desenvolvedores (v 1.0) para autenticar identidades do Azure AD (contas corporativas e de estudante) solicitando tokens usando a ADAL. Agora, usando o MSAL. js, você pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades do Azure AD e contas da Microsoft e contas sociais e locais por meio do Azure AD B2C) por meio da plataforma Microsoft Identity (v 2.0).

Este artigo descreve como escolher entre a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) e a biblioteca de autenticação do Azure AD para JavaScript (ADAL. js) e compara as duas bibliotecas.

## <a name="choosing-between-adaljs-and-msaljs"></a>Escolhendo entre ADAL. js e MSAL. js

Na maioria dos casos, você deseja usar a plataforma de identidade da Microsoft e o MSAL. js, que é a última geração de bibliotecas de autenticação da Microsoft. Usando o MSAL. js, você adquire tokens para usuários que entram em seu aplicativo com o Azure AD (contas corporativas e de estudante), MSA (contas pessoais) da Microsoft ou Azure AD B2C.

Se você já estiver familiarizado com o ponto de extremidade v 1.0 (e o ADAL. js), talvez queira ler [o que há de diferente no ponto de extremidade v 2.0?](active-directory-v2-compare.md).

No entanto, você ainda precisará usar o ADAL. js se seu aplicativo precisar conectar usuários com versões anteriores do [serviços de Federação do Active Directory (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Principais diferenças na autenticação com MSAL. js

### <a name="core-api"></a>API principal

* O ADAL. js usa [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) como a representação de uma instância da conexão do aplicativo para o servidor de autorização ou o provedor de identidade por meio de uma URL de autoridade. No contrário, a API MSAL. js é projetada em relação ao aplicativo cliente do agente do usuário (uma forma de aplicativo cliente público em que o código do cliente é executado em um agente de usuário, como um navegador da Web). Ele fornece a classe `UserAgentApplication` para representar uma instância do contexto de autenticação do aplicativo com o servidor de autorização. Para obter mais detalhes, consulte [inicializar usando MSAL. js](msal-js-initializing-client-applications.md).

* No ADAL. js, os métodos para adquirir tokens são associados a uma única autoridade definida no `AuthenticationContext`. No MSAL. js, as solicitações de token de aquisição podem ter valores de autoridade diferentes dos que estão definidos no `UserAgentApplication`. Isso permite que o MSAL. js adquira e armazene em cache tokens separadamente para vários locatários e contas de usuário no mesmo aplicativo.

* O método para adquirir e renovar tokens silenciosamente sem avisar os usuários é denominado `acquireToken` no ADAL. js. No MSAL. js, esse método é denominado `acquireTokenSilent` para ser mais descritivo dessa funcionalidade.

### <a name="authority-value-common"></a>`common` de valor de autoridade

Na v 1.0, o uso da autoridade de `https://login.microsoftonline.com/common` permitirá que os usuários entrem com qualquer conta do Azure AD (para qualquer organização).

Na v 2.0, usar a autoridade de `https://login.microsoftonline.com/common` permitirá que os usuários entrem com qualquer conta da organização do Azure AD ou uma MSA (conta pessoal da Microsoft). Para restringir a entrada somente às contas do Azure AD (mesmo comportamento do ADAL. js), você precisa usar `https://login.microsoftonline.com/organizations`. Para obter detalhes, consulte a opção de configuração `authority` em [inicializar usando MSAL. js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Escopos para adquirir tokens
* Escopo em vez do parâmetro de recurso em solicitações de autenticação para adquirir tokens

    o protocolo v 2.0 usa escopos em vez de recurso nas solicitações. Em outras palavras, quando seu aplicativo precisa solicitar tokens com permissões para um recurso como o MS Graph, a diferença nos valores passados para os métodos de biblioteca é a seguinte:

    v 1.0: Resource = https\://graph.microsoft.com

    v 2.0: scope = https\://graph.microsoft.com/User.Read

    Você pode solicitar escopos para qualquer API de recurso usando o URI da API neste formato: appidURI/Scope, por exemplo: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Somente para o MS API do Graph, um valor de escopo `user.read` é mapeado para https:\//graph.microsoft.com/User.Read e pode ser usado de forma intercambiável.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Escopos dinâmicos para consentimento incremental.

    Ao criar aplicativos usando a v 1.0, você precisava registrar o conjunto completo de permissões (escopos estáticos) exigido pelo aplicativo para que o usuário consentisse no momento do logon. Na v 2.0, você pode usar o parâmetro scope para solicitar as permissões no momento em que desejar. Eles são chamados de escopos dinâmicos. Isso permite que o usuário forneça consentimento incremental para escopos. Portanto, se no início você quiser que o usuário entre no seu aplicativo e não precisar de nenhum tipo de acesso, poderá fazer isso. Se posteriormente você precisar da capacidade de ler o calendário do usuário, poderá solicitar o escopo do calendário nos métodos acquireToken e obter o consentimento do usuário. Por exemplo:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Escopos para APIs V 1.0

    Ao obter tokens para APIs V 1.0 usando MSAL. js, você pode solicitar todos os escopos estáticos registrados na API acrescentando `.default` ao URI de ID do aplicativo da API como escopo. Por exemplo:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [comparação v 1.0 e v 2.0](active-directory-v2-compare.md).
