---
title: Gerir a personalização SSO e token usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como gerir a personalização SSO e token usando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e72bd04bb41537546191b8ceb320c0722bd10146
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340296"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gerir a personalização SSO e token utilizando políticas personalizadas no Azure Ative Directory B2C

Este artigo fornece informações sobre como você pode gerir as suas configurações de token, sessão e único sign-on (SSO) usando [políticas personalizadas](custom-policy-overview.md) em Azure Ative Directory B2C (Azure AD B2C).

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>JWT token lifes and claims configuração

Para alterar as definições das suas vidas simbólicas, adicione um elemento [ClaimsProviders](claimsproviders.md) no ficheiro de partidos de suporte da política que pretende impactar.  O elemento **ClaimsProviders** é uma criança do elemento [TrustFrameworkPolicy.](trustframeworkpolicy.md)

Insira o elemento ClaimsProviders entre o elemento BasePolicy e o elemento RelyingParty do ficheiro do partido.

Lá dentro, terás de colocar a informação que afeta as tuas vidas simbólicas. O XML parece este exemplo:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Os seguintes valores são definidos no exemplo anterior:

- **Prazos de vida de acesso -** O valor de vida útil do símbolo de acesso é definido com **token_lifetime_secs** item de metadados. O valor predefinido é de 3600 segundos (60 minutos).
- **ID token lifetime -** O valor de vida de símbolo de ID é definido com o **item metadados id_token_lifetime_secs.** O valor predefinido é de 3600 segundos (60 minutos).
- **Atualizar a vida útil -** O valor de vida de token de atualização é definido com o item metadados **refresh_token_lifetime_secs.** O valor predefinido é de 1209600 segundos (14 dias).
- **Refrescar a vida útil da janela deslizante** - Se quiser definir uma janela deslizá-la para o seu token de atualização, desapedace o valor do **rolling_refresh_token_lifetime_secs** item metadados. O valor predefinido é de 7776000 (90 dias). Se não pretender impor uma janela deslizante, substitua o item por `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` .
- **Reclamação emitente (iss)** - A alegação do Emitente (iss) é definida com o item metadados **IssuanceClaimPattern.** Os valores aplicáveis são `AuthorityAndTenantGuid` `AuthorityWithTfp` e.
- **Definição de reclamação que representa o ID da política** - As opções para definir este valor são `TFP` (política de quadro de confiança) e `ACR` (referência de contexto de autenticação). `TFP` é o valor recomendado. Definir **AutenticaçãoContextReferenceClaimPattern** com o valor de `None` .

    No elemento **ClaimsSchema,** adicione este elemento:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    No seu elemento **OutputClaims,** adicione este elemento:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para ACR, **remova o produto AuthenticationContextReferenceClaimPattern.**

- **Reclamação (sub) assunto** - Esta opção é predefinida para ObjectID, se pretender mudar esta definição para `Not Supported` , substituir esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    com esta linha:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> As aplicações de uma página única que utilizam o fluxo de código de autorização com O PKCE têm sempre um prazo de vida útil de 24 horas. [Saiba mais sobre as implicações de segurança dos tokens de atualização no navegador.](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)

## <a name="provide-optional-claims-to-your-app"></a>Fornecer reclamações opcionais à sua app

As alegações [de produção de perfis técnicos](relyingparty.md#technicalprofile) da política do partido de Relying são valores que são devolvidos a uma aplicação. A adição de reclamações de saída irá emitir as reclamações no token após uma viagem bem sucedida do utilizador, e será enviada para a aplicação. Modifique o elemento de perfil técnico dentro da secção do partido dependente para adicionar as reclamações desejadas como uma reclamação de saída.

1. Abra o seu ficheiro de política personalizado. Por exemplo, SignUpOrSignin.xml.
1. Encontre o elemento OutputClaims. Adicione o OutputClaim que pretende ser incluído no token. 
1. Desa estação os atributos de reclamação de saída. 

O exemplo a seguir acrescenta a `accountBalance` alegação. A reclamação de contaBalance é enviada para o pedido como saldo. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

O elemento OutputClaim contém os seguintes atributos:

  - **ClaimTypeReferenceId** - O identificador de um tipo de reclamação já definido na secção [ClaimsSchema](claimsschema.md) no ficheiro de política ou ficheiro de política dos pais.
  - **PartnerClaimType** - Permite alterar o nome da reclamação no token. 
  - **DefaultValue** - Um valor predefinido. Também pode definir o valor padrão para um [resolver de reclamação,](claim-resolver-overview.md)como iD de inquilino.
  - **AlwaysUseDefaultValue** - Force a utilização do valor predefinido.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a sessão Azure AD B2C](session-overview.md).
- Saiba como configurar o comportamento da [sessão em políticas personalizadas.](session-behavior-custom-policy.md)
- Referência: [JwtIssuer](jwt-issuer-technical-profile.md).
