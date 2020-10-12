---
title: Protocolos de consentimento da plataforma de identidade da Microsoft
description: Uma descrição da autorização no ponto final da plataforma de identidade da Microsoft, incluindo âmbitos, permissões e consentimento.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: cb629b80958ed2897f76eb099f738c33b48c3696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119611"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Consentimento de administração na plataforma de identidade da Microsoft

Algumas permissões requerem o consentimento de um administrador antes de poderem ser concedidas dentro de um inquilino.  Você também pode usar o ponto final de consentimento administrativo para conceder permissões a um inquilino inteiro.

## <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando constrói uma aplicação que utiliza o ponto final de consentimento administrativo, a aplicação precisa de uma página ou vista na qual o administrador pode aprovar as permissões da app. Esta página pode fazer parte do fluxo de inscrição da aplicação, parte das definições da aplicação, ou pode ser um fluxo dedicado de "connect". Em muitos casos, faz sentido que a app mostre esta vista de "conectar" apenas depois de um utilizador ter assinado com uma conta microsoft de trabalho ou escola.

Quando assinar o utilizador na sua aplicação, pode identificar a organização a que o administrador pertence antes de pedir-lhes que aprovem as permissões necessárias. Embora não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. Para iniciar sômsim do utilizador, siga os [nossos tutoriais de protocolo de plataforma de identidade da Microsoft.](active-directory-v2-protocols.md)

## <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões a um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da sua organização, pode redirecionar o utilizador para o *ponto final de consentimento da*plataforma de identidade da Microsoft.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parâmetro     | Condição     | Descrição                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Obrigatório | O inquilino do diretório que quer pedir autorização. Pode ser fornecido em formato guiado ou de nome amigável ou genericamente referenciado com `organizations` o que se vê no exemplo. Não utilize 'comum', uma vez que as contas pessoais não podem fornecer consentimento administrativo, exceto no contexto de um inquilino. Para garantir a melhor compatibilidade com contas pessoais que gerem os inquilinos, utilize o ID do inquilino sempre que possível. |
| `client_id` | Necessário | O **ID da Aplicação (cliente)** que o [portal Azure – Experiência de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `redirect_uri` | Necessário |O URI de redirecionamento onde deseja que a resposta seja enviada para que a sua aplicação seja tratada. Deve corresponder exatamente a um dos URIs redirecionados que registou no portal de registo de aplicações. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. Utilize o estado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam. |
|`scope`        | Necessário      | Define o conjunto de permissões solicitadas pela aplicação. Isto pode ser estático (usando /.predefinido) ou âmbitos dinâmicos.  Isto pode incluir os âmbitos OIDC `openid` (, `profile` , . `email` . |


Neste momento, a Azure AD exige que um administrador de inquilino assine para completar o pedido. Pede-se ao administrador que aprove todas as permissões que solicitou no `scope` parâmetro.  Se tiver usado um valor estático `/.default` () funcionará como o ponto final de consentimento de administração v1.0 e solicitará o consentimento para todos os âmbitos encontrados nas permissões necessárias para a aplicação.

### <a name="successful-response"></a>Resposta bem sucedida

Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida é a seguinte:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parâmetro         | Descrição                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| O inquilino do diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID.|
| `state`           | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam.|
| `scope`          | O conjunto de permissões a que foi dado acesso, para o pedido.|
| `admin_consent`   | Será definido para `True` .|

### <a name="error-response"></a>Resposta de erro

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Adicionando aos parâmetros vistos numa resposta bem sucedida, os parâmetros de erro são vistos como abaixo.

| Parâmetro          | Descrição                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros.|
| `error_description`| Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro.|
| `tenant`| O inquilino do diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID.|
| `state`           | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que quiser. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a visualização em que se encontravam.|
| `admin_consent`   | Será programado `True` para indicar que esta resposta ocorreu num fluxo de consentimento administrativo.|

## <a name="next-steps"></a>Passos seguintes
- Veja [como converter uma app para ser multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
- Saiba como [o consentimento é suportado na camada do protocolo OAuth 2.0 durante o fluxo de concessão de código de autorização](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Saiba [como uma aplicação multi-arrendatário pode usar o quadro de consentimento](./howto-convert-app-to-be-multi-tenant.md) para implementar o consentimento "utilizador" e "administrador", suportando padrões de aplicação de vários níveis mais avançados.
- Compreender [experiências de consentimento de pedidos de Azure AD](application-consent-experience.md)