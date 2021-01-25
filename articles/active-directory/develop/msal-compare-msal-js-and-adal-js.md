---
title: Diferenças entre MSAL.js e ADAL.js | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as diferenças entre a Microsoft Authentication Library para JavaScript (MSAL.js) e a Azure AD Authentication Library for JavaScript (ADAL.js) e como escolher qual usar.
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
ms.openlocfilehash: a1ec8c31681ab6c0ac40ba33e94f33057ee948e7
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754794"
---
# <a name="differences-between-msaljs-and-adaljs"></a>Diferenças entre MSAL.js e ADAL.js

Tanto a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) como a Azure AD Authentication Library for JavaScript (ADAL.js) são utilizadas para autenticar entidades AD do Azure e solicitar fichas a partir de Azure AD. Até agora, a maioria dos desenvolvedores tem trabalhado com a Azure AD para desenvolvedores (v1.0) para autenticar identidades AZure AD (trabalho e contas escolares) solicitando fichas usando ADAL. Agora, usando MSAL.js, pode autenticar um conjunto mais alargado de identidades da Microsoft (identidades AD AD Azure e contas da Microsoft, e contas sociais e locais através do Azure AD B2C) através da plataforma de identidade da Microsoft.

Este artigo descreve como escolher entre a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js) e a Biblioteca de Autenticação AD AZure para o JavaScript (ADAL.js) e compara as duas bibliotecas.

## <a name="choosing-between-adaljs-and-msaljs"></a>Escolher entre ADAL.js e MSAL.js

Na maioria dos casos pretende utilizar a plataforma de identidade da Microsoft e MSAL.js, que é a última geração de bibliotecas de autenticação da Microsoft. Utilizando MSAL.js, adquire fichas para utilizadores que se inscrevam na sua aplicação com Azure AD (contas de trabalho e escola), contas da Microsoft (pessoais) (MSA) ou Azure AD B2C.

Se já está familiarizado com o ponto final v1.0 (e ADAL.js), talvez queira ler [O que há de diferente no ponto final v2.0?](../azuread-dev/azure-ad-endpoint-comparison.md)

No entanto, ainda precisa de utilizar ADAL.js se a sua aplicação precisar de assinar em utilizadores com versões anteriores dos Serviços da [Federação de Diretórios Ativos (ADFS)](/windows-server/identity/active-directory-federation-services).

## <a name="key-differences-in-authentication-with-msaljs"></a>Principais diferenças na autenticação com MSAL.js

### <a name="core-api"></a>Core API

* ADAL.js utiliza [o AutenticaçãoContexto](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) como representação de uma instância da ligação da sua aplicação ao servidor de autorização ou fornecedor de identidade através de um URL de autoridade. Pelo contrário, MSAL.js API é projetado em torno da aplicação do cliente do agente utilizador (uma forma de aplicação de cliente público em que o código do cliente é executado em um agente de utilizador, como um navegador web). Fornece a `UserAgentApplication` classe para representar uma instância do contexto de autenticação da aplicação com o servidor de autorização. Para mais detalhes, consulte [Initialize usando MSAL.js](msal-js-initializing-client-applications.md).

* Em ADAL.js, os métodos de aquisição de fichas estão associados a uma única autoridade definida no `AuthenticationContext` . Em MSAL.js, os pedidos de ficha de aquisição podem ter valores de autoridade diferentes dos estabelecidos no `UserAgentApplication` . Isto permite que MSAL.js adquiram e cache tokens separadamente para vários inquilinos e contas de utilizador na mesma aplicação.

* O método para adquirir e renovar tokens silenciosamente sem avisar os utilizadores é nomeado `acquireToken` em ADAL.js. Em MSAL.js, este método é nomeado `acquireTokenSilent` para ser mais descritivo desta funcionalidade.

### <a name="authority-value-common"></a>Valor da autoridade `common`

Em v1.0, a utilização da `https://login.microsoftonline.com/common` autoridade permitirá que os utilizadores assinem com qualquer conta AD Azure (para qualquer organização).

Em v2.0, usando a `https://login.microsoftonline.com/common` autoridade, permitirá que os utilizadores entrem em qualquer conta de organização AZure ou uma conta pessoal da Microsoft (MSA). Para restringir o sinal apenas às contas AD do Azure (o mesmo comportamento que com ADAL.js), tem de usar `https://login.microsoftonline.com/organizations` . Para mais detalhes, consulte a `authority` opção config em [Initialize usando MSAL.js](msal-js-initializing-client-applications.md).

### <a name="scopes-for-acquiring-tokens"></a>Âmbitos de aquisição de fichas
* Âmbito em vez de parâmetro de recursos em pedidos de autenticação para adquirir fichas

    o protocolo v2.0 utiliza âmbitos em vez de recursos nos pedidos. Por outras palavras, quando a sua aplicação precisa de solicitar fichas com permissões para um recurso como o MS Graph, a diferença de valores transmitida aos métodos da biblioteca é a seguinte:

    v1.0: recurso = https \: //graph.microsoft.com

    v2.0: âmbito = https \: //graph.microsoft.com/User.Read

    Pode solicitar âmbitos para qualquer recurso API utilizando o URI da API neste formato: appidURI/scope Por exemplo: https: \/ /mytenant.onmicrosoft.com/myapi/api.read

    Apenas para a MS Graph API, um valor de âmbito `user.read` mapeia para https: \/ /graph.microsoft.com/User.Read e pode ser usado intercambiavelmente.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Âmbitos dinâmicos para consentimento incremental.

    Ao criar aplicações utilizando o v1.0, foi necessário registar o conjunto completo de permissões (âmbitos estáticos) exigidos pelo pedido para que o utilizador consinta no momento do início. Em v2.0, pode utilizar o parâmetro de âmbito para solicitar as permissões no momento em que as desejar. Estes são chamados âmbitos dinâmicos. Isto permite ao utilizador fornecer consentimento incremental aos âmbitos. Por isso, se no início apenas pretender que o utilizador faça o sômis para a sua aplicação e não necessita de qualquer tipo de acesso, pode fazê-lo. Se mais tarde precisar da capacidade de ler o calendário do utilizador, pode então solicitar o âmbito do calendário nos métodos acquireToken e obter o consentimento do utilizador. Por exemplo:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Âmbitos para APIs V1.0

    Ao obter fichas para APIs V1.0 utilizando MSAL.js, pode solicitar todos os âmbitos estáticos registados na API, anexando `.default` à App ID URI da API como âmbito. Por exemplo:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Próximos passos
Para obter mais informações, consulte a [comparação v1.0 e v2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
