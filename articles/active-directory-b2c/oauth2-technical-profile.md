---
title: Definir um perfil técnico do OAuth2 em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico do OAuth2 em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33bad4982d54eb18e91be28511fb9137223f4a91
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950974"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OAuth2 em uma política personalizada Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade do protocolo OAuth2. OAuth2 é o principal protocolo para autorização e autenticação delegada. Para obter mais informações, consulte a [estrutura de autorização do OAuth 2,0 da RFC 6749](https://tools.ietf.org/html/rfc6749). Com um perfil técnico do OAuth2, você pode federar com um provedor de identidade baseado em OAuth2, como o Facebook. A Federação com um provedor de identidade permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OAuth2`. Por exemplo, o protocolo para o perfil técnico do **Facebook-OAuth** é `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas talvez você queira enviar parâmetros adicionais ao seu provedor de identidade. O exemplo a seguir adiciona o **domain_hint** parâmetro de cadeia de caracteres de consulta com o valor de `contoso.com` à solicitação de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade OAuth2. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade contanto que você defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade do Facebook:

- A declaração de **first_name** é mapeada para a Declaração **fornecida** .
- A declaração de **last_name** é mapeada para a declaração de **sobrenome** .
- A Declaração **DisplayName** sem mapeamento de nome.
- A declaração de **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A Declaração **identityprovider** que contém o nome do provedor de identidade.
- A declaração de **authenticationname** com um valor padrão de **socialIdpAuthentication**.

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
| client_id | Sim | O identificador do aplicativo do provedor de identidade. |
| IdTokenAudience | Não | O público-alvo da id_token. Se especificado, Azure AD B2C verifica se o token está em uma declaração retornada pelo provedor de identidade e é igual ao especificado. |
| authorization_endpoint | Sim | A URL do ponto de extremidade de autorização de acordo com a RFC 6749. |
| AccessTokenEndpoint | Sim | A URL do ponto de extremidade do token de acordo com a RFC 6749. |
| ClaimsEndpoint | Sim | A URL do ponto de extremidade de informações do usuário de acordo com a RFC 6749. |
| AccessTokenResponseFormat | Não | O formato da chamada de ponto de extremidade do token de acesso. Por exemplo, o Facebook requer um método HTTP GET, mas a resposta do token de acesso está no formato JSON. |
| AdditionalRequestQueryParameters | Não | Parâmetros de consulta de solicitação adicionais. Por exemplo, talvez você queira enviar parâmetros adicionais para seu provedor de identidade. Você pode incluir vários parâmetros usando um delimitador de vírgula. |
| ClaimsEndpointAccessTokenName | Não | O nome do parâmetro de cadeia de consulta de token de acesso. Alguns pontos de extremidade de declarações de provedores de identidade dão suporte à solicitação HTTP GET. Nesse caso, o token de portador é enviado usando um parâmetro de cadeia de caracteres de consulta em vez do cabeçalho de autorização. |
| ClaimsEndpointFormatName | Não | O nome do parâmetro de cadeia de caracteres de consulta de formato. Por exemplo, você pode definir o nome como `format` neste ponto de extremidade de declarações do LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | Não | O valor do parâmetro de cadeia de caracteres de consulta de formato. Por exemplo, você pode definir o valor como `json` neste ponto de extremidade de declarações do LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ProviderName | Não | O nome do provedor de identidade. |
| response_mode | Não | O método que o provedor de identidade usa para enviar o resultado para Azure AD B2C. Valores possíveis: `query`, `form_post` (padrão) ou `fragment`. |
| scope | Não | O escopo da solicitação que é definido de acordo com a especificação do provedor de identidade OAuth2. Como `openid`, `profile`e `email`. |
| HttpBinding | Não | A associação HTTP esperada para o token de acesso e os pontos de extremidade do token de declarações. Valores possíveis: `GET` ou `POST`.  |
| ResponseErrorCodeParamName | Não | O nome do parâmetro que contém a mensagem de erro retornada sobre HTTP 200 (OK). |
| ExtraParamsInAccessTokenEndpointResponse | Não | Contém os parâmetros extras que podem ser retornados na resposta de **AccessTokenEndpoint** por alguns provedores de identidade. Por exemplo, a resposta de **AccessTokenEndpoint** contém um parâmetro extra, como `openid`, que é um parâmetro obrigatório além do access_token em uma cadeia de caracteres de consulta de solicitação **ClaimsEndpoint** . Vários nomes de parâmetro devem ser ignorados e separados pelo delimitador de vírgula ', '. |
| ExtraParamsInClaimsEndpointRequest | Não | Contém os parâmetros extras que podem ser retornados na solicitação **ClaimsEndpoint** por alguns provedores de identidade. Vários nomes de parâmetro devem ser ignorados e separados pelo delimitador de vírgula ', '. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente do aplicativo de provedor de identidade. A chave de criptografia será necessária somente se os metadados de **response_types** estiverem definidos como `code`. Nesse caso, Azure AD B2C faz outra chamada para trocar o código de autorização por um token de acesso. Se os metadados estiverem definidos como `id_token`, você poderá omitir a chave criptográfica. |

## <a name="redirect-uri"></a>URI de Redirecionamento

Ao configurar a URL de redirecionamento do seu provedor de identidade, insira `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Certifique-se de substituir o **locatário** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com) e **PolicyId** pelo identificador da política (por exemplo, b2c_1a_policy). O URI de redirecionamento precisa estar em letras minúsculas.

Se você estiver usando o domínio **b2clogin.com** em vez de **login.microsoftonline.com** , certifique-se de usar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar o Google + como um provedor de identidade OAuth2 usando políticas personalizadas](active-directory-b2c-custom-setup-goog-idp.md)













