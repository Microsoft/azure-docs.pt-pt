---
title: Personalizar a interface de utilizador
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do utilizador para as suas aplicações que utilizam o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056117"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalize a interface do utilizador no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Marcar e personalizar a interface de utilizador que o Azure Ative Directory B2C (Azure AD B2C) exibe aos seus clientes, ajuda a proporcionar uma experiência de utilizador perfeita na sua aplicação. Estas experiências incluem inscrição, inscrição, edição de perfis e reposição de passwords. Neste artigo, você personaliza as suas páginas Azure AD B2C usando o modelo de página e a marca da empresa. 

> [!TIP]
> Para personalizar outros aspetos das páginas de fluxo do seu utilizador para além do modelo de página, logotipo do banner, imagem de fundo ou cor de fundo, consulte como [personalizar o UI com o modelo HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Descrição geral

Azure AD B2C fornece vários modelos incorporados que pode escolher para dar às páginas de experiência do utilizador um aspeto profissional. Estes modelos de página também podem servir de ponto de partida para a sua própria personalização, utilizando a funcionalidade [de marca da empresa.](#company-branding)

### <a name="ocean-blue"></a>Azul-do-oceano

Exemplo do modelo Ocean Blue renderizado no sinal de inscrição na página:

![Screenshot do modelo do azul do oceano](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Ardósia Cinza

Exemplo do modelo cinza de ardósia renderizado no sinal de inscrição na página:

![Screenshot do modelo cinza de ardósia](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Clássico

Exemplo do modelo clássico renderizado no sinal de inscrição na página:

![Screenshot clássico do modelo](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Imagem corporativa

Pode personalizar as suas páginas Azure AD B2C com um logotipo de banner, imagem de fundo e cor de fundo utilizando [a marca](../active-directory/fundamentals/customize-branding.md)Azure Ative Directory Company . A marca da empresa inclui a inscrição, inscrição, edição de perfis e reposição de passwords. 

O exemplo a seguir mostra um *Inscrição e sinal na* página com um logotipo personalizado, imagem de fundo, usando o modelo Ocean Blue:

![Página de inscrição/inscrição marcada servida por Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Selecione um modelo de página

::: zone pivot="b2c-user-flow"

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione um fluxo de utilizador que pretende personalizar.
1. Em **Personalizar** no menu esquerdo, selecione **layouts de página** e, em seguida, selecione um **modelo**.
    ![Drop-down de seleção de modelo na página de fluxo do utilizador do portal Azure](./media/customize-ui/select-page-template.png)

Ao escolher um modelo, o modelo selecionado é aplicado a todas as páginas do fluxo do seu utilizador. O URI para cada página é visível no campo **URI da página personalizada.**

::: zone-end

::: zone pivot="b2c-custom-policy"

Para selecionar um modelo de página, desafine o `LoadUri` elemento das [definições](contentdefinitions.md)de conteúdo . O exemplo a seguir mostra os identificadores de definição de conteúdo e os correspondentes `LoadUri` . 

Azul-do-Oceano:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Ardósia Cinza:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Clássico: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Configurar o branding da empresa

Para personalizar as suas páginas de fluxo de utilizador, primeiro configura a marca da empresa no Azure Ative Directory e, em seguida, ativo-a nos fluxos do seu utilizador em Azure AD B2C.

Comece por definir o logótipo do banner, imagem de fundo e cor de fundo dentro **da marca da Empresa.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Manage**, selecione **marca da Empresa.**
1. Siga os passos em [Adicionar marca à página de inscrição do Azure Ative Directory da sua organização.](../active-directory/fundamentals/customize-branding.md)

Tenha estas coisas em mente quando configurar a marca da empresa em Azure AD B2C:

* A marca da empresa em Azure AD B2C está atualmente limitada à **imagem de fundo,** **logotipo do banner** e personalização da cor de **fundo.** As outras propriedades no painel de marca da empresa, por exemplo, **configurações avançadas,** não são *suportadas.*
* Nas páginas de fluxo do utilizador, a cor de fundo é mostrada antes da imagem de fundo ser carregada. Recomendamos que escolha uma cor de fundo que corresponda de perto as cores da sua imagem de fundo para uma experiência de carregamento mais suave.
* O logótipo do banner aparece nos e-mails de verificação enviados aos seus utilizadores quando iniciam um fluxo de utilizador de inscrição.



## <a name="enable-company-branding-in-user-flow-pages"></a>Ativar a marca da empresa nas páginas de fluxo do utilizador

::: zone pivot="b2c-user-flow"

Depois de configurar a marca da empresa, ative-a nos fluxos do utilizador.

1. No menu esquerdo do portal Azure, selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **fluxos de utilizador (políticas)**.
1. Selecione o fluxo de utilizador para o qual pretende ativar a marca da empresa. A marca da empresa não é **suportada** para os tipos padrão *de inscrição do utilizador* e *edição* de perfil padrão.
1. Em **Personalizar, selecione** **layouts de página** e, em seguida, selecione a página que gostaria de marcar. Por exemplo, **selecione Unified's sign up ou iniciar sedução na página**.
1. Para a versão layout da **página (pré-visualização)**, escolha a versão **1.2.0** ou superior.
1. Selecione **Guardar**.

Se quiser marcar todas as páginas no fluxo do utilizador, desacorda a versão de layout da página para cada layout de página no fluxo do utilizador.

![Seleção de layout de página em Azure AD B2C no portal Azure](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Uma vez configurado o branding da empresa, ative-o na sua política personalizada. Configure a [versão de layout](contentdefinitions.md#migrating-to-page-layout) da página com a versão da página para todas `contract` *as* definições de conteúdo na sua política personalizada. O formato do valor deve conter a palavra `contract` : _urn:com:microsoft:aad:b2c:elements:**contract**:p age-name:version_. Para especificar um layout de página nas suas políticas personalizadas que usam um valor **datauri** antigo. Para mais informações, aprenda a [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página.

O exemplo a seguir mostra as definições de conteúdo com o respetivo contrato de página e o modelo da página *Ocean Blue:* 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

Encontre mais informações sobre como pode personalizar a interface do utilizador das suas aplicações em [Personalizar a interface de utilizador da sua aplicação em Azure Ative Directory B2C](customize-ui-with-html.md).
