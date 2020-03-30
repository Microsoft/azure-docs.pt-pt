---
title: Personalize a interface de utilizador da sua aplicação com uma política personalizada
titleSuffix: Azure AD B2C
description: Aprenda a personalizar uma interface de utilizador usando uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87c3a3a904705b9fcb702c4745c4c80a4b447e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476727"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalize a interface de utilizador da sua aplicação utilizando uma política personalizada no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao completar os passos deste artigo, cria uma política personalizada de inscrição e inscrição com a sua marca e aparência. Com o Azure Ative Directory B2C (Azure AD B2C), obtém-se o controlo quase total dos conteúdos HTML e CSS que são apresentados aos utilizadores. Quando utiliza uma política personalizada, configura a personalização ui em XML em vez de utilizar controlos no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Get started com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar sessão com contas locais.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

## <a name="4-modify-the-extensions-file"></a>4. Modificar o ficheiro de extensões

Para configurar a personalização ui, copie a **Definição** de Conteúdo e os seus elementos infantis do ficheiro base para o ficheiro de extensões.

1. Abra o ficheiro base da sua apólice. Por exemplo, <em> `SocialAndLocalAccounts/` </em>. Este ficheiro base é um dos ficheiros de política incluídos no pacote de arranque de política personalizada, que deveria ter obtido no pré-requisito, [Começar com políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Procure e copie todo o conteúdo do elemento Definições de **Conteúdo.**
1. Abra o ficheiro de extensão. Por exemplo, *TrustFrameworkExtensions.xml*. Procure o elemento **BuildingBlocks.** Se o elemento não existir, adicione-o.
1. Colar todo o conteúdo do elemento **ContentDefinitions** que copiou em criança do elemento **BuildingBlocks.**
1. Procure o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML que copiou.
1. Altere o valor do **LoadUri** para o URL do ficheiro HTML que carregou para o armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    A sua política personalizada deve parecer o seguinte código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Guarde o ficheiro de extensões.

## <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Faça upload e teste a sua política personalizada atualizada

### <a name="51-upload-the-custom-policy"></a>5.1 Faça upload da política personalizada

1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Procure e **selecione Azure AD B2C**.
1. No âmbito **das Políticas,** selecione Quadro de **Experiência de Identidade**.
1. Selecione **a política personalizada de upload**.
1. Faça upload do ficheiro de extensões que alterou anteriormente.

### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Teste a política personalizada usando **Run agora**

1. Selecione a política que carregou e, em seguida, selecione **Executar agora**.
1. Deverá poder inscrever-se através de um endereço de e-mail.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurar conteúdo de página personalizada dinâmica URI

Ao utilizar políticas personalizadas Azure AD B2C, pode enviar um parâmetro no caminho url, ou uma corda de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. O parâmetro pode ser qualquer [reclamação resolver](claim-resolver-overview.md), como o ID da aplicação, `campaignId`id de idioma ou parâmetro de corda de consulta personalizada, como .

### <a name="sending-query-string-parameters"></a>Envio de parâmetros de cordas de consulta

Para enviar parâmetros de cordas de consulta, na `ContentDefinitionParameters` política do partido que [depende,](relyingparty.md)adicione um elemento como mostrado abaixo.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Na sua definição de `LoadUri` conteúdo, altere o valor de `https://<app_name>.azurewebsites.net/home/unified`. A sua `ContentDefinition` política personalizada deve parecer o seguinte código:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando o Azure AD B2C carrega a página, faz uma chamada para o ponto final do seu servidor web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Conteúdo de página dinâmica URI

O conteúdo pode ser retirado de diferentes locais com base nos parâmetros utilizados. No seu ponto final ativado pelo CORS, instale uma estrutura de pasta para hospedar conteúdo. Por exemplo, pode organizar o conteúdo na seguinte estrutura. *Pasta/pasta de raiz por idioma/ficheiros html*. Por exemplo, a sua página personalizada URI pode parecer:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envia o código ISO `fr` de duas letras para a língua, para o francês:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre elementos UI que podem ser personalizados, consulte o guia de referência para a personalização de [UI para fluxos de utilizadores](customize-ui-overview.md).
