---
title: Inscreva-se com credenciais de senha do proprietário de recursos conceder | Rio Azure
titleSuffix: Microsoft identity platform
description: Suporte fluxos de autenticação sem navegador utilizando a credencial de credencial de senha do proprietário do recurso (ROPC).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bf469b79fa532978e904a54f32c80280706ee7cb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174585"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Plataforma de identidade microsoft e credenciais de senha do proprietário de recursos OAuth 2.0

A plataforma de identidade da Microsoft suporta a [concessão de Credenciais de Senha do Proprietário de Recursos OAuth 2.0 (ROPC),](https://tools.ietf.org/html/rfc6749#section-4.3)que permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe.  Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Quando possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas para [adquirir fichas e chamar APIs web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dê também uma olhada nas aplicações de [amostra que utilizam o MSAL.](sample-v2-code.md)

> [!WARNING]
> A Microsoft recomenda que _não_ utilize o fluxo ROPC. Na maioria dos cenários, alternativas mais seguras estão disponíveis e recomendadas. Este fluxo requer um elevado grau de confiança na aplicação e comporta riscos que não estão presentes noutros fluxos. Só deve utilizar este fluxo quando não puderem ser utilizados outros fluxos mais seguros.

> [!IMPORTANT]
>
> * A plataforma de identidade da Microsoft apenas suporta ROPC para inquilinos AZure AD, não contas pessoais. Isto significa que você deve usar um ponto final específico do inquilino `https://login.microsoftonline.com/{TenantId_or_Name}` () ou o `organizations` ponto final.
> * As contas pessoais que são convidadas para um inquilino da AD Azure não podem usar o ROPC.
> * As contas que não têm senhas não podem entrar através do ROPC. Para este cenário, recomendamos que utilize um fluxo diferente para a sua aplicação.
> * Se os utilizadores precisarem de utilizar a [autenticação de vários fatores (MFA)](../authentication/concept-mfa-howitworks.md) para iniciar sessão na aplicação, serão bloqueados.
> * O ROPC não é suportado em cenários [de federação de identidade híbrida](../hybrid/whatis-fed.md) (por exemplo, Azure AD e ADFS usados para autenticar contas no local). Se os utilizadores forem redirecionados para um fornecedor de identidade no local, o Azure AD não é capaz de testar o nome de utilizador e a palavra-passe contra esse fornecedor de identidade. No entanto, [a autenticação pass-through](../hybrid/how-to-connect-pta.md) é suportada com ROPC.
> * Uma exceção a um cenário de federação de identidade híbrida seria o seguinte: A política home Realm Discovery com AllowCloudPasswordValidation definida para TRUE permitirá que o fluxo ROPC funcione para utilizadores federados quando a palavra-passe no local estiver sincronizada na nuvem. Para obter mais informações, consulte [Ativar a autenticação direta ropc dos utilizadores federados para aplicações antigas.](../manage-apps/configure-authentication-for-federated-users-portal.md#enable-direct-ropc-authentication-of-federated-users-for-legacy-applications)

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra o fluxo ROPC.

![Diagrama mostrando o fluxo de credencial de senha do proprietário do recurso](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Pedido de autorização

O fluxo ROPC é um único pedido: envia a identificação do cliente e as credenciais do utilizador para o IDP, e depois recebe fichas em troca. O cliente deve solicitar o endereço de e-mail do utilizador (UPN) e a palavra-passe antes de o fazer. Imediatamente após um pedido bem sucedido, o cliente deve libertar de forma segura as credenciais do utilizador da memória. Nunca deve salvá-los.

> [!TIP]
> Tente executar este pedido no Carteiro!
> [![Tente executar este pedido no Carteiro](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
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
| `tenant` | Obrigatório | O inquilino do diretório em que quer entrar no utilizador. Isto pode ser em formato GUIADO ou friendly name. Este parâmetro não pode ser definido `common` para ou , mas pode ser definido para `consumers` `organizations` . |
| `client_id` | Necessário | O ID da Aplicação (cliente) que o [portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `grant_type` | Necessário | Deve ser definido para `password` . |
| `username` | Necessário | O endereço de correio eletrónico do utilizador. |
| `password` | Necessário | A senha do utilizador. |
| `scope` | Recomendado | Uma lista de [âmbitos separados](v2-permissions-and-consent.md)pelo espaço , ou permissões, que a aplicação necessita. Num fluxo interativo, o administrador ou o utilizador devem consentir com estes âmbitos com antecedência. |
| `client_secret`| Às vezes necessário | Se a sua aplicação é um cliente público, então a `client_secret` ou não pode ser `client_assertion` incluída.  Se a aplicação é um cliente confidencial, então deve ser incluída. |
| `client_assertion` | Às vezes necessário | Uma forma diferente `client_secret` de, gerada usando um certificado.  Consulte [as credenciais de certificado](active-directory-certificate-credentials.md) para mais detalhes. |

### <a name="successful-authentication-response"></a>Resposta de autenticação bem sucedida

O exemplo a seguir mostra uma resposta simbólica bem sucedida:

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
| `token_type` | String | Sempre definido para `Bearer` . |
| `scope` | Cadeias separadas do espaço | Se um token de acesso foi devolvido, este parâmetro lista os âmbitos para os quais o token de acesso é válido. |
| `expires_in`| int | Número de segundos para o qual o token de acesso incluído é válido. |
| `access_token`| Corda opaca | Emitido para os [âmbitos](v2-permissions-and-consent.md) que foram solicitados. |
| `id_token` | JWT | Emitido se o parâmetro original `scope` incluísse o `openid` âmbito. |
| `refresh_token` | Corda opaca | Emitido se o parâmetro original `scope` `offline_access` incluído. |

Pode utilizar o token refresh para adquirir novos tokens de acesso e fichas de atualização utilizando o mesmo fluxo descrito na documentação de fluxo do [Código OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).

### <a name="error-response"></a>Resposta de erro

Se o utilizador não tiver fornecido o nome de utilizador ou palavra-passe correto, ou se o cliente não tiver recebido o consentimento solicitado, a autenticação falhará.

| Erro | Descrição | Ação do cliente |
|------ | ----------- | -------------|
| `invalid_grant` | A autenticação falhou | As credenciais estavam incorretas ou o cliente não tem consentimento para os âmbitos solicitados. Se os telescópios não forem concedidos, será devolvido um `consent_required` erro. Se isso ocorrer, o cliente deve enviar o utilizador para um pedido interativo usando uma webview ou browser. |
| `invalid_request` | O pedido foi mal construído | O tipo de subvenção não é suportado nos `/common` contextos ou `/consumers` autenticação.  Use `/organizations` ou uma identificação de inquilino em vez disso. |

## <a name="learn-more"></a>Saber mais

Para um exemplo de utilização do ROPC, consulte a amostra de código de [aplicação da consola .NET Core](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) no GitHub.
