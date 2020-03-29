---
title: Inscreva-se com a concessão de credenciais de senha do proprietário de recursos / Azure
titleSuffix: Microsoft identity platform
description: Suporte fluxos de autenticação sem navegador utilizando a bolsa de credencial de senha do proprietário de recursos (ROPC).
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
ms.openlocfilehash: b935ad2491ca486a3bc6878f0332e5390600b1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700690"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Plataforma de identidade da Microsoft e Credenciais de senha do proprietário de recursos OAuth 2.0

A plataforma de identidade da Microsoft suporta a concessão de credenciais de senha do proprietário de [recursos OAuth 2.0 (ROPC),](https://tools.ietf.org/html/rfc6749#section-4.3)que permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe.  Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Sempre que possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas em vez de adquirir fichas e ligar para [APIs web protegidos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também as [aplicações de amostra que utilizam o MSAL.](sample-v2-code.md)

> [!WARNING]
> A Microsoft recomenda que _não_ utilize o fluxo ROPC. Na maioria dos cenários, alternativas mais seguras estão disponíveis e recomendadas. Este fluxo requer um grau de confiança muito elevado na aplicação, e apresenta riscos que não estão presentes noutros fluxos. Só deve utilizar este fluxo quando outros fluxos mais seguros não podem ser utilizados.

> [!IMPORTANT]
>
> * O ponto final da plataforma de identidade da Microsoft apenas suporta ROPC para inquilinos da AD Azure, não contas pessoais. Isto significa que você deve usar um`https://login.microsoftonline.com/{TenantId_or_Name}`ponto `organizations` final específico do inquilino ( ) ou o ponto final.
> * As contas pessoais que são convidadas para um inquilino da AD Azure não podem usar ropc.
> * Contas que não têm senhas não podem entrar através do ROPC. Para este cenário, recomendamos que utilize um fluxo diferente para a sua aplicação.
> * Se os utilizadores precisarem de utilizar a autenticação de vários fatores (MFA) para iniciar sessão na aplicação, serão bloqueados.
> * A ROPC não é suportada em cenários de federação de [identidade híbrida](/azure/active-directory/hybrid/whatis-fed) (por exemplo, AD Azure e ADFS usadas para autenticar contas no local). Se os utilizadores forem redirecionados para um fornecedor de identidade no local, a Azure AD não é capaz de testar o nome de utilizador e a palavra-passe contra esse fornecedor de identidade. No entanto, a [autenticação pass-through](/azure/active-directory/hybrid/how-to-connect-pta) é suportada com ropc.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra o fluxo ROPC.

![Diagrama mostrando o fluxo credencial de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Pedido de autorização

O fluxo ROPC é um único pedido: envia a identificação do cliente e as credenciais do utilizador para o IDP, e depois recebe fichas em troca. O cliente deve solicitar o endereço de e-mail do utilizador (UPN) e a palavra-passe antes de o fazer. Imediatamente após um pedido bem sucedido, o cliente deve libertar as credenciais do utilizador de forma segura da memória. Nunca deve salvá-los.

> [!TIP]
> Tente executar este pedido no Carteiro!
> [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


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
| `tenant` | Necessário | O inquilino de diretório em que quer registar o utilizador. Isto pode ser em formato DE NOME GUIA ou amigável. Este parâmetro não pode ser `common` `consumers`definido para ou `organizations`, mas pode ser definido para . |
| `client_id` | Necessário | O ID de Aplicação (cliente) que o [portal Azure - Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações atribuída à sua app. | 
| `grant_type` | Necessário | Tem de `password`ser definido para. |
| `username` | Necessário | O endereço de correio eletrónico do utilizador. |
| `password` | Necessário | A senha do utilizador. |
| `scope` | Recomendado | Uma lista de [âmbitos separados](v2-permissions-and-consent.md)do espaço, ou permissões, que a aplicação necessita. Num fluxo interativo, o administrador ou o utilizador devem consentir com estes âmbitos com antecedência. |
| `client_secret`| Às vezes necessário | Se a sua aplicação for `client_secret` `client_assertion` um cliente público, então a ou não pode ser incluída.  Se a aplicação é um cliente confidencial, então deve ser incluída. | 
| `client_assertion` | Às vezes necessário | Uma forma `client_secret`diferente de, gerada com um certificado.  Consulte [as credenciais](active-directory-certificate-credentials.md) de certificado para mais detalhes. | 

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

O exemplo que se segue mostra uma resposta simbólica bem sucedida:

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
| `token_type` | Cadeia | Sempre definido `Bearer`para . |
| `scope` | Cordas separadas do espaço | Se um token de acesso tiver sido devolvido, este parâmetro lista os âmbitos para os que o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o que o token de acesso incluído é válido para. |
| `access_token`| Corda opaca | Emitido para os [âmbitos solicitados.](v2-permissions-and-consent.md) |
| `id_token` | JWT | Emitido se o `scope` parâmetro original `openid` incluía o âmbito. |
| `refresh_token` | Corda opaca | Emitido se o `scope` parâmetro original `offline_access`incluído . |

Pode utilizar o token de atualização para adquirir novos tokens de acesso e fichas de atualização utilizando o mesmo fluxo descrito na documentação de fluxo do [Código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o utilizador não tiver fornecido o nome de utilizador ou palavra-passe correto, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os âmbitos solicitados. Se os âmbitos não forem concedidos, um `consent_required` erro será devolvido. Se isso ocorrer, o cliente deve enviar o utilizador para um pedido interativo usando um webview ou navegador. |
| `invalid_request` | O pedido foi mal construído | O tipo de subvenção não `/common` `/consumers` é suportado nos contextos de autenticação ou autenticação.  Use `/organizations` ou uma identificação de inquilino. |

## <a name="learn-more"></a>Saiba mais

* Experimente roPC por si mesmo utilizando a aplicação da [consola de amostras](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2).
* Para determinar se deve utilizar o ponto final v2.0, leia sobre [as limitações](active-directory-v2-limitations.md)da plataforma de identidade da Microsoft .
