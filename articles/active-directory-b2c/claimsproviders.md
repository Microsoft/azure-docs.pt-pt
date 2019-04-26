---
title: ClaimsProviders - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de ClaimsProvider de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2d862d07a65c3fb28b49a82692ea575f787b9750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313856"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Um fornecedor de afirmações contém um conjunto de [perfis técnicos](technicalprofiles.md). Cada fornecedor de afirmações tem de ter um ou mais perfis técnicos que determinam os pontos finais e os protocolos necessários para comunicar com o fornecedor de afirmações. Um fornecedor de afirmações pode ter vários perfis técnicos. Por exemplo, vários perfis técnicos poderão ser definidos porque o fornecedor de afirmações suporta vários protocolos, vários pontos de extremidade com diferentes capacidades ou libera afirmações diferentes em níveis diferentes de garantia. Pode ser aceitável para libertar afirmações confidenciais no percurso do utilizador de um, mas não em outro.

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

O **ClaimsProviders** elemento contém o seguinte elemento:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Um fornecedor de afirmações acreditada que pode ser otimizado em várias jornadas de utilizador. |

## <a name="claimsprovider"></a>ClaimsProvider

O **ClaimsProvider** elemento contém elementos subordinados:

| Elemento | Ocorrências | Descrição |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Uma cadeia de caracteres que contém o nome de domínio para o fornecedor de afirmações. Por exemplo, se o seu fornecedor de afirmações inclui o perfil técnico do Facebook, o nome de domínio é Facebook.com. Este nome de domínio é utilizado para todos os perfis técnicos definidos no fornecedor de afirmações, a menos que substituído pelo perfil de técnico. O nome de domínio também pode ser referenciado num **domain_hint**. Para obter mais informações, consulte a **redirecionar início de sessão para um fornecedor de redes sociais** secção [configurar direto início de sessão através do Azure Active Directory B2C](direct-signin.md). |
| Nome a Apresentar | 0:1 | Uma cadeia que contém o nome do fornecedor de afirmações que pode ser apresentado aos utilizadores. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Um conjunto de perfis técnicos suportado pelo fornecedor de afirmação |

**ClaimsProvider** organiza os seus perfis técnicos a inter-relação entre o fornecedor de afirmações. O exemplo seguinte mostra o fornecedor de afirmações do Azure Active Directory com os perfis de técnicos do Azure Active Directory:

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

O exemplo seguinte mostra o fornecedor de afirmações do Facebook com o **Facebook OAUTH** perfil técnico.

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
