---
title: Adicione marca à página de sinsagem da sua organização
titleSuffix: Azure AD B2C
description: Saiba como adicionar a marca da sua organização às páginas Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111497"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Adicione marca às páginas Azure Ative Directory B2C da sua organização

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Pode personalizar as suas páginas Azure AD B2C com um logotipo de banner, imagem de fundo e cor de fundo utilizando [a marca](../active-directory/fundamentals/customize-branding.md)Azure Ative Directory Company . A marca da empresa inclui a inscrição, inscrição, edição de perfis e reposição de passwords. 

> [!TIP]
> Para personalizar outros aspetos das páginas de fluxo do seu utilizador para além do logótipo do banner, imagem de fundo ou cor de fundo, consulte como [personalizar o UI com o modelo HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Para personalizar as suas páginas de fluxo de utilizador, primeiro configura a marca da empresa no Azure Ative Directory, depois ativa-a nos layouts de página dos fluxos do seu utilizador em Azure AD B2C.

## <a name="configure-company-branding"></a>Configurar o branding da empresa

Comece por definir o logótipo do banner, imagem de fundo e cor de fundo dentro **da marca da Empresa.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Manage**, selecione **marca da Empresa.**
1. Siga os passos em [Adicionar marca à página de inscrição do Azure Ative Directory da sua organização.](../active-directory/fundamentals/customize-branding.md)

Tenha estas coisas em mente quando configurar a marca da empresa em Azure AD B2C:

* A marca da empresa em Azure AD B2C está atualmente limitada à **imagem de fundo,** **logotipo do banner** e personalização da cor de **fundo.** As outras propriedades no painel de marca da empresa, por exemplo, as que se encontram em **configurações Avançadas,** não são *suportadas.*
* Nas páginas de fluxo do utilizador, a cor de fundo é mostrada antes da imagem de fundo ser carregada. Recomendamos que escolha uma cor de fundo que corresponda de perto as cores da sua imagem de fundo para uma experiência de carregamento mais suave.
* O logótipo do banner aparece nos e-mails de verificação enviados aos seus utilizadores quando iniciam um fluxo de utilizador de inscrição.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Ativar a marca nas páginas de fluxo do utilizador

Depois de configurar a marca da empresa, ative-a nos fluxos do utilizador.

1. No menu esquerdo do portal Azure, selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **fluxos de utilizador (políticas)**.
1. Selecione o fluxo de utilizador para o qual pretende ativar a marca da empresa. A marca da empresa não é **suportada** para os tipos padrão *de inscrição do utilizador* e *edição* de perfil padrão.
1. Em **Personalizar, selecione** **layouts de página** e, em seguida, selecione o layout que gostaria de marcar. Por exemplo, **selecione Unified's sign up ou iniciar sedução na página**.
1. Para a versão layout da **página (pré-visualização)**, escolha a versão **1.2.0** ou superior.
1. Selecione **Guardar**.

Se quiser marcar todas as páginas no fluxo do utilizador, desacorda a versão de layout da página para cada layout de página no fluxo do utilizador.

![Seleção de layout de página em Azure AD B2C no portal Azure](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Selecione um layout de página

Para ativar a marca da empresa, precisa [definir uma versão de layout](contentdefinitions.md#migrating-to-page-layout) de página com versão de página para todas `contract` *as* definições de conteúdo na sua política personalizada. O formato do valor deve conter a palavra `contract` : _urn:com:microsoft:aad:b2c:elements:**contract**:p age-name:version_. Para especificar um layout de página nas suas políticas personalizadas que usam um valor **datauri** antigo.

Aprenda a [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página.

O exemplo a seguir mostra os identificadores de definição de conteúdo e os **DataUri correspondentes** com contrato de página: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

O exemplo a seguir mostra um logotipo de banner personalizado e uma imagem de fundo em um *Sign up e assinar na* página de fluxo do utilizador que usa o modelo Ocean Blue:

![Página de inscrição/inscrição marcada servida por Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Utilize ativos de marca da empresa em HTML personalizado

Para utilizar os ativos de marca da sua empresa em [HTML personalizado,](customize-ui-with-html.md)adicione as seguintes etiquetas fora da `<div id="api">` etiqueta:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A fonte de imagem é substituída pela imagem de fundo e logotipo do banner.

## <a name="next-steps"></a>Passos seguintes

Encontre mais informações sobre como pode personalizar a interface do utilizador das suas aplicações em [Personalizar a interface de utilizador da sua aplicação em Azure Ative Directory B2C](customize-ui-with-html.md).