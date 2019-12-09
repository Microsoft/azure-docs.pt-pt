---
title: Entrar com concessão de credenciais de senha do proprietário do recurso | Azure
titleSuffix: Microsoft identity platform
description: Suporte a fluxos de autenticação sem navegador usando a concessão de credencial de senha do proprietário do recurso (ROPC).
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
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24c6bfdc7efc8f15378d4a126b978bc77741b43c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919329"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Plataforma de identidade da Microsoft e credenciais de senha do proprietário do recurso OAuth 2,0

A plataforma de identidade da Microsoft dá suporte à [concessão de credenciais de senha (ROPC) do proprietário do recurso OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.3), que permite que um aplicativo entre no usuário manipulando sua senha diretamente.  Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

> [!WARNING]
> A Microsoft recomenda que você _não_ use o fluxo ROPC. Na maioria dos cenários, as alternativas mais seguras estão disponíveis e recomendadas. Esse fluxo requer um grau muito alto de confiança no aplicativo e traz riscos que não estão presentes em outros fluxos. Você só deve usar esse fluxo quando outros fluxos mais seguros não puderem ser usados.

> [!IMPORTANT]
>
> * O ponto de extremidade da plataforma Microsoft Identity suporta apenas ROPC para locatários do Azure AD, não contas pessoais. Isso significa que você deve usar um ponto de extremidade específico do locatário (`https://login.microsoftonline.com/{TenantId_or_Name}`) ou o ponto de extremidade `organizations`.
> * As contas pessoais que são convidadas para um locatário do Azure AD não podem usar o ROPC.
> * Contas que não têm senhas não podem entrar por meio de ROPC. Para esse cenário, recomendamos que você use um fluxo diferente para seu aplicativo em vez disso.
> * Se os usuários precisarem usar a autenticação multifator (MFA) para fazer logon no aplicativo, eles serão bloqueados em vez disso.
> * Não há suporte para ROPC em cenários de [Federação de identidade híbrida](/azure/active-directory/hybrid/whatis-fed) (por exemplo, AD do Azure e ADFS usados para autenticar contas locais). Se os usuários forem redirecionados de página inteira para provedores de identidade locais, o Azure AD não poderá testar o nome de usuário e a senha nesse provedor de identidade. No entanto, há suporte para a [autenticação de passagem](/azure/active-directory/hybrid/how-to-connect-pta) com ROPC.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra o fluxo ROPC.

![Diagrama mostrando o fluxo de credenciais de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Solicitação de autorização

O fluxo ROPC é uma única solicitação: ele envia a identificação do cliente e as credenciais do usuário para o IDP e, em seguida, recebe tokens em retorno. O cliente deve solicitar o endereço de email (UPN) do usuário e a senha antes de fazer isso. Imediatamente após uma solicitação bem-sucedida, o cliente deve liberar com segurança as credenciais do usuário da memória. Ele nunca deve salvá-los.

> [!TIP]
> Tente executar esta solicitação no postmaster!
> [![tentar executar esta solicitação no postmaster](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Obrigatório | O locatário do diretório no qual você deseja registrar o usuário. Isso pode estar no formato de nome amigável ou GUID. Este parâmetro não pode ser definido como `common` ou `consumers`, mas pode ser definido como `organizations`. |
| `client_id` | Obrigatório | A ID do aplicativo (cliente) que a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. | 
| `grant_type` | Obrigatório | Tem de ser definido como `password`. |
| `username` | Obrigatório | O endereço de correio eletrónico do utilizador. |
| `password` | Obrigatório | A senha do usuário. |
| `scope` | Recomendado | Uma lista separada por espaços de [escopos](v2-permissions-and-consent.md), ou permissões, que o aplicativo requer. Em um fluxo interativo, o administrador ou o usuário deve consentir esses escopos antes do tempo. |
| `client_secret`| Às vezes é necessário | Se seu aplicativo for um cliente público, o `client_secret` ou `client_assertion` não poderá ser incluído.  Se o aplicativo for um cliente confidencial, ele deverá ser incluído. | 
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
| `token_type` | String | Sempre definido como `Bearer`. |
| `scope` | Cadeias de caracteres separadas por espaço | Se um token de acesso for retornado, esse parâmetro listará os escopos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Cadeia de caracteres opaca | Emitido para os [escopos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido se o parâmetro de `scope` original incluía o escopo de `openid`. |
| `refresh_token` | Cadeia de caracteres opaca | Emitido se o parâmetro de `scope` original for incluído `offline_access`. |

Você pode usar o token de atualização para adquirir novos tokens de acesso e atualizar tokens usando o mesmo fluxo descrito na [documentação do fluxo de código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o usuário não forneceu o nome de usuário ou a senha correta ou o cliente não recebeu o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | Falha na autenticação | As credenciais estavam incorretas ou o cliente não tem consentimento para os escopos solicitados. Se os escopos não forem concedidos, um erro de `consent_required` será retornado. Se isso ocorrer, o cliente deverá enviar o usuário para um prompt interativo usando uma WebView ou um navegador. |
| `invalid_request` | A solicitação foi construída de forma inadequada | O tipo Grant não tem suporte nos contextos de autenticação `/common` ou `/consumers`.  Em vez disso, use `/organizations` ou uma ID de locatário. |

## <a name="learn-more"></a>Saber mais

* Experimente o ROPC sozinho usando o [aplicativo de console de exemplo](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se você deve usar o ponto de extremidade v 2.0, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).
