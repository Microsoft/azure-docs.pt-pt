---
title: Defina um perfil técnico OAuth2 numa política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico OAuth2 numa política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e87772b6911e69b94f66cf09f0700f0025947fd0
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283835"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OAuth2 numa política personalizada do Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Ative Directory B2C (Azure AD B2C) presta apoio ao fornecedor de identidade do protocolo OAuth2. OAuth2 é o protocolo principal para autorização e autenticação delegada. Para mais informações, consulte o [RFC 6749 O Plano de Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). Com um perfil técnico OAuth2, pode federar com um fornecedor de identidade baseado em OAuth2, como o Facebook. A federação com um fornecedor de identidade permite que os utilizadores assinem com as suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O **atributo nome** do elemento **Protocolo** tem de ser definido para `OAuth2` . Por exemplo, o protocolo para o perfil técnico **Facebook-OAUTH** `OAuth2` é:

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Reclamações de entrada

Os **elementos InputClaims** e **InputClaimsTransformations** não são necessários. Mas pode querer enviar parâmetros adicionais ao seu fornecedor de identidade. O exemplo a seguir adiciona o **parâmetro de** cadeia de consulta domain_hint com o valor do pedido de `contoso.com` autorização.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Reclamações de saída

O elemento **OutputClaims** contém uma lista de reclamações devolvidas pelo fornecedor de identidade OAuth2. Poderá ser necessário mapear o nome da reclamação definida na sua política para o nome definido no fornecedor de identidade. Também pode incluir reclamações que não são devolvidas pelo fornecedor de identidade desde que desemque o `DefaultValue` atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos de **saídaClaimsTransformation** que são utilizados para modificar as alegações de saída ou gerar novos.

O exemplo a seguir mostra as reclamações devolvidas pelo fornecedor de identidade do Facebook:

- A **first_name** alegação está mapeada para a **reclamação dada pelo Nome.**
- A **last_name** alegação está mapeada para a alegação do **sobrenome.**
- A **alegação do nome de exibição** sem mapeamento de nomes.
- A reclamação **de e-mail** sem mapeamento de nome.

O perfil técnico também devolve alegações que não são devolvidas pelo fornecedor de identidade:

- A **identidadeProvider** afirma que contém o nome do fornecedor de identidade.
- A **autenticaçãoProvação com** um valor predefinido de **SocialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_id | Yes | O identificador de aplicação do fornecedor de identidade. |
| IdTokenAudience | No | O público do id_token. Se especificado, a Azure AD B2C verifica se o token está numa reclamação devolvida pelo fornecedor de identidade e é igual à especificada. |
| authorization_endpoint | Yes | O URL do ponto final de autorização de acordo com o RFC 6749. |
| AccessTokenEndpoint | Yes | O URL do ponto final simbólico de acordo com o RFC 6749. |
| ReclamaçõesEndpoint | Yes | O URL do ponto final de informação do utilizador de acordo com RFC 6749. |
| end_session_endpoint | Yes | O URL do ponto final da sessão de acordo com o RFC 6749. |
| AccessTokenResponseFormat | No | O formato da chamada de ponto final de acesso. Por exemplo, o Facebook requer um método HTTP GET, mas a resposta do token de acesso está no formato JSON. |
| Outros RequestQueryParameters | No | Parâmetros de consulta de pedido adicionais. Por exemplo, pode querer enviar parâmetros adicionais ao seu fornecedor de identidade. Pode incluir vários parâmetros utilizando o delimiter de vírgula. |
| ClaimsEndpointAccessTokenName | No | O nome do parâmetro de cadeia de consulta de acesso. As alegações de alguns fornecedores de identidade suportam o pedido GET HTTP. Neste caso, o token do portador é enviado utilizando um parâmetro de cadeia de consulta em vez do cabeçalho de autorização. Valor predefinido: `access_token` . |
| ClaimsEndpointFormatName | No | O nome do parâmetro de cadeia de consulta de formato. Por exemplo, pode definir o nome como `format` neste ponto final de reclamações do LinkedIn `https://api.linkedin.com/v1/people/~?format=json` . |
| ClaimsEndpointFormat | No | O valor do parâmetro de cadeia de consulta de formato. Por exemplo, pode definir o valor como `json` neste ponto final de reclamações do LinkedIn `https://api.linkedin.com/v1/people/~?format=json` . |
| Metotod de Transporte de Transportes | No | Especifica como o símbolo é enviado. O método padrão é uma cadeia de consulta. Para enviar o token como cabeçalho de pedido, definido para `AuthorizationHeader` . |
| ProviderName | No | O nome do fornecedor de identidade. |
| response_mode | No | O método que o fornecedor de identidade utiliza para enviar o resultado de volta para Azure AD B2C. Valores possíveis: `query` `form_post` (padrão), ou `fragment` . |
| scope | No | O âmbito do pedido que é definido de acordo com a especificação do fornecedor de identidade OAuth2. Como, `openid` `profile` `email` e. |
| HttpBinding | No | A ligação HTTP esperada ao token de acesso e reclama pontos finais simbólicos. Valores possíveis: `GET` ou `POST` .  |
| Nome de RespostaErrorCodeParamName | No | O nome do parâmetro que contém a mensagem de erro devolvida em HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | No | Contém os parâmetros extra que podem ser devolvidos na resposta do **AccessTokenEndpoint** por alguns fornecedores de identidade. Por exemplo, a resposta do **AccessTokenEndpoint** contém um parâmetro extra, como `openid` , que é um parâmetro obrigatório para além do access_token numa cadeia de **pedidos claimsEndpoint.** Vários nomes de parâmetros devem ser escapados e separados pela vírgula '', delimiter. |
| ExtraParamsInClaimsEndpointRequest | No | Contém os parâmetros adicionais que podem ser devolvidos no pedido **claimsEndpoint** por alguns fornecedores de identidade. Vários nomes de parâmetros devem ser escapados e separados pela vírgula '', delimiter. |
| IncluirClaimResolvingInClaimsHandling  | No | Para pedidos de entradas e saídas, especifica se a [resolução de sinistros](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` , ou `false` (predefinição). Se pretender utilizar uma reclamação no perfil técnico, desa um pouco `true` para . |
| ResolveJsonPathsInJsonTokens  | No | Indica se o perfil técnico resolve os caminhos do JSON. Valores possíveis: `true` , ou `false` (predefinição). Utilize estes metadados para ler dados a partir de um elemento JSON aninhado. Em um [OutputClaim](technicalprofiles.md#output-claims), desapedaça `PartnerClaimType` o elemento de caminho JSON que pretende obter. Por exemplo: `firstName.localized` ou `data.0.to.0.email` . .|
|token_endpoint_auth_method| No| Especifica como a Azure AD B2C envia o cabeçalho de autenticação para o ponto final simbólico. Valores possíveis: `client_secret_post` (padrão) e `client_secret_basic` (visualização pública), `private_key_jwt` (visualização pública). Para mais informações, consulte [a secção de autenticação do cliente OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). |
|token_signing_algorithm| No | Especifica o algoritmo de assinatura para utilizar quando `token_endpoint_auth_method` estiver definido para `private_key_jwt` . Valores possíveis: `RS256` (padrão) ou `RS512` . .|
|SingleLogoutEnabled| No | Indica se durante a entrada no perfil técnico tenta-se assinar dos fornecedores de identidade federados. Para mais informações, consulte [a sessão de Sessão Azure AD B2C](session-behavior.md#sign-out). Valores possíveis: `true` (padrão), ou `false` .|
| UsePolicyInRedirectUri | No | Indica se deve utilizar uma política ao construir o URI de redirecionamento. Ao configurar a sua aplicação no fornecedor de identidade, tem de especificar o URI de redirecionamento. O redirecionamento URI aponta para Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Se `true` especificar, tem de adicionar um URI de redirecionamento para cada apólice que utilizar. Por exemplo: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Yes | O segredo do cliente da aplicação do fornecedor de identidade. A chave criptográfica só é necessária se os **metadados response_types** estiver definidos para `code` . Neste caso, a Azure AD B2C faz outra chamada para trocar o código de autorização por um token de acesso. Se os metadados estiverem `id_token` definidos, pode omitir a tecla criptográfica. |

## <a name="redirect-uri"></a>URI de Redirecionamento

Quando configurar o URI de redirecionamento do seu fornecedor de identidade, insira `https://{tenant-name}.b2clogin.com/{tenant-name}.onmicrosoft.com/oauth2/authresp` . Certifique-se de substituir `{tenant-name}` pelo nome do seu inquilino (por exemplo, contosob2c). O URI de redirecionamento tem de estar em todas as minúsculas.

Exemplos:

- [Adicione o Google+ como fornecedor de identidade OAuth2 usando políticas personalizadas](identity-provider-google.md)
