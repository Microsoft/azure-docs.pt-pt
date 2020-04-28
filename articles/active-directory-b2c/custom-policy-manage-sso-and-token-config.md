---
title: Gerir a personalização SSO e simbólica utilizando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba mais sobre gestão de SSO e personalização simbólica utilizando políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189298"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gerir a personalização SSO e simbólica utilizando políticas personalizadas no Diretório Ativo Azure B2C

Este artigo fornece informações sobre como pode gerir as configurações de token, session e single sign-on (SSO) utilizando [políticas personalizadas](custom-policy-overview.md) no Azure Ative Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Token lifetimes e configuração de reclamações

Para alterar as definições das suas vidas simbólicas, adicione um elemento [Reclamadores](claimsproviders.md) no ficheiro partidário de confiança da política que pretende impactar.  O elemento **ClaimsProviders** é uma criança do elemento [TrustFrameworkPolicy.](trustframeworkpolicy.md)

Insira o elemento ClaimsProviders entre o elemento BasePolicy e o elemento RelyingParty do ficheiro do partido que depende.

Lá dentro, terás de pôr a informação que afeta as tuas vidas simbólicas. O XML parece-se com este exemplo:

```XML
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

- **Acesso a token lifetimes** - O valor de vida token de acesso é definido com **token_lifetime_secs** item de metadados. O valor predefinido é de 3600 segundos (60 minutos).
- **ID token lifetime** - O valor de vida token id é definido com o **id_token_lifetime_secs** item metadados. O valor predefinido é de 3600 segundos (60 minutos).
- Refrescar a **vida útil do token** - O valor de vida token de atualização é definido com **o** refresh_token_lifetime_secs item metadados. O valor predefinido é de 1209600 segundos (14 dias).
- **Refresque** a vida útil da janela deslizante do token - Se quiser definir uma janela deslizante para o seu token de atualização, detete o valor do **rolling_refresh_token_lifetime_secs** item de metadados. O valor predefinido é de 7776000 (90 dias). Se não quiser impor uma vida útil deslizante da `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`janela, substitua o item por .
- **Reclamação do emitente (iss)** - A alegação do Emitente (iss) é definida com o item de metadados **IssuanceClaimPattern.** Os valores `AuthorityAndTenantGuid` aplicáveis são e `AuthorityWithTfp`.
- **Definição** de reclamação representando ID de `TFP` política - As `ACR` opções para definir este valor são (política de quadro de confiança) e (referência de contexto de autenticação). `TFP`é o valor recomendado. Definir **AutenticaçãoContextoReferênciaReclamação** com `None`o valor de .

    No elemento **ClaimsSchema,** adicione este elemento:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    No seu elemento **OutputClaims,** adicione este elemento:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para a ACR, remova o item de direito de referência de contexto **de autenticação.**

- **Reclamação (sub)** - Esta opção não se aplica ao ObjectID, se quiser mudar esta definição para `Not Supported`, substitua esta linha:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    com esta linha:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Comportamento da sessão e SSO

Para alterar o comportamento da sua sessão e as configurações SSO, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty.](relyingparty.md)  O elemento **UserJourneyBehaviors** deve seguir imediatamente a **Viagem**de Utilizador Padrão . O interior do seu elemento **UserJourneyBehavors** deve parecer este exemplo:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Os seguintes valores são configurados no exemplo anterior:

- **Sinal único ligado (SSO)** - O único sinal é configurado com o **SingleSignOn**. Os valores `Tenant`aplicáveis são, `Application`e `Policy` `Suppressed`.
- **Tempo de saída da sessão** da web - O timeout da sessão da web app é definido com o elemento **SessionExpiryType.** Os valores `Absolute` aplicáveis são e `Rolling`.
- Vida útil da sessão de **aplicações web** - O tempo de vida da sessão da web app é definido com o elemento **SessionExpireyInSeconds.** O valor predefinido é de 86400 segundos (1440 minutos).
