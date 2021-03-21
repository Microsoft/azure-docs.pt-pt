---
title: Defina um perfil técnico OAuth1 numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico OAuth 1.0 numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7a2cbca8c02dcbfc0e59d31040d1fca7a790cd35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96936663"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OAuth1 numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio ao fornecedor de identidade [do protocolo OAuth 1.0.](https://tools.ietf.org/html/rfc5849) Este artigo descreve as especificidades de um perfil técnico para interagir com um fornecedor de sinistros que suporta este protocolo padronizado. Com um perfil técnico da OAuth1, pode federar com um fornecedor de identidade baseado em OAuth1, como o Twitter. A federação com o fornecedor de identidade permite que os utilizadores assinem com as suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `OAuth1` . Por exemplo, o protocolo para o perfil técnico **do Twitter-OAUTH1** é `OAuth1` .

```xml
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

Os **elementos InputClaims** e **InputClaimsTransformations**  estão vazios ou ausentes.

## <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade OAuth1. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade desde que desemisse o atributo **DefaultValue.**

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

O exemplo a seguir mostra as reclamações devolvidas pelo fornecedor de identidade do Twitter:

- A **user_id** alegação que está mapeada para a alegação **da EmitenteUSerId.**
- A **screen_name** alegação que está mapeada para a **alegação do Nome de Exibição.**
- A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve alegações que não são devolvidas pelo fornecedor de identidade:

- A **identidadeProvider** afirma que contém o nome do fornecedor de identidade.
- A **autenticação AlegaçãoSource** com um valor predefinido de `socialIdpAuthentication` .

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

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_id | Yes | O identificador de aplicação do fornecedor de identidade. |
| ProviderName | No | O nome do fornecedor de identidade. |
| request_token_endpoint | Yes | O URL do ponto final do token de pedido de acordo com RFC 5849. |
| authorization_endpoint | Yes | O URL do ponto final de autorização de acordo com o RFC 5849. |
| access_token_endpoint | Yes | O URL do ponto final simbólico de acordo com o RFC 5849. |
| ReclamaçõesEndpoint | No | O URL do ponto final de informação do utilizador. |
| ReclamaçõesResponseFormat | No | O formato de resposta de reclamações.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Yes | O segredo do cliente da aplicação do fornecedor de identidade.   |

## <a name="redirect-uri"></a>URI de Redirecionamento

Quando configurar o URI de redirecionamento do seu fornecedor de identidade, insira `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/{policy-id}/oauth1/authresp` . Certifique-se de substituir `{tenant-name}` pelo nome do seu inquilino (por exemplo, contosob2c) e pelo `{policy-id}` identificador da sua política (por exemplo, b2c_1a_policy). O URI de redirecionamento tem de estar em todas as minúsculas. Adicione um URL de redirecionamento para todas as políticas que utilizem o login do fornecedor de identidade.

Exemplos:

- [Adicione o Twitter como um fornecedor de identidade OAuth1 usando políticas personalizadas](identity-provider-twitter.md)
