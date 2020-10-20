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
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9b2c5b24b88dd51596dfb5bd8b5f397419ca6e4
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215200"
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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a sessão Azure AD B2C](session-overview.md).
- Saiba como configurar o comportamento da [sessão em políticas personalizadas.](session-behavior-custom-policy.md)
- Referência: [JwtIssuer](jwt-issuer-technical-profile.md).
