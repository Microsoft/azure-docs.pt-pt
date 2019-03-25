---
title: Selecione um contrato de página no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como selecionar um contrato de página no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 39198c0093f018b64a1292f023914651b51b4faf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403787"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Selecione um contrato de página com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Pode ativar o código do lado do cliente de JavaScript nas políticas do Azure Active Directory (Azure AD) B2C, quer esteja a utilizar fluxos de utilizador ou políticas personalizadas. Este artigo discute como selecionar um contrato de página no Azure AD B2C ao configurá-lo num [política personalizada](active-directory-b2c-overview-custom.md). Um contrato de página é uma associação de elementos que fornece o Azure AD B2C e o conteúdo que fornecer. Se pretende usar [Javascript](javascript-samples.md), é necessário definir uma versão de contrato de página para todas as suas definições de conteúdo na sua política personalizada.

> [!NOTE]
> Se pretender ativar o JavaScript para fluxos de utilizador, consulte [sobre a utilização de versões de contrato de página e JavaScript num fluxo de utilizador](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Substitua os valores de URI de dados

Em suas políticas personalizadas, talvez tenha [ContentDefinitions](contentdefinitions.md) que definem os modelos HTML utilizados no percurso do utilizador. O **ContentDefinition** contém uma **URI de dados** que se refere a elementos da página fornecidos pelo Azure AD B2C. O **LoadUri** é o caminho relativo para o conteúdo HTML e CSS que fornecer.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Para selecionar um contrato de página, alterar os **URI de dados** valores em seu [ContentDefinitions](contentdefinitions.md) nas suas políticas. Alternando a partir do antigo **URI de dados** valores para os novos valores, selecionar um pacote imutável. A vantagem de utilizar este pacote é que sabe que não altere e causar um comportamento inesperado na sua página. 

Para configurar um contrato de página, utilize a seguinte tabela para encontrar **URI de dados** valores. 

| Valor do URI de dados antigo | Novo valor de URI de dados |
| ----------------- | ----------------- |
| urn: com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn: com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn: com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn: com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn: com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Passos Seguintes

Encontrar mais informações sobre como pode personalizar a interface do usuário das suas aplicações na [personalizar a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



