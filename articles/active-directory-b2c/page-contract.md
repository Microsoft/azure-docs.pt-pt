---
title: Selecione um contrato de página - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como selecionar um contrato de página no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7aab43695f0b11590d8bd2aa011073ba04d95250
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66513002"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Selecione um contrato de página com as políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Pode ativar o código do lado do cliente de JavaScript nas políticas do Azure Active Directory (Azure AD) B2C, quer esteja a utilizar fluxos de utilizador ou políticas personalizadas. Para ativar o JavaScript para as suas aplicações, tem de adicionar um elemento para sua [política personalizada](active-directory-b2c-overview-custom.md), selecione um contrato de página e utilize [b2clogin.com](b2clogin.md) nos seus pedidos. Um contrato de página é uma associação de elementos que fornece o Azure AD B2C e o conteúdo que fornecer. Este artigo discute como selecionar um contrato de página no Azure AD B2C, configurando-o numa política personalizada.

> [!NOTE]
> Se pretender ativar o JavaScript para fluxos de utilizador, consulte [JavaScript e página Contrato versões no Azure Active Directory B2C](user-flow-javascript-overview.md).

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



