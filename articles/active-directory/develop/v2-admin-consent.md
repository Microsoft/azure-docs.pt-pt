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
ms.openlocfilehash: c9f633e0d205adaf5cefb2e3c036ce7f48253651
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886386"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Consentimento do administrador na plataforma de identidade da Microsoft

Algumas permissões requerem o consentimento de um administrador antes de poderem ser concedidas dentro de um inquilino.  Você também pode usar o ponto final de consentimento administrativo para conceder permissões a um inquilino inteiro.  

## <a name="recommended-sign-the-user-into-your-app"></a>Recomendado: Inscreva o utilizador na sua aplicação

Normalmente, quando se constrói uma aplicação que utiliza o ponto final do consentimento do administrador, a aplicação precisa de uma página ou vista na qual o administrador pode aprovar as permissões da app. Esta página pode fazer parte do fluxo de inscrição da aplicação, parte das definições da aplicação, ou pode ser um fluxo dedicado de "connect". Em muitos casos, faz sentido que a app mostre esta visão de "connect" apenas depois de um utilizador ter assinado com uma conta de trabalho ou escola da Microsoft.

Ao assinar o utilizador na sua aplicação, pode identificar a organização a que pertence o administrador antes de pedir-lhes que aprovem as permissões necessárias. Embora não seja estritamente necessário, pode ajudá-lo a criar uma experiência mais intuitiva para os seus utilizadores organizacionais. Para iniciar sessão com o utilizador, siga os [nossos tutoriais](active-directory-v2-protocols.md)de protocolo de plataforma de identidade da Microsoft .

## <a name="request-the-permissions-from-a-directory-admin"></a>Solicite as permissões de um administrador de diretório

Quando estiver pronto para solicitar permissões ao administrador da sua organização, pode redirecionar o utilizador para o *ponto final*de consentimento da plataforma de identidade da Microsoft .

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
| `tenant` | Necessário | O inquilino do diretório de quem quer pedir autorização. Pode ser fornecido em formato de nome `organizations` GUIA ou amigável OU genericamente referenciado com o exemplo. Não utilize "comum", uma vez que as contas pessoais não podem dar consentimento à administração, exceto no contexto de um inquilino. Para garantir a melhor compatibilidade com contas pessoais que gerem os inquilinos, utilize o ID do inquilino sempre que possível. |
| `client_id` | Necessário | O ID de **Aplicação (cliente)** que o [portal Azure – App registra](https://go.microsoft.com/fwlink/?linkid=2083908) experiência atribuída à sua app. |
| `redirect_uri` | Necessário |O URI redireciona do URI onde pretende que a resposta seja enviada para a sua aplicação manusear. Deve corresponder exatamente a uma das URIs redirecionais que registou no portal de registo da aplicação. |
| `state` | Recomendado | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. Utilize o Estado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava. |
|`scope`        | Necessário      | Define o conjunto de permissões que estão a ser solicitadas pelo pedido. Isto pode ser estático (utilizando /.padrão) ou âmbitos dinâmicos.  Isto pode incluir os âmbitos`openid` `profile`oIDC ( , , `email`). | 


Neste momento, a Azure AD exige que um administrador inquilino assine o pedido. Pede-se ao administrador que aprove todas as `scope` permissões que solicitou no parâmetro.  Se tiver usado um`/.default`valor estático , funcionará como o ponto final de consentimento da v1.0 e solicitará o consentimento de todos os âmbitos encontrados nas permissões necessárias para a app.

### <a name="successful-response"></a>Resposta bem sucedida

Se o administrador aprovar as permissões para a sua aplicação, a resposta bem sucedida é a seguinte:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parâmetro         | Descrição                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| O inquilino de diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID.|
| `state`           | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava.|
| `scope`          | O conjunto de permissões que tiveram acesso, para o pedido.|
| `admin_consent`   | Será definido `True`para .|

### <a name="error-response"></a>Resposta de erro

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Adicionando aos parâmetros observados numa resposta bem sucedida, os parâmetros de erro são vistos como abaixo.

| Parâmetro          | Descrição                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros.|
| `error_description`| Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro.|
| `tenant`| O inquilino de diretório que concedeu ao seu pedido as permissões que solicitou, em formato GUID.|
| `state`           | Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queiras. O Estado é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que se encontrava.|
| `admin_consent`   | Será definido `True` para indicar que esta resposta ocorreu num fluxo de consentimento administrativo.|

## <a name="next-steps"></a>Passos seguintes
- Veja [como converter uma app para ser multi-inquilino](howto-convert-app-to-be-multi-tenant.md)
- Saiba como o consentimento é suportado na camada de [protocolo OAuth 2.0 durante o fluxo de concessão do código](v2-oauth2-auth-code-flow.md#request-an-authorization-code)de autorização .
- Saiba [como uma aplicação multi-arrendatária pode usar o quadro de consentimento](active-directory-devhowto-multi-tenant-overview.md) para implementar o consentimento de "utilizador" e "administrador", suportando padrões de aplicação multi-nível mais avançados.
- Compreender experiências de consentimento de [aplicação da AD Azure](application-consent-experience.md)
