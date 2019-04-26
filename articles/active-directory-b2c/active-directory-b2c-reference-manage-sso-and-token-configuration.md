---
title: Gerir o SSO e personalização de token com as políticas personalizadas no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre a gestão de SSO e personalização de token com as políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
origin.date: 10/09/2018
ms.date: 04/01/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: c0f5be7fd77ae195b66f8a8fb052ab8573d48171
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317178"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gerir o SSO e personalização de token com as políticas personalizadas no Azure Active Directory B2C

Este artigo fornece informações sobre como pode gerir o seu token, sessão e configurações de início de sessão único (SSO) com [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Configuração de afirmações e durações de token

Para alterar as definições no seu durações de token, é possível adicionar um [ClaimsProviders](claimsproviders.md) elemento no arquivo de terceiros entidade confiadora da política que pretende afetar.  O **ClaimsProviders** elemento é um elemento subordinado a [TrustFrameworkPolicy](trustframeworkpolicy.md) elemento. Dentro dela, precisará de colocar as informações que afeta as durações de token. O XML é semelhante a este exemplo:

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

Os seguintes valores estão definidos no exemplo anterior:

- **Durações de token de acesso** – a duração de token de acesso valor é definido com **token_lifetime_secs** item de metadados. O valor predefinido é de 3600 segundos (60 minutos).
- **Duração do token de ID** – a duração de token de ID valor é definido com o **id_token_lifetime_secs** item de metadados. O valor predefinido é de 3600 segundos (60 minutos).
- **Duração do token atualizado** – a duração de token de atualização de valor é definido com o **refresh_token_lifetime_secs** item de metadados. O valor predefinido é 1209600 segundos (14 dias).
- **Atualizar token duração da janela deslizante** - se de que pretende definir uma duração de janela deslizante para seu token de atualização, defina o valor de **rolling_refresh_token_lifetime_secs** item de metadados. O valor predefinido é 7776000 (90 dias). Se não pretender impor uma duração de janela deslizante, substituir o item com `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Afirmação do emissor (iss)** -afirmações o emissor (iss) está definido com o **IssuanceClaimPattern** item de metadados. Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Definição de afirmação que representa a ID de política** -são as opções para a definição deste valor `TFP` (política de estrutura de confiança) e `ACR` (referência de contexto de autenticação). `TFP` é o valor recomendado. Definir **AuthenticationContextReferenceClaimPattern** com o valor do `None`. 

    Na **ClaimsSchema** elemento, adicionar este elemento: 
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    No seu **OutputClaims** elemento, adicionar este elemento:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para o ACR, remover os **AuthenticationContextReferenceClaimPattern** item.

- **Afirmação do requerente** -esta opção é predefinido para o ObjectID, se desejar alternar esta definição para `Not Supported`, substitua essa linha: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    com esta linha:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Comportamento da sessão e SSO

Para alterar seu comportamento de sessão e configurações de SSO, é possível adicionar um **UserJourneyBehaviors** elemento dentro do [RelyingParty](relyingparty.md) elemento.  O **UserJourneyBehaviors** elemento imediatamente tem de seguir os **DefaultUserJourney**. O interior da sua **UserJourneyBehavors** elemento deve ter um aspeto semelhante a este exemplo:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Os seguintes valores estão configurados no exemplo anterior:

- **Início de sessão (SSO) único** -início de sessão único está configurado com o **SingleSignOn**. Os valores aplicáveis são `Tenant`, `Application`, `Policy`, e `Suppressed`. 
- **Aplicação Web duração da sessão (minutos)** – a sessão de aplicativo web tempo de vida é definido com o **SessionExpiryInSeconds** elemento. O valor predefinido é 86400 segundos (1440 minutos).
- **Tempo limite de sessão de aplicação Web** – a sessão de aplicativo web, tempo limite é definido com o **SessionExpiryType** elemento. Os valores aplicáveis são `Absolute` e `Rolling`.

