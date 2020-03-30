---
title: Defina um perfil técnico OAuth1 numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico OAuth 1.0 numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184082"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OAuth1 numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio ao fornecedor de identidade do [protocolo OAuth 1.0.](https://tools.ietf.org/html/rfc5849) Este artigo descreve as especificidades de um perfil técnico para interagir com um fornecedor de sinistros que suporta este protocolo padronizado. Com um perfil técnico OAuth1, pode federar-se com um fornecedor de identidade baseado em OAuth1, como o Twitter. A federação com o fornecedor de identidade permite que os utilizadores assinem com as suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `OAuth1`tem de ser definido para . Por exemplo, o protocolo para o perfil técnico `OAuth1` **Twitter-OAUTH1** é .

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** estão vazios ou ausentes.

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade OAuth1. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não sejam devolvidas pelo fornecedor de identidade desde que detetete o atributo **DefaultValue.**

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

O exemplo que se segue mostra as alegações devolvidas pelo fornecedor de identidade do Twitter:

- A **user_id** alegação que está mapeada para a alegação do **emitenteUserId.**
- A **screen_name** afirmação que está mapeada para a alegação de nome de **exibição.**
- A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve reclamações que não são devolvidas pelo fornecedor de identidade:

- A **alegação do Fornecedor de Identidade** que contém o nome do fornecedor de identidade.
- A **autenticaçãoSource** reclama com `socialIdpAuthentication`um valor predefinido de .

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

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador de aplicação do fornecedor de identidade. |
| ProviderName | Não | O nome do fornecedor de identidade. |
| request_token_endpoint | Sim | O URL do ponto final simbólico do pedido de acordo com o RFC 5849. |
| authorization_endpoint | Sim | O URL do ponto final de autorização de acordo com o RFC 5849. |
| access_token_endpoint | Sim | O URL do ponto final simbólico de acordo com o RFC 5849. |
| Ponto final de reclamações | Não | O URL do ponto final da informação do utilizador. |
| Formato Resposta a Reclamações | Não | O formato de resposta a sinistros.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade.   |

## <a name="redirect-uri"></a>URI de Redirecionamento

Quando configurar o URL redirecionado do `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`seu fornecedor de identidade, introduza . Certifique-se de substituir o **inquilino** pelo seu nome de inquilino (por exemplo, contosob2c.onmicrosoft.com) e **políticaId** pelo identificador da sua política (por exemplo, b2c_1a_policy). O URI redirecionado tem de estar em todas as minúsculas. Adicione um URL de redirecionamento para todas as políticas que utilizem o login do fornecedor de identidade.

Se estiver a utilizar o domínio **b2clogin.com** em vez de **login.microsoftonline.com** certifique-se de utilizar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicione o Twitter como um fornecedor de identidade OAuth1 usando políticas personalizadas](identity-provider-twitter-custom.md)













