---
title: Definir um perfil técnico do OAuth1 em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico do OAuth1 em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 97fa5757f8b77e29545f6d6f6b885334c7b526f1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063995"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OAuth1 em uma política personalizada Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade do [protocolo OAuth 1,0](https://tools.ietf.org/html/rfc5849) . Este artigo descreve as especificidades de um perfil técnico para interagir com um provedor de declarações que dá suporte a esse protocolo padronizado. Com um perfil técnico do OAuth1, você pode federar com um provedor de identidade baseado em OAuth1, como o Twitter. A Federação com o provedor de identidade permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocol

O atributo **Name** do elemento **Protocol** precisa ser definido como `OAuth1`. Por exemplo, o protocolo para o perfil técnico do **Twitter-OAUTH1** é `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** estão vazios ou ausentes.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade OAuth1. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade contanto que você defina o atributo **DefaultValue** .

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade do Twitter:

- A Declaração **user_id** que é mapeada para a Declaração **issuerUserId** .
- A Declaração **screen_name** que é mapeada para a Declaração **DisplayName** .
- A declaração de **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A Declaração **identityprovider** que contém o nome do provedor de identidade.
- A declaração de **pré-autenticação** com um valor padrão de `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador do aplicativo do provedor de identidade. |
| ProviderName | Não | O nome do provedor de identidade. |
| request_token_endpoint | Sim | A URL do ponto de extremidade do token de solicitação de acordo com a RFC 5849. |
| authorization_endpoint | Sim | A URL do ponto de extremidade de autorização de acordo com a RFC 5849. |
| access_token_endpoint | Sim | A URL do ponto de extremidade do token de acordo com a RFC 5849. |
| ClaimsEndpoint | Não | A URL do ponto de extremidade de informações do usuário. |
| ClaimsResponseFormat | Não | O formato de resposta de declarações.|

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente do aplicativo de provedor de identidade.   |

## <a name="redirect-uri"></a>URI de Redirecionamento

Ao configurar a URL de redirecionamento do seu provedor de `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`identidade, digite. Certifique-se de substituir o **locatário** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com) e **PolicyId** pelo identificador da política (por exemplo, b2c_1a_policy). O URI de redirecionamento precisa estar em letras minúsculas. Adicione uma URL de redirecionamento para todas as políticas que usam o logon do provedor de identidade.

Se você estiver usando o domínio **b2clogin.com** em vez de **login.microsoftonline.com** , certifique-se de usar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar o Twitter como um provedor de identidade OAuth1 usando políticas personalizadas](active-directory-b2c-custom-setup-twitter-idp.md)













