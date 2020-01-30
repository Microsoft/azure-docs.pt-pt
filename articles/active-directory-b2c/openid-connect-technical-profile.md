---
title: Defina um perfil técnico OpenID Connect numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico OpenID Connect numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8bda1d3bcce37cbb7b5306d460bddd4652349fe9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840354"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OpenID Connect numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte ao fornecedor de identidade do protocolo [OpenID Connect.](https://openid.net/2015/04/17/openid-connect-certification-program/) OpenID Connect 1.0 define uma camada de identidade em cima da OAuth 2.0 e representa o estado da arte nos protocolos modernos de autenticação. Com um perfil técnico OpenID Connect, pode federar-se com um fornecedor de identidade baseado em OpenID Connect, como o Azure AD. Federar com um fornecedor de identidade permite que os utilizadores inscrevam-se com as suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O **nome** atributo do elemento **protocolo** tem de ser definido para `OpenIdConnect`. Por exemplo, o protocolo para o perfil técnico **MSA-OIDC** é `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas pode querer enviar parâmetros adicionais ao seu fornecedor de identidade. O exemplo seguinte adiciona o parâmetro de cadeia de consulta **domain_hint** com o valor de `contoso.com` ao pedido de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade OpenID Connect. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade, desde que estabeleça o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

O exemplo seguinte mostra as reclamações devolvidas pelo fornecedor de identidade da Conta Microsoft:

- A **alegação sub** que está mapeada para a alegação do **emitenteUserId.**
- O **nome** afirma que está mapeado na alegação de nome de **exibição.**
- O **e-mail** sem mapeamento de nome.

O perfil técnico também devolve reclamações que não são devolvidas pelo fornecedor de identidade:

- A **alegação do Fornecedor de Identidade** que contém o nome do fornecedor de identidade.
- A **autenticaçãoSource** reclama com um valor predefinido de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador de aplicação do fornecedor de identidade. |
| IdTokenAudience | Não | O público do id_token. Se especificado, o Azure AD B2C verifica se o token está numa reclamação devolvida pelo fornecedor de identidade e é igual à especificada. |
| METADADOS | Sim | Um URL que aponta para um documento de configuração JSON formatado de acordo com a especificação OpenID Connect Discovery, que também é conhecida como um ponto final de configuração aberta bem conhecido. |
| Nome do provedor | Não | O nome do fornecedor de identidade. |
| response_types | Não | O tipo de resposta de acordo com a especificação OpenID Connect Core 1.0. Valores possíveis: `id_token`, `code`ou `token`. |
| response_mode | Não | O método que o fornecedor de identidade utiliza para enviar o resultado de volta para O Azure AD B2C. Valores possíveis: `query`, `form_post` (predefinido) ou `fragment`. |
| scope | Não | O âmbito do pedido definido de acordo com a especificação OpenID Connect Core 1.0. Como `openid`, `profile`e `email`. |
| HttpBinding | Não | O http esperado que se liga ao token de acesso e reclama pontos finais simbólicos. Valores possíveis: `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Não | Uma chave que pode ser usada para iniciar sessão em cada um dos inquilinos quando utilizar um fornecedor de identidade multi-inquilino, como o Azure Ative Directory. |
| UsePolicyInRedirectUri | Não | Indica se deve utilizar uma política na construção do URI redirecionado. Quando configurar a sua aplicação no fornecedor de identidade, tem de especificar o URI redirecionado. O redirecionamento URI aponta para Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Se especificar `false`, terá de adicionar um URI redirecionado para cada apólice que utilizar. Por exemplo: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não | Indica se um pedido a um serviço externo deve ser marcado como uma falha se o código de estado http estiver na gama 5xx. A predefinição é `false`. |
| DiscoverMetadataByTokenIssuer | Não | Indica se os metadados oIDC devem ser descobertos utilizando o emitente no símbolo JWT. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade. A chave criptográfica só é necessária se os metadados **response_types** estiverem definidos para `code`. Neste caso, o Azure AD B2C faz outra chamada para trocar o código de autorização por um sinal de acesso. Se os metadados estiverem definidos para `id_token` pode omitir a chave criptográfica.  |

## <a name="redirect-uri"></a>Redirecione Uri

Quando configurar o URI redirecionamento do seu fornecedor de identidade, introduza `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Certifique-se de substituir `{your-tenant-name}` pelo nome do seu inquilino. O URI redirecionado tem de estar em todas as minúsculas.

Exemplos:

- [Adicione a Conta Microsoft (MSA) como um fornecedor de identidade usando políticas personalizadas](identity-provider-microsoft-account-custom.md)
- [Inscreva-se usando contas Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Permitir que os utilizadores inscrevam-se num fornecedor de identidade Azure AD multi-inquilino usando políticas personalizadas](identity-provider-azure-ad-multi-tenant-custom.md)
