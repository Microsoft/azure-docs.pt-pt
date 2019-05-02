---
title: Definir um perfil técnico de OAuth1 numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico de OAuth1 numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7b3d579e9d4ceb92ee961778ba6083292461c144
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699832"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de OAuth1 em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para o [protocolo OAuth 1.0](https://tools.ietf.org/html/rfc5849) fornecedor de identidade. Este artigo descreve as especificações de um perfil técnico da interagir com um fornecedor de afirmações que suporte esse protocolo padronizado. Com um perfil de técnico de OAuth1, pode federar com um fornecedor de identidade com base de OAuth1, como o Twitter. Federação com o fornecedor de identidade permite aos utilizadores iniciar sessão com existente social ou as identidades de empresa.

## <a name="protocol"></a>Protocolo

O **Name** atributo da **protocolo** elemento tem de ser definido como `OAuth1`. Por exemplo, o protocolo para o **Twitter OAUTH1** é o perfil técnico `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>Afirmações de entrada

O **InputClaims** e **InputClaimsTransformations** elementos estão vazios ou ausentes.

## <a name="output-claims"></a>Afirmações de saída

O **OutputClaims** elemento contém uma lista das declarações devolvido pelo fornecedor de identidade de OAuth1. Terá de mapear o nome da afirmação definido na sua política para o nome definido no fornecedor de identidade. Também pode incluir declarações que não são devolvidas pelo fornecedor de identidade, desde que definir o **DefaultValue** atributo.

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar as afirmações de saída ou gerar novas etiquetas.

O exemplo seguinte mostra as afirmações devolvidas pelo fornecedor de identidade de Twitter:

- O **user_id** afirmação que está mapeada para o **issuerUserId** de afirmação.
- O **screen_name** afirmação que está mapeada para o **displayName** de afirmação.
- O **e-mail** sem mapeamento de nome de afirmação.

O perfil técnico também retorna afirmações que não são devolvidas pelo fornecedor de identidade: 

- O **identityProvider** afirmação que contém o nome do fornecedor de identidade.
- O **authenticationSource** a afirmação com um valor padrão de `socialIdpAuthentication`.

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
| request_token_endpoint | Sim | O URL do ponto de extremidade de token de pedido de acordo com a RFC 5849. |
| authorization_endpoint | Sim | O URL do ponto final de autorização de acordo com a RFC 5849. |
| access_token_endpoint | Sim | O URL do ponto final do token de acordo com a RFC 5849. |
| ClaimsEndpoint | Não | O URL do ponto de extremidade de informações do utilizador. | 
| ClaimsResponseFormat | Não | O formato de resposta de afirmações.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O **CryptographicKeys** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade.   | 

## <a name="redirect-uri"></a>URI de Redirecionamento

Quando configurar o URL de redirecionamento do seu fornecedor de identidade, introduza `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Certifique-se de substituir **inquilino** pelo nome do seu inquilino (por exemplo, contosob2c.onmicrosoft.com) e **policyId** com o identificador da sua política (por exemplo, b2c_1a_policy). O URI de redirecionamento tem de ser em letras minúsculas. Adicione um URL de redirecionamento para todas as políticas que utilizam o início de sessão do fornecedor de identidade. 

Se estiver a utilizar o **b2clogin.com** domínio em vez de **login.microsoftonline.com** certificar-se de que utilizar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar o Twitter como fornecedor de identidade de OAuth1 ao utilizar políticas personalizadas](active-directory-b2c-custom-setup-twitter-idp.md)













