---
title: Diferenças entre MSAL.js e ADAL.js / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a Microsoft Authentication Library for JavaScript (MSAL.js) e a Azure AD Authentication Library for JavaScript (ADAL.js) e como escolher qual usar.
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
ms.openlocfilehash: 7238a78279528b4522d09178d00bf916f14bad88
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696423"
---
# <a name="differences-between-msal-js-and-adal-js"></a>Diferenças entre MSAL JS e ADAL JS

Tanto a Microsoft Authentication Library for JavaScript (MSAL.js) como a Azure AD Authentication Library for JavaScript (ADAL.js) são usadas para autenticar entidades da AD Azure e solicitar fichas da Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para programadores (v1.0) para autenticar identidades azure AD (contas de trabalho e escola) solicitando fichas usando a ADAL. Agora, usando mSAL.js, pode autenticar um conjunto mais amplo de identidades da Microsoft (identidades Azure AD e contas Microsoft, e contas sociais e locais através do Azure AD B2C) através da plataforma de identidade da Microsoft (v2.0).

Este artigo descreve como escolher entre a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) e a Biblioteca de Autenticação AD Azure para JavaScript (ADAL.js) e compara as duas bibliotecas.

## <a name="choosing-between-adaljs-and-msaljs"></a>Escolha entre ADAL.js e MSAL.js

Na maioria dos casos, pretende utilizar a plataforma de identidade da Microsoft e o MSAL.js, que é a mais recente geração de bibliotecas de autenticação da Microsoft. Utilizando MSAL.js, adquire fichas para utilizadores que saem da sua aplicação com a Azure AD (contas de trabalho e escola), contas Microsoft (pessoais) (MSA) ou Azure AD B2C.

Se já está familiarizado com o ponto final v1.0 (e ADAL.js), talvez queira ler [O que há de diferente no ponto final v2.0?](active-directory-v2-compare.md)

No entanto, ainda precisa de utilizar a ADAL.js se a sua aplicação precisar de contratar utilizadores com versões anteriores dos Serviços da Federação de [DiretórioAtivo (ADFS).](/windows-server/identity/active-directory-federation-services)

## <a name="key-differences-in-authentication-with-msaljs"></a>Principais diferenças na autenticação com MSAL.js

### <a name="core-api"></a>API central

* A ADAL.js utiliza o [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) como representação de uma instância da ligação da sua aplicação ao servidor de autorização ou fornecedor de identidade através de um URL de autoridade. Pelo contrário, a MSAL.js API é projetada em torno da aplicação do cliente do agente utilizador (uma forma de aplicação do cliente público em que o código do cliente é executado em um utilizador-agente como um navegador web). Fornece a `UserAgentApplication` classe para representar uma instância do contexto de autenticação da aplicação com o servidor de autorização. Para mais detalhes, consulte [Inicialize utilizando MSAL.js](msal-js-initializing-client-applications.md).

* Em ADAL.js, os métodos de aquisição de fichas estão `AuthenticationContext`associados a uma única autoridade estabelecida na . Em MSAL.js, os pedidos simbólicos de aquisição podem assumir valores de autoridade diferentes dos estabelecidos no `UserAgentApplication`. Isto permite que a MSAL.js adquira e cache tokens separadamente para vários inquilinos e contas de utilizador na mesma aplicação.

* O método para adquirir e renovar tokens `acquireToken` silenciosamente sem incitar os utilizadores é nomeado em ADAL.js. Em MSAL.js, este `acquireTokenSilent` método é nomeado para ser mais descritivo desta funcionalidade.

### <a name="authority-value-common"></a>Valor da autoridade`common`

Em v1.0, `https://login.microsoftonline.com/common` a utilização da autoridade permitirá que os utilizadores assinem qualquer conta Azure AD (para qualquer organização).

Em v2.0, `https://login.microsoftonline.com/common` utilizando a autoridade, permitirá que os utilizadores assinem qualquer conta de organização da Azure AD ou uma conta pessoal da Microsoft (MSA). Para restringir o signo apenas às contas AD Azure (o mesmo comportamento `https://login.microsoftonline.com/organizations`que com a ADAL.js), é necessário utilizar . Para mais detalhes, consulte a opção `authority` de config em [Inicialize utilizando MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Âmbitos para aquisição de fichas
* Âmbito em vez de parâmetro de recursos em pedidos de autenticação para adquirir fichas

    v2.0 protocolo utiliza âmbitos em vez de recursos nos pedidos. Por outras palavras, quando a sua aplicação precisa de solicitar fichas com permissões para um recurso como o MS Graph, a diferença de valores passados para os métodos da biblioteca é a seguinte:

    v1.0: recurso\:= https //graph.microsoft.com

    v2.0: âmbito\:= https //graph.microsoft.com/User.Read

    Pode solicitar âmbitos para qualquer recurso API utilizando o URI da API neste formato: appidURI/scope Por exemplo: https:\//mytenant.onmicrosoft.com/myapi/api.read

    Apenas para a API do `user.read` Gráfico MS, um âmbito de mapas de valor para https:\//graph.microsoft.com/User.Read e pode ser usado alternadamente.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Âmbitos dinâmicos para o consentimento incremental.

    Ao construir aplicações utilizando v1.0, foi necessário registar o conjunto completo de permissões (âmbitos estáticos) exigidos pelo pedido para que o utilizador consinta no momento do login. Em v2.0, pode utilizar o parâmetro de âmbito para solicitar as permissões no momento em que as desejar. Estes são chamados de âmbitos dinâmicos. Isto permite ao utilizador fornecer consentimento incremental aos âmbitos. Portanto, se no início apenas quiser que o utilizador aceda à sua aplicação e não precise de qualquer tipo de acesso, pode fazê-lo. Se mais tarde necessitar da capacidade de ler o calendário do utilizador, poderá então solicitar o âmbito do calendário nos métodos token adquiridos e obter o consentimento do utilizador. Por exemplo:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Âmbitos para V1.0 APIs

    Ao obter fichas para V1.0 APIs utilizando MSAL.js, pode solicitar todos os âmbitos estáticos registados na API, através do âmbito `.default` do ID app da API. Por exemplo:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Passos seguintes
Para mais informações, consulte a [comparação v1.0 e v2.0](active-directory-v2-compare.md).
