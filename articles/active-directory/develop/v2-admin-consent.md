---
title: Protocolos de consentimento do administrador da plataforma de identidade da Microsoft | Microsoft Docs
description: Uma descrição da autorização no ponto de extremidade da plataforma Microsoft Identity, incluindo escopos, permissões e consentimento.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b820be5631d207a32cbf14aa1eec9f3f6de2af52
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766060"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Consentimento do administrador na plataforma Microsoft Identity

Algumas permissões exigem consentimento de um administrador antes que possam ser concedidas dentro de um locatário.  Você também pode usar o ponto de extremidade de consentimento do administrador para conceder permissões a um locatário inteiro.  

## <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: conectar o usuário ao seu aplicativo

Normalmente, quando você cria um aplicativo que usa o ponto de extremidade de consentimento do administrador, o aplicativo precisa de uma página ou exibição na qual o administrador possa aprovar as permissões do aplicativo. Essa página pode fazer parte do fluxo de inscrição do aplicativo, parte das configurações do aplicativo ou pode ser um fluxo de "conexão" dedicado. Em muitos casos, faz sentido que o aplicativo mostre esse modo de exibição "conectar" somente depois que um usuário tiver entrado com uma conta Microsoft corporativa ou de estudante.

Ao conectar o usuário ao seu aplicativo, você pode identificar a organização à qual o administrador pertence antes de pedir que eles aprovem as permissões necessárias. Embora não seja estritamente necessário, ele pode ajudá-lo a criar uma experiência mais intuitiva para seus usuários organizacionais. Para conectar o usuário, siga nossos [tutoriais de protocolo de plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Solicitar as permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões do administrador da sua organização, você poderá redirecionar o usuário para o ponto de *extremidade de consentimento do administrador*da plataforma de identidade da Microsoft.

```
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
| `tenant` | Obrigatório | O locatário do diretório do qual você deseja solicitar permissão. Pode ser fornecido em formato de nome amigável ou GUID ou referenciado genericamente com `organizations` como visto no exemplo. Não use ' Common ', pois as contas pessoais não podem fornecer consentimento de administrador, exceto no contexto de um locatário. Para garantir a melhor compatibilidade com contas pessoais que gerenciam locatários, use a ID do locatário quando possível. |
| `client_id` | Obrigatório | A **ID do aplicativo (cliente)** que a [portal do Azure – registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída ao seu aplicativo. |
| `redirect_uri` | Obrigatório |O URI de redirecionamento no qual você deseja que a resposta seja enviada para que seu aplicativo manipule. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal de registro de aplicativo. |
| `state` | Recomendado | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Use o estado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
|`scope`        | Obrigatório      | Define o conjunto de permissões que estão sendo solicitadas pelo aplicativo. Pode ser estático (usando/.default) ou escopos dinâmicos.  Isso pode incluir os escopos OIDC (`openid`, `profile`, `email`). | 


Neste ponto, o Azure AD requer que um administrador de locatários entre para concluir a solicitação. O administrador é solicitado a aprovar todas as permissões que você solicitou no parâmetro `scope`.  Se você usou um valor estático (`/.default`), ele funcionará como o ponto de extremidade de consentimento do administrador v 1.0 e solicitará o consentimento para todos os escopos encontrados nas permissões necessárias para o aplicativo.

### <a name="successful-response"></a>Resposta bem-sucedida

Se o administrador aprovar as permissões para seu aplicativo, a resposta bem-sucedida terá esta aparência:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parâmetro         | Descrição                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| O locatário de diretório que concedeu ao aplicativo as permissões solicitadas, no formato GUID.|
| `state`           | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam.|
| `scope`          | O conjunto de permissões ao qual foi concedido acesso para o aplicativo.|
| `admin_consent`   | Será definido como `True`.|

### <a name="error-response"></a>Resposta de erro

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Adicionando aos parâmetros vistos em uma resposta bem-sucedida, os parâmetros de erro são vistos como abaixo.

| Parâmetro          | Descrição                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros.|
| `error_description`| Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro.|
| `tenant`| O locatário de diretório que concedeu ao aplicativo as permissões solicitadas, no formato GUID.|
| `state`           | Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. O estado é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam.|
| `admin_consent`   | Será definido como `True` para indicar que essa resposta ocorreu em um fluxo de consentimento do administrador.|

## <a name="next-steps"></a>Passos seguintes
- Consulte [como converter um aplicativo para ser multilocatário](howto-convert-app-to-be-multi-tenant.md)
- Saiba como [o consentimento tem suporte na camada de protocolo OAuth 2,0 durante o fluxo de concessão de código de autorização](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Saiba [como um aplicativo multilocatário pode usar a estrutura de consentimento](active-directory-devhowto-multi-tenant-overview.md) para implementar o consentimento de "usuário" e "administrador", dando suporte a padrões de aplicativos de várias camadas mais avançados.
- Noções básicas sobre [experiências de consentimento de aplicativo do Azure ad](application-consent-experience.md)
