---
title: Defina um perfil técnico OpenID Connect numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico OpenID Connect numa política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332769"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OpenID Connect numa política personalizada do Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) fornece suporte ao fornecedor de identidade do protocolo [OpenID Connect.](https://openid.net/2015/04/17/openid-connect-certification-program/) OpenID Connect 1.0 define uma camada de identidade em cima da OAuth 2.0 e representa o estado da arte nos protocolos modernos de autenticação. Com um perfil técnico OpenID Connect, pode federar-se com um fornecedor de identidade baseado em OpenID Connect, como o Azure AD. Federar com um fornecedor de identidade permite que os utilizadores inscrevam-se com as suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O **atributo** nome do elemento **protocolo** `OpenIdConnect`tem de ser definido para . Por exemplo, o protocolo para o perfil técnico `OpenIdConnect` **MSA-OIDC** é:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas pode querer enviar parâmetros adicionais ao seu fornecedor de identidade. O exemplo seguinte adiciona o parâmetro **de** cadeia `contoso.com` de consulta domain_hint com o valor do pedido de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Reclamações de produção

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade OpenID Connect. Poderá ter de mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não são devolvidas pelo fornecedor `DefaultValue` de identidade, desde que detete o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **outputClaimsTransformation** que são usados para modificar as reclamações de saída ou gerar novos.

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

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador de aplicação do fornecedor de identidade. |
| IdTokenAudience | Não | O público do id_token. Se especificado, o Azure AD B2C verifica se a `aud` reclamação num token devolvido pelo fornecedor de identidade é igual à especificada nos metadados IdTokenAudience.  |
| METADADOS | Sim | Um URL que aponta para um documento de configuração do fornecedor de identidade OpenID Connect, que também é conhecido como ponto final de configuração conhecido do OpenID. O URL pode `{tenant}` conter a expressão, que é substituída pelo nome do inquilino.  |
| authorization_endpoint | Não | Um URL que aponta para um ponto final de autorização de autorização de configuração do fornecedor de identidade OpenID Connect. O valor dos metadados authorization_endpoint `authorization_endpoint` tem precedência sobre o especificado no ponto final de configuração bem conhecido do OpenID. O URL pode `{tenant}` conter a expressão, que é substituída pelo nome do inquilino. |
| issuer | Não | O identificador único de um fornecedor de identidade OpenID Connect. O valor dos metadados emitentes tem precedência sobre o `issuer` especificado no ponto final de configuração bem conhecido do OpenID.  Se especificado, o Azure AD B2C verifica se a `iss` reclamação num token devolvido pelo fornecedor de identidade é igual à especificada nos metadados emitentes. |
| ProviderName | Não | O nome do fornecedor de identidade.  |
| response_types | Não | O tipo de resposta de acordo com a especificação OpenID Connect Core 1.0. Valores `id_token`possíveis: , `code`ou `token`. |
| response_mode | Não | O método que o fornecedor de identidade utiliza para enviar o resultado de volta para O Azure AD B2C. Valores `query`possíveis: `form_post` , `fragment`(predefinido) ou . |
| scope | Não | O âmbito do pedido definido de acordo com a especificação OpenID Connect Core 1.0. Tais `openid`como, `profile` `email`e . |
| HttpBinding | Não | O http esperado que se liga ao token de acesso e reclama pontos finais simbólicos. Valores `GET` possíveis: ou `POST`.  |
| ValidTokenIssuerPrefixes | Não | Uma chave que pode ser usada para iniciar sessão em cada um dos inquilinos quando utilizar um fornecedor de identidade multi-inquilino, como o Azure Ative Directory. |
| Política de UtilizaçãoInRedirectUri | Não | Indica se deve utilizar uma política na construção do URI redirecionado. Quando configurar a sua aplicação no fornecedor de identidade, tem de especificar o URI redirecionado. O redirecionamento URI aponta para `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`Azure AD B2C, .  Se especificar, `false`tem de adicionar um URI redirecionado para cada política que utiliza. Por exemplo: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não | Indica se um pedido a um serviço externo deve ser marcado como uma falha se o código de estado http estiver na gama 5xx. A predefinição é `false`. |
| DiscoverMetadataByTokenIssuer | Não | Indica se os metadados oIDC devem ser descobertos utilizando o emitente no símbolo JWT. |
| Incluir Requerer Resolução de Reclamações  | Não | Para pedidos de entrada e saída, especifica se a resolução de [sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores `true`possíveis: ou `false`  (padrão). Se pretender utilizar uma reclamação no perfil técnico, desempente-a para `true`. |

### <a name="ui-elements"></a>Elementos da IU
 
As seguintes definições podem ser utilizadas para configurar a mensagem de erro apresentada após a falha. Os metadados devem ser configurados no perfil técnico OpenID Connect. As mensagens de erro podem ser [localizadas.](localization-string-ids.md#sign-up-or-sign-in-error-messages)

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| UserMessageif ClaimsprincipalNão Existe | Não | A mensagem para visualizar ao utilizador se uma conta com o nome de utilizador fornecido não se encontrar no diretório. |
| UserMessageIfInvalidPassword | Não | A mensagem a mostrar ao utilizador se a palavra-passe estiver incorreta. |
| UserMessageifOldPasswordUsed| Não |  A mensagem para mostrar ao utilizador se for utilizada uma senha antiga.|

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade. A chave criptográfica só é **response_types** necessária se os `code`metadados response_types estiverem definidos para . Neste caso, o Azure AD B2C faz outra chamada para trocar o código de autorização por um sinal de acesso. Se os metadados `id_token` estiverem definidos, pode omitir a chave criptográfica.  |

## <a name="redirect-uri"></a>Redirecione Uri

Quando configurar o URI redirecionamento `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`do seu fornecedor de identidade, introduza . Certifique-se `{your-tenant-name}` de substituir pelo nome do seu inquilino. O URI redirecionado tem de estar em todas as minúsculas.

Exemplos:

- [Adicione a Conta Microsoft (MSA) como um fornecedor de identidade usando políticas personalizadas](identity-provider-microsoft-account-custom.md)
- [Inscreva-se usando contas Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Permitir que os utilizadores inscrevam-se num fornecedor de identidade Azure AD multi-inquilino usando políticas personalizadas](identity-provider-azure-ad-multi-tenant-custom.md)
