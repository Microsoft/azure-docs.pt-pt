---
title: ClaimsProviders - Azure Ative Directory B2C | Microsoft Docs
description: Especificar o elemento ClaimsProvider de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 846394266b981c14788148be465912b14bc1fb3e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447916"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma reclamação fornece uma interface para comunicar com diferentes tipos de partes através dos seus [perfis técnicos.](technicalprofiles.md) Todos os prestadores de sinistros devem ter um ou mais perfis técnicos que determinem os pontos finais e os protocolos necessários para comunicar com o fornecedor de sinistros. Um fornecedor de sinistros pode ter vários perfis técnicos. Por exemplo, vários perfis técnicos podem ser definidos porque o fornecedor de sinistros suporta vários protocolos, vários pontos finais com diferentes capacidades, ou lança diferentes reivindicações em diferentes níveis de garantia. Pode ser aceitável lançar reclamações sensíveis numa viagem de utilizador, mas não noutra.

Uma viagem de utilizador combina chamar perfis técnicos através de passos de orquestração para definir a sua lógica de negócio. 

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

O elemento **ClaimsProviders** contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| Provívido de Reclamações | 1:n | Um provedor de reclamações acreditado que pode ser alavancado em várias viagens de utilizador. |

## <a name="claimsprovider"></a>Provívido de Reclamações

O elemento **ClaimsProvider** contém os seguintes elementos infantis:

| Elemento | Ocorrências | Descrição |
| ------- | ---------- | ----------- |
| Domínio | 0:1 | Uma cadeia que contém o nome de domínio para o fornecedor de reclamações. Por exemplo, se o seu fornecedor de sinistros incluir o perfil técnico do Facebook, o nome de domínio é Facebook.com. Este nome de domínio é utilizado para todos os perfis técnicos definidos no fornecedor de sinistros, a menos que seja ultrapassado pelo perfil técnico. O nome de domínio também pode ser referenciado numa **domain_hint**. Para obter mais informações, consulte o **redirecionar o sôm para uma** secção de prestador social de [Configuração de inscrição direta utilizando o Azure Ative Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Uma cadeia que contém o nome do fornecedor de sinistros. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Um conjunto de perfis técnicos apoiados pelo fornecedor de sinistros |

**ClaimsProvider** organiza a forma como os seus perfis técnicos se relacionam com o fornecedor de sinistros. O exemplo a seguir mostra o fornecedor de reclamações do Azure Ative Directory com os perfis técnicos do Azure Ative Directory:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O exemplo a seguir mostra o fornecedor de reclamações do Facebook com o perfil técnico **Facebook-OAUTH.**

```xml
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
