---
title: ClaimsProviders - Azure Ative Directory B2C / Microsoft Docs
description: Especificar o elemento ClaimsProvider de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1d1928de8c9731b54966e566d4dddf9c01073d41
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201264"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um fornecedor de sinistros contém um conjunto de [perfis técnicos.](technicalprofiles.md) Todos os prestadores de sinistros devem ter um ou mais perfis técnicos que determinem os pontos finais e os protocolos necessários para comunicar com o fornecedor de sinistros. Um fornecedor de sinistros pode ter vários perfis técnicos. Por exemplo, vários perfis técnicos podem ser definidos porque o fornecedor de sinistros suporta vários protocolos, vários pontos finais com diferentes capacidades, ou lança diferentes reivindicações em diferentes níveis de garantia. Pode ser aceitável lançar reclamações sensíveis numa viagem de utilizador, mas não noutra.

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
| Domain | 0:1 | Uma cadeia que contém o nome de domínio para o fornecedor de reclamações. Por exemplo, se o seu fornecedor de sinistros incluir o perfil técnico do Facebook, o nome de domínio é Facebook.com. Este nome de domínio é utilizado para todos os perfis técnicos definidos no fornecedor de sinistros, a menos que seja ultrapassado pelo perfil técnico. O nome de domínio também pode ser referenciado numa **domain_hint**. Para obter mais informações, consulte o **redirecionar o sôm para uma** secção de prestador social de [Configuração de inscrição direta utilizando o Azure Ative Directory B2C](direct-signin.md). |
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
