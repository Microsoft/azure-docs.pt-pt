---
title: Definir um perfil técnico de OAuth2 numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico de OAuth2 numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fde556c60f823f4bd287ca5672503158c7292f51
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918931"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OAuth2 em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para o fornecedor de identidade de protocolo OAuth2. Este é o protocolo primário para autorização e autenticação delegada. Para obter mais informações, consulte a [RFC 6749 o OAuth 2.0 autorização Framework](https://tools.ietf.org/html/rfc6749). Com o OAuth2 perfil técnico, pode federar com um OAuth2 com base em fornecedor de identidade, como o Facebook e o Live.com, que lhe permite aos utilizadores iniciar sessão com existente social ou as identidades de empresa.

## <a name="protocol"></a>Protocolo

O **Name** atributo da **protocolo** elemento tem de ser definido como `OAuth2`. Por exemplo, o protocolo para o **Facebook OAUTH** é o perfil técnico `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>Afirmações de entrada

O **InputClaims** e **InputClaimsTransformations** elementos não são necessários. Mas talvez queira enviar parâmetros adicionais para o fornecedor de identidade. O exemplo seguinte adiciona o **domain_hint** com o valor do parâmetro da cadeia de consulta `contoso.com` para o pedido de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Afirmações de saída

O **OutputClaims** elemento contém uma lista das declarações devolvido pelo fornecedor de identidade de OAuth2. Terá de mapear o nome da afirmação definido na sua política para o nome definido no fornecedor de identidade. Também pode incluir declarações que não são devolvidas pelo fornecedor de identidade, desde que definir o `DefaultValue` atributo.

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar as afirmações de saída ou gerar novas etiquetas.

O exemplo seguinte mostra as afirmações devolvidas pelo fornecedor de identidade do Facebook:

- O **first_name** afirmação é mapeada para o **givenName** de afirmação.
- O **last_name** afirmação é mapeada para o **Apelido** de afirmação.
- O **displayName** sem mapeamento de nome de afirmação....
- O **e-mail** sem mapeamento de nome de afirmação.

O perfil técnico também retorna afirmações que não são devolvidas pelo fornecedor de identidade: 

- O **identityProvider** afirmação que contém o nome do fornecedor de identidade.
- O **authenticationSource** a afirmação com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador de aplicação do fornecedor de identidade. |
| IdTokenAudience | Não | O público-alvo do id_token. Se for especificado, o Azure AD B2C verifica se o token está numa afirmação devolvida pelo fornecedor de identidade e é igual a um serviço especificado. |
| authorization_endpoint | Sim | O URL do ponto final de autorização de acordo com a RFC 6749. |
| AccessTokenEndpoint | Sim | O URL do ponto final do token de acordo com a RFC 6749. |  
| ClaimsEndpoint | Sim | O URL do ponto final de informações de utilizador de acordo com a RFC 6749. | 
| AccessTokenResponseFormat | Não | O formato da chamada de ponto final do token de acesso. Por exemplo, Facebook requer um método HTTP GET, mas a resposta de token de acesso está no formato JSON. |
| AdditionalRequestQueryParameters | Não | Parâmetros de consulta de pedido adicionais. Por exemplo, pode querer enviar parâmetros adicionais para o fornecedor de identidade. Pode incluir vários parâmetros com o delimitador de vírgulas. | 
| ClaimsEndpointAccessTokenName | Não | O nome do parâmetro de cadeia de caracteres de consulta token de acesso. Pedido de GET HTTP de suporte de pontos finais de afirmações de alguns fornecedores de identidade. Neste caso, o token de portador é enviado através de um parâmetro de cadeia de caracteres de consulta em vez do cabeçalho de autorização. |
| ClaimsEndpointFormatName | Não | O nome do parâmetro de cadeia de caracteres de consulta formato. Por exemplo, pode definir o nome como `format` neste LinkedIn ponto final de afirmações `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Não | O valor do parâmetro de cadeia de caracteres de consulta formato. Por exemplo, pode definir o valor como `json` neste LinkedIn ponto final de afirmações `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Não | O nome do fornecedor de identidade. |
| response_mode | Não | O método que o fornecedor de identidade utiliza para enviar o resultado para o Azure AD B2C. Valores possíveis: `query`, `form_post` (predefinição), ou `fragment`. |
| scope | Não | O âmbito do pedido de acesso definido de acordo com a especificação de fornecedor de identidade do OAuth2. Por exemplo, `openid`, `profile`, e `email`. |
| HttpBinding | Não | O enlace de HTTP esperado para os token e afirmações de token pontos finais de acesso. Valores possíveis: `GET` ou `POST`.  |
| ResponseErrorCodeParamName | Não | O nome do parâmetro que contém a mensagem de erro devolvida através de HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Não | Contém os parâmetros adicionais que podem ser devolvidos na resposta do **AccessTokenEndpoint** por alguns fornecedores de identidade. Por exemplo, a resposta do **AccessTokenEndpoint** contém como um parâmetro extra `openid`, que é um parâmetro obrigatório, além de access_token numa **ClaimsEndpoint** consulta do pedido cadeia de caracteres. Vários nomes de parâmetro devem ser caracteres de escape e separados pela vírgula ',' delimitador. |
| ExtraParamsInClaimsEndpointRequest | Não | Contém os parâmetros adicionais que podem ser devolvidos na **ClaimsEndpoint** pedido por alguns fornecedores de identidade. Vários nomes de parâmetro devem ser caracteres de escape e separados pela vírgula ',' delimitador. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O **CryptographicKeys** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade. A chave de criptografia é necessária apenas se o **response_types** metadados é definido como `code`. Neste caso, o Azure AD B2C faz outra chamada para o código de autorização para um token de acesso do exchange. Se os metadados é definido como `id_token` pode omitir a chave criptográfica.  |  

## <a name="redirect-uri"></a>URI de Redirecionamento

Quando configurar o URL de redirecionamento do seu fornecedor de identidade, introduza `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Certifique-se de substituir **inquilino** com o nome do inquilino (por exemplo, contosob2c.onmicrosoft.com) e **policyId** com o identificador da sua política (por exemplo, b2c_1a_policy). O URI de redirecionamento tem de ser em letras minúsculas.

Se estiver a utilizar o **b2clogin.com** domínio em vez de **login.microsoftonline.com** certificar-se de que utilizar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar Google + como um fornecedor de identidade de OAuth2 através de políticas personalizadas](active-directory-b2c-custom-setup-goog-idp.md)













