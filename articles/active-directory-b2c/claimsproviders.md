---
title: ClaimsProviders - Diretório Ativo Azure B2C  Microsoft Docs
description: Especifique o elemento ClaimsProvider de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189774"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um fornecedor de sinistros contém um conjunto de [perfis técnicos.](technicalprofiles.md) Cada prestador de sinistros deve ter um ou mais perfis técnicos que determinem os pontos finais e os protocolos necessários para comunicar com o prestador de sinistros. Um fornecedor de sinistros pode ter vários perfis técnicos. Por exemplo, vários perfis técnicos podem ser definidos porque o fornecedor de sinistros suporta múltiplos protocolos, vários pontos finais com diferentes capacidades, ou liberta diferentes reclamações em diferentes níveis de garantia. Pode ser aceitável lançar reclamações sensíveis numa viagem de utilizador, mas não noutra.

```XML
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
| ClaimsProvider | 1:n | Um fornecedor de sinistros acreditado que pode ser alavancado em várias viagens de utilizador. |

## <a name="claimsprovider"></a>ClaimsProvider

O elemento **ClaimsProvider** contém os seguintes elementos infantis:

| Elemento | Ocorrências | Descrição |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Uma cadeia que contém o nome de domínio para o fornecedor de reclamações. Por exemplo, se o seu fornecedor de reclamações incluir o perfil técnico do Facebook, o nome de domínio é Facebook.com. Este nome de domínio é utilizado para todos os perfis técnicos definidos no fornecedor de sinistros, a menos que seja ultrapassado pelo perfil técnico. O nome de domínio também pode ser referenciado num **domain_hint**. Para mais informações, consulte o **redirecionamento para uma** secção de prestador social de configuração de entrada direta utilizando o [Diretório Ativo Azure B2C](direct-signin.md). |
| DisplayName | 1:1 | Uma cadeia que contém o nome do fornecedor de sinistros. |
| [Perfis Técnicos](technicalprofiles.md) | 0:1 | Um conjunto de perfis técnicos suportados pelo prestador de sinistros |

**A ClaimsProvider** organiza a forma como os seus perfis técnicos se relacionam com o prestador de sinistros. O exemplo seguinte mostra o fornecedor de sinistros Azure Ative Directory com os perfis técnicos do Diretório Ativo Azure:

```XML
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

O exemplo que se segue mostra o fornecedor de reclamações do Facebook com o perfil técnico **Facebook-OAUTH.**

```XML
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
