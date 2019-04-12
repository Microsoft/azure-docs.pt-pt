---
title: Use plataforma de identidade Microsoft para iniciar sessão de utilizadores que utilizam ROPC | Azure
description: Autenticação de navegador sem suporte fluxos com a concessão de credenciais de palavra-passe de proprietário do recurso.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c1372263bfa3f684d30ad583bfb6a9d434c3cc2
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499942"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Plataforma de identidade da Microsoft e a credencial de palavra-passe de proprietário de recursos do OAuth 2.0

Suporte de plataforma de identidade do Microsoft da [conceder credenciais de palavra-passe de proprietário do recurso (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo iniciar o utilizador ao lidar diretamente com a palavra-passe. O fluxo ROPC requer um alto grau de exposição de confiança e de utilizador e os programadores só devem utilizar este fluxo, quando os fluxos de outros, mais seguros, não podem ser utilizados.

> [!IMPORTANT]
> * O ponto de extremidade de plataforma do Microsoft identity só suporta ROPC para inquilinos do Azure AD, as contas pessoais não. Isso significa que tem de utilizar um ponto de extremidade específico de inquilino (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o `organizations` ponto final.
> * Contas pessoais que estão convidadas a um inquilino do Azure AD não é possível utilizar ROPC.
> * Contas que não têm as palavras-passe não podem iniciar sessão através de ROPC. Para este cenário, recomendamos que utilize um fluxo diferente para a sua aplicação em vez disso.
> * Se os utilizadores têm de utilizar a autenticação multifator (MFA) para iniciar sessão na aplicação, serão bloqueados em vez disso.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra o fluxo ROPC.

![Fluxo ROPC](media/v2-oauth2-ropc/v2-oauth-ropc.png)

## <a name="authorization-request"></a>Pedido de autorização

O fluxo ROPC é uma única solicitação&mdash;envia ao cliente para a identificação e as credenciais do utilizador para o IDP e, em seguida, receber em troca tokens. O cliente tem de pedir endereço de e-mail do utilizador (UPN) e a palavra-passe antes de fazer isso. Imediatamente após um pedido com êxito, o cliente deve libertar de forma segura as credenciais do usuário da memória. Nunca deve guardá-los.

> [!TIP]
> Experimente executar este pedido no Postman!
> [![Executar no Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Parâmetro | Condição | Descrição |
| --- | --- | --- |
| `tenant` | Necessário | O inquilino do diretório que pretende registar o utilizador em. Isso pode ser no formato de nome amigável ou de GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `grant_type` | Necessário | Tem de ser definido como `password`. |
| `username` | Necessário | Endereço de e-mail do utilizador. |
| `password` | Necessário | A senha do usuário. |
| `scope` | Recomendado | Uma lista separada por espaço de [âmbitos](v2-permissions-and-consent.md), ou permissões, que o aplicativo exige. Nestes âmbitos tem consentimento ao antecipadamente por um administrador ou pelo usuário num fluxo de interativo. |

### <a name="successful-authentication-response"></a>Resposta de autenticação com êxito

O código a seguir mostra um exemplo de uma resposta de token com êxito:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parâmetro | Formato | Descrição |
| --------- | ------ | ----------- |
| `token_type` | String | Sempre definido como `Bearer`. |
| `scope` | Cadeias de caracteres de espaço separado | Se foi devolvido um token de acesso, este parâmetro indica os âmbitos que o token de acesso é válido para. |
| `expires_in`| int | Número de segundos que o token de acesso incluído é válido para. |
| `access_token`| Cadeia opaca | Emitido para o [âmbitos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | If emitido original `scope` parâmetro incluído o `openid` âmbito. |
| `refresh_token` | Cadeia opaca | If emitido original `scope` parâmetro incluído `offline_access`. |

Pode usar o token de atualização para adquirir novos tokens de acesso e tokens utilizando o mesmo fluxo descrito de atualização do [documentação de fluxo de código de OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o utilizador ainda não forneceu o nome de utilizador correto ou a palavra-passe ou o cliente não recebeu o pedido de consentimento, a autenticação irá falhar.

| Erro | Descrição | Ação de cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais foram incorretas ou o cliente não tem autorização para os âmbitos pedidas. Se os âmbitos não são concedidos, um `consent_required` vai ser devolvido o erro. Se isto ocorrer, o cliente deve enviar o utilizador para uma linha de comandos interativa através de uma webview ou browser. |
| `invalid_request` | O pedido foi construído incorretamente | O tipo de concessão não é suportado o `/common` ou `/consumers` contextos de autenticação.  Utilize `/organizations` em vez disso. |
| `invalid_client` | A aplicação está configurada incorretamente | Isto pode acontecer se o `allowPublicClient` propriedade não está definida como verdadeiro para o [manifesto do aplicativo](reference-app-manifest.md). O `allowPublicClient` propriedade é necessária porque a concessão ROPC não tem um URI de redirecionamento. O Azure AD não consegue determinar se a aplicação é um aplicativo de cliente público ou um cliente confidencial, a menos que a propriedade está definida. Tenha em atenção que ROPC só é suportada para aplicações de cliente público. |

## <a name="learn-more"></a>Saiba mais

* Experimentar o ROPC na utilizando o [exemplo de aplicação de consola](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se deve utilizar o ponto final v2.0, leia sobre [limitações de plataforma de identidade do Microsoft](active-directory-v2-limitations.md).
