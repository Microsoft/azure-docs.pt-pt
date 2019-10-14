---
title: Gerenciar o SSO e a personalização de token usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Saiba como gerenciar o SSO e a personalização de token usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 36a95b502c13ccf360ba4ac56b4837d41ee487c8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296405"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gerenciar o SSO e a personalização de token usando políticas personalizadas no Azure Active Directory B2C

Este artigo fornece informações sobre como você pode gerenciar suas configurações de token, sessão e logon único (SSO) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory B2C (Azure ad B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Tempos de vida de token e configuração de declarações

Para alterar as configurações em seus tempos de vida de token, adicione um elemento [ClaimsProviders](claimsproviders.md) no arquivo de terceira parte confiável da política que você deseja impactar.  O elemento **ClaimsProviders** é um filho do elemento [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Insira o elemento ClaimsProviders entre o elemento BasePolicy e o elemento RelyingParty do arquivo de terceira parte confiável.

Dentro do, você precisará colocar as informações que afetam os tempos de vida do token. O XML é semelhante a este exemplo:

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

Os valores a seguir são definidos no exemplo anterior:

- **Tempos de vida do token de acesso** -o valor de tempo de vida do token de acesso é definido com o item de metadados **token_lifetime_secs** . O valor padrão é 3600 segundos (60 minutos).
- **Tempo de vida do token de ID** -o valor de tempo de vida do token de ID é definido com o item de metadados **id_token_lifetime_secs** . O valor padrão é 3600 segundos (60 minutos).
- **Tempo de vida do token de atualização** – o valor do tempo de vida do token de atualização é definido com o item de metadados **refresh_token_lifetime_secs** . O valor padrão é 1209600 segundos (14 dias).
- **Tempo de vida da janela deslizante do token de atualização** – se desejar definir um tempo de vida da janela deslizante para o token de atualização, defina o valor do item de metadados **rolling_refresh_token_lifetime_secs** . O valor padrão é 7776000 (90 dias). Se você não quiser impor um tempo de vida da janela deslizante, substitua o item por `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Declaração do emissor (ISS)** -a declaração do emissor (ISS) é definida com o item de metadados **IssuanceClaimPattern** . Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Definindo a declaração que representa a ID da política** -as opções para definir esse valor são `TFP` (Diretiva de estrutura de confiança) e `ACR` (referência de contexto de autenticação). `TFP` é o valor recomendado. Defina **AuthenticationContextReferenceClaimPattern** com o valor de `None`.

    No elemento **ClaimsSchema** , adicione este elemento:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    No elemento **OutputClaims** , adicione este elemento:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para ACR, remova o item **AuthenticationContextReferenceClaimPattern** .

- **Declaração de Subject (sub)** -essa opção usa ObjectID como padrão se você quiser mudar essa configuração para `Not Supported`, substituir esta linha:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    com esta linha:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Comportamento de sessão e SSO

Para alterar o comportamento da sessão e as configurações de SSO, você adiciona um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty](relyingparty.md) .  O elemento **UserJourneyBehaviors** deve seguir imediatamente o **DefaultUserJourney**. O interior do seu elemento **UserJourneyBehavors** deve ser semelhante a este exemplo:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Os valores a seguir são configurados no exemplo anterior:

- **SSO (logon único)** -o logon único é configurado com o **logon único**. Os valores aplicáveis são `Tenant`, `Application`, `Policy` e `Suppressed`.
- **Tempo limite da sessão do aplicativo Web** -o tempo limite da sessão do aplicativo Web é definido com o elemento **SessionExpiryType** . Os valores aplicáveis são `Absolute` e `Rolling`.
- **Tempo de vida da sessão do aplicativo** Web-o tempo de vida da sessão do aplicativo Web é definido com o elemento **SessionExpiryInSeconds** . O valor padrão é 86400 segundos (1440 minutos).
