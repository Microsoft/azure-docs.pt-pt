---
title: Usar a plataforma de identidade da Microsoft para conectar usuários usando a concessão de credencial de senha do proprietário do recurso (ROPC) | Azure
description: Suporte a fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário do recurso.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d5324aba5202abb76f07d1eaf43fe214e690393
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193217"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Plataforma Microsoft Identity e a credencial de senha do proprietário do recurso OAuth 2,0

A plataforma de identidade da Microsoft oferece suporte à concessão de credencial de [senha (ROPC) do proprietário do recurso](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo Conecte o usuário manipulando sua senha diretamente. O fluxo ROPC requer um alto grau de confiança e exposição do usuário, e você só deve usar esse fluxo quando outros, mais seguros, fluxos não podem ser usados.

> [!IMPORTANT]
>
> * O ponto de extremidade da plataforma Microsoft Identity suporta apenas ROPC para locatários do Azure AD, não contas pessoais. Isso significa que você deve usar um ponto de extremidade específico do`https://login.microsoftonline.com/{TenantId_or_Name}`locatário () `organizations` ou o ponto de extremidade.
> * As contas pessoais que são convidadas para um locatário do Azure AD não podem usar o ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para esse cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisarem usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra o fluxo ROPC.

![Diagrama mostrando o fluxo de credenciais de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Pedido de autorização

O fluxo ROPC é uma única solicitação: ele envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe tokens em retorno. O cliente deve solicitar o endereço de email (UPN) do usuário e a senha antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar esta solicitação no postmaster!
> [![Tente executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required. 

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
| `tenant` | Requerido | O locatário do diretório no qual você deseja registrar o usuário. Isso pode estar no formato de nome amigável ou GUID. Esse parâmetro não pode ser definido `common` como `consumers`ou, mas pode ser definido `organizations`como. |
| `client_id` | Requerido | A ID do aplicativo (cliente) que a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. | 
| `grant_type` | Requerido | Tem de ser definido como `password`. |
| `username` | Requerido | O endereço de email do usuário. |
| `password` | Requerido | A senha do usuário. |
| `scope` | Recomendado | Uma lista separada por espaços de [](v2-permissions-and-consent.md)escopos, ou permissões, que o aplicativo requer. Em um fluxo interativo, o administrador ou o usuário deve consentir esses escopos antes do tempo. |
| `client_secret`| Às vezes é necessário | Se seu aplicativo for um cliente público, o ou `client_secret` `client_assertion` o não poderá ser incluído.  Se o aplicativo for um cliente confidencial, ele deverá ser incluído. | 
| `client_assertion` | Às vezes é necessário | Uma forma diferente de `client_secret`, gerada usando um certificado.  Consulte [credenciais de certificado](active-directory-certificate-credentials.md) para obter mais detalhes. | 

### <a name="successful-authentication-response"></a>Resposta de autenticação bem-sucedida

O exemplo a seguir mostra uma resposta de token bem-sucedida:

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
| `token_type` | Cadeia | Sempre definido como `Bearer`. |
| `scope` | Cadeias de caracteres separadas por espaço | Se um token de acesso for retornado, esse parâmetro listará os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [](v2-permissions-and-consent.md) escopos que foram solicitados. |
| `id_token` | JWT | Emitido se o parâmetro `scope` original incluía `openid` o escopo. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido se o parâmetro `scope` original estiver `offline_access`incluído. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo descrito na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não forneceu o nome de usuário ou a senha correta ou o cliente não recebeu o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | Falha na autenticação | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um `consent_required` erro será retornado. Se isso ocorrer, o cliente deverá enviar o usuário para um prompt interativo usando uma WebView ou um navegador. |
| `invalid_request` | A solicitação foi construída de forma inadequada | Não há suporte para o tipo Grant `/common` nos `/consumers` contextos de autenticação ou.  Use `/organizations` ou uma ID de locatário em vez disso. |

## <a name="learn-more"></a>Saber mais

* Experimente o ROPC sozinho usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se você deve usar o ponto de extremidade v 2.0, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).
