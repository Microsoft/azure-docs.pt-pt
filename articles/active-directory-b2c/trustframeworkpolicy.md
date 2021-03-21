---
title: TrustFrameworkPolicy - Azure Ative Directory B2C | Microsoft Docs
description: Especificar o elemento TrustFrameworkPolicy de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470722"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou mais ficheiros com formato XML, que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem elementos da política, tais como o esquema de reclamações, as transformações de reclamações, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos, a viagem do utilizador e as etapas de orquestração. Cada ficheiro de política é definido dentro do elemento **TrustFrameworkPolicy** de alto nível de um ficheiro de política.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


O elemento **TrustFrameworkPolicy** contém os seguintes atributos:

| Atributo | Obrigatório | Descrição |
|---------- | -------- | ----------- |
| PolíticaSesoversão | Yes | A versão do esquema que deve ser usada para executar a apólice. O valor deve ser `0.3.0.0` |
| TenantObjectId | No | O identificador de objetos único do inquilino Azure Ative Directory B2C (Azure AD B2C). |
| TenantId | Yes | O identificador único do inquilino a que esta política pertence. |
| PolicyId | Yes | O identificador único para a apólice. Este identificador deve ser pré-fixado por *B2C_1A_* |
| PublicPolicyUri | Yes | O URI para a apólice, que é a combinação da identificação do inquilino e a identificação da apólice. |
| Envio de munições | No | Valores possíveis: `Production` , ou `Development` . . `Production` é a predefinição. Use esta propriedade para depurar a sua apólice. Para obter mais informações, consulte [a Recolha de Registos.](troubleshoot-with-application-insights.md) |
| UserJourneyRecorderEndpoint | No | O ponto final que é utilizado para a exploração madeireira. O valor deve ser definido `urn:journeyrecorder:applicationinsights` se o atributo existir. Para obter mais informações, consulte [a Recolha de Registos.](troubleshoot-with-application-insights.md) |


O exemplo a seguir mostra como especificar o elemento **TrustFrameworkPolicy:**

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

O elemento **TrustFrameworkPolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | ----------- |
| Política de Base| 0:1| O identificador de uma política de base. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Os blocos de construção da sua política. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Uma coleção de fornecedores de sinistros. |
| [UserJourneys](userjourneys.md) | 0:1 | Uma coleção de viagens de utilizador. |
| [RelyingParty](relyingparty.md) | 0:1 | Uma definição de uma política partidária dependente. |

Para herdar uma política de outra política, um elemento **basePolicy** deve ser declarado sob o elemento **TrustFrameworkPolicy** do ficheiro político. O elemento **BasePolicy** é uma referência à política de base da qual esta política é derivada.

O elemento **BasePolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Description |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do seu inquilino Azure AD B2C. |
| PolicyId | 1:1 | O identificador da política dos pais. |


O exemplo a seguir mostra como especificar uma política de base. Esta **política B2C_1A_TrustFrameworkExtensions** deriva da política **B2C_1A_TrustFrameworkBase.**

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

