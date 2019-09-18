---
title: Definir um perfil técnico do OpenID Connect em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico do OpenID Connect em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1262d34f93ecbcdb71586fd551d28fde477f92a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063948"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico do OpenID Connect em uma política personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para o provedor de identidade do protocolo [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . O OpenID Connect 1,0 define uma camada de identidade na parte superior do OAuth 2,0 e representa o estado da arte em protocolos de autenticação modernos. Com um perfil técnico do OpenID Connect, você pode federar com um provedor de identidade baseado em OpenID Connect, como o Azure AD. A Federação com um provedor de identidade permite que os usuários entrem com suas identidades sociais ou empresariais existentes.

## <a name="protocol"></a>Protocol

O atributo **Name** do elemento **Protocol** precisa ser definido como `OpenIdConnect`. Por exemplo, o protocolo para o perfil técnico **MSA-OIDC** é `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas talvez você queira enviar parâmetros adicionais ao seu provedor de identidade. O exemplo a seguir adiciona o parâmetro de cadeia de caracteres de consulta `contoso.com` domain_hint com o valor de para a solicitação de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade do OpenID Connect. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que você defina `DefaultValue` o atributo.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** que são usados para modificar as declarações de saída ou gerar novas.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade da conta da Microsoft:

- A **subdeclaração que** é mapeada para a Declaração **issuerUserId** .
- A declaração de **nome** que é mapeada para a Declaração **DisplayName** .
- O **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A Declaração **identityprovider** que contém o nome do provedor de identidade.
- A declaração de **authenticationname** com um valor padrão de **socialIdpAuthentication**.

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

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | O identificador do aplicativo do provedor de identidade. |
| IdTokenAudience | Não | O público-alvo do id_token. Se especificado, Azure AD B2C verifica se o token está em uma declaração retornada pelo provedor de identidade e é igual ao especificado. |
| LOS | Sim | Uma URL que aponta para um documento de configuração JSON Formatado de acordo com a especificação de descoberta do OpenID Connect, que também é conhecido como um ponto de extremidade de configuração do OpenID conhecido. |
| ProviderName | Não | O nome do provedor de identidade. |
| response_types | Não | O tipo de resposta de acordo com a especificação do OpenID Connect Core 1,0. Valores possíveis: `id_token`, `code`ou `token`. |
| response_mode | Não | O método que o provedor de identidade usa para enviar o resultado para Azure AD B2C. Valores possíveis: `query`, `form_post` (padrão) ou `fragment`. |
| scope | Não | O escopo da solicitação que é definido de acordo com a especificação do OpenID Connect Core 1,0. `openid`Como, `profile`e. `email` |
| HttpBinding | Não | A associação HTTP esperada para o token de acesso e os pontos de extremidade do token de declarações. Valores possíveis: `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Não | Uma chave que pode ser usada para entrar em cada um dos locatários ao usar um provedor de identidade multilocatário, como Azure Active Directory. |
| UsePolicyInRedirectUri | Não | Indica se uma política deve ser usada ao construir o URI de redirecionamento. Ao configurar seu aplicativo no provedor de identidade, você precisa especificar o URI de redirecionamento. O URI de redirecionamento aponta `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` para Azure ad B2C, (login.microsoftonline.com pode mudar com Your-Tenant-Name.b2clogin.com).  Se você especificar `false`, precisará adicionar um URI de redirecionamento para cada política usada. Por exemplo: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não | Indica se uma solicitação para um serviço externo deve ser marcada como uma falha se o código de status http estiver no intervalo 5xx. A predefinição é `false`. |
| DiscoverMetadataByTokenIssuer | Não | Indica se os metadados OIDC devem ser descobertos usando o emissor no token JWT. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Requerido | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | O segredo do cliente do aplicativo de provedor de identidade. A chave de criptografia será necessária somente se os metadados de **response_types** estiverem `code`definidos como. Nesse caso, Azure AD B2C faz outra chamada para trocar o código de autorização por um token de acesso. Se os metadados estiverem definidos como `id_token` você pode omitir a chave criptográfica.  |

## <a name="redirect-uri"></a>URI de redirecionamento

Ao configurar o URI de redirecionamento do seu provedor de `https://login.microsoftonline.com/te/tenant/oauth2/authresp`identidade, digite. Certifique-se de substituir o **locatário** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com) ou a ID do locatário. O URI de redirecionamento precisa estar em letras minúsculas.

Se você estiver usando o domínio **b2clogin.com** em vez de **login.microsoftonline.com** , certifique-se de usar b2clogin.com em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar a MSA (conta da Microsoft) como um provedor de identidade usando políticas personalizadas](active-directory-b2c-custom-setup-msa-idp.md)
- [Entrar usando contas do Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](active-directory-b2c-setup-commonaad-custom.md)

 














