---
title: Definir um perfil técnico OpenId Connect numa política personalizada no Azure Active Directory B2C | Documentos da Microsoft
description: Defina um perfil técnico OpenId Connect numa política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a13ca362bf08b86297641061992f0820f0b624c5
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916772"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico OpenId Connect em políticas personalizadas do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory (Azure AD) B2C fornece suporte para o [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) fornecedor de identidade do protocolo. OpenID Connect 1.0 define uma camada de identidade com base no OAuth 2.0 e representa a Estado da última palavra em protocolos de autenticação moderna.  Com OpenId Connect perfil técnico, pode federar com uma ligação do OpenId com base em fornecedor de identidade, como o Azure AD, que lhe permite aos utilizadores iniciar sessão com existente social ou as identidades de empresa.

## <a name="protocol"></a>Protocolo

O **Name** atributo da **protocolo** elemento tem de ser definido como `OpenIdConnect`. Por exemplo, o protocolo para o **MSA OIDC** é o perfil técnico `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

O **OutputClaims** elemento contém uma lista das declarações devolvido pelo fornecedor de identidade de OpenId Connect. Terá de mapear o nome da afirmação definido na sua política para o nome definido no fornecedor de identidade. Também pode incluir declarações que não são devolvidas pelo fornecedor de identidade, desde que definir o `DefaultValue` atributo.

O **OutputClaimsTransformations** elemento pode conter uma coleção de **OutputClaimsTransformation** elementos que são utilizados para modificar as afirmações de saída ou gerar novas etiquetas.

O exemplo seguinte mostra as afirmações devolvidas pelo fornecedor de identidade Account Microsoft:

- O **sub** afirmação que está mapeada para o **socialIdpUserId** de afirmação.
- O **name** afirmação que está mapeada para o **displayName** de afirmação.
- O **e-mail** sem mapeamento de nomes.

O perfil técnico também retorna afirmações que não são devolvidas pelo fornecedor de identidade:

- O **identityProvider** afirmação que contém o nome do fornecedor de identidade.
- O **authenticationSource** a afirmação com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador de aplicação do fornecedor de identidade. |
| IdTokenAudience | Não | O público-alvo do id_token. Se for especificado, o Azure AD B2C verifica se o token está numa afirmação devolvida pelo fornecedor de identidade e é igual a um serviço especificado. |
| METADATA | Sim | Um URL que aponta para um documento de configuração JSON formatado em conformidade com a especificação de deteção de ligação OpenID, que também é conhecido como um ponto de extremidade de configuração de openid bem conhecido. |
| ProviderName | Não | O nome do fornecedor de identidade. |
| response_types | Não | O tipo de resposta, de acordo com a especificação de OpenID Connect Core 1.0. Valores possíveis: `id_token`, `code`, ou `token`. |
| response_mode | Não | O método que o fornecedor de identidade utiliza para enviar o resultado para o Azure AD B2C. Valores possíveis: `query`, `form_post` (predefinição), ou `fragment`. |
| scope | Não | O âmbito do pedido de acesso definido de acordo com a especificação de OpenID Connect Core 1.0. Por exemplo, `openid`, `profile`, e `email`. |
| HttpBinding | Não | O enlace de HTTP esperado para os token e afirmações de token pontos finais de acesso. Valores possíveis: `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Não | Uma chave que pode ser utilizada para iniciar sessão a cada um dos inquilinos, quando utilizar um fornecedor de identidade de multi-inquilino do Azure Active Directory. |
| UsePolicyInRedirectUri | Não | Indica se deve utilizar uma política ao construir o URI de redirecionamento. Quando configurar a sua aplicação no fornecedor de identidade, tem de especificar o URI de redirecionamento. O redirecionamento URI aponta para o Azure AD B2C, `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com pode mudar com o seu inquilino name.b2clogin.com).  Se especificar `false`, precisa adicionar um URI de redirecionamento para cada política utilizar. Por exemplo: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não | Indica se um pedido para um serviço externo deve ser marcado como uma falha se o código de estado de Http está no intervalo 5xx. A predefinição é `false`. |
| DiscoverMetadataByTokenIssuer | Não | Indica se os metadados OIDC devem ser detetados com o emissor no JWT token. |

## <a name="cryptographic-keys"></a>Chaves criptográficas

O **CryptographicKeys** elemento contém o atributo seguinte:

| Atributo | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente da aplicação do fornecedor de identidade. A chave de criptografia é necessária apenas se o **response_types** metadados é definido como `code`. Neste caso, o Azure AD B2C faz outra chamada para o código de autorização para um token de acesso do exchange. Se os metadados é definido como `id_token` pode omitir a chave criptográfica.  |  

## <a name="redirect-uri"></a>Redirect Uri
 
Quando configura o URI de redirecionamento de seu fornecedor de identidade, introduza `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Certifique-se de substituir **inquilino** com o nome do inquilino (por exemplo, contosob2c.onmicrosoft.com) ou ID. do inquilino O URI de redirecionamento tem de ser em letras minúsculas.

Se estiver a utilizar o **b2clogin.com** domínio em vez de **login.microsoftonline.com** certificar-se de que utilizar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar conta da Microsoft (MSA) como um fornecedor de identidade com as políticas personalizadas](active-directory-b2c-custom-setup-msa-idp.md)
- [Inicie sessão com contas do Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Permitir que os utilizadores iniciem sessão a um fornecedor de identidade do multi-inquilino do Azure AD com as políticas personalizadas](active-directory-b2c-setup-commonaad-custom.md)

 














