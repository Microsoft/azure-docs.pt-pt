---
title: Personalizar a interface do usuário do seu aplicativo com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba mais sobre como personalizar uma interface do usuário usando uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8e07d3e1815c1b47b9d37c08e8fac5359b71fe7c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245996"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao completar os passos deste artigo, cria uma política personalizada de inscrição e inscrição com a sua marca e aparência. Com o Azure Active Directory B2C (Azure AD B2C), você obtém controle quase total do conteúdo HTML e CSS que é apresentado aos usuários. Ao usar uma política personalizada, você configura a personalização da interface do usuário em XML em vez de usar controles na portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em introdução [às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada de trabalho para inscrever-se e entrar com contas locais.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

## <a name="4-modify-the-extensions-file"></a>4. Modificar o arquivo de extensões

Para configurar a personalização ui, copie a **Definição** de Conteúdo e os seus elementos infantis do ficheiro base para o ficheiro de extensões.

1. Abra o arquivo base da sua política. Por exemplo, <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em>. Este ficheiro base é um dos ficheiros de política incluídos no pacote de arranque de política personalizada, que deveria ter obtido no pré-requisito, [Começar com políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Pesquise e copie todo o conteúdo do elemento **ContentDefinitions** .
1. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions. xml*. Procure o elemento **BuildingBlocks** . Se o elemento não existir, adicione-o.
1. Cole todo o conteúdo do elemento **ContentDefinitions** que você copiou como um filho do elemento **BuildingBlocks** .
1. Procure o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML que você copiou.
1. Altere o valor de **LoadUri** para a URL do arquivo HTML que você carregou no armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

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

1. Salve o arquivo de extensões.

## <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Carregar e testar sua política personalizada atualizada

### <a name="51-upload-the-custom-policy"></a>5.1 Faça upload da política personalizada

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Procure e selecione **Azure ad B2C**.
1. Em **políticas**, selecione **estrutura de experiência de identidade**.
1. Selecione **carregar política personalizada**.
1. Carregue o arquivo de extensões que você alterou anteriormente.

### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Teste a política personalizada usando **Run agora**

1. Selecione a política que você carregou e, em seguida, selecione **executar agora**.
1. Você deve ser capaz de se inscrever usando um endereço de email.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurar conteúdo de página personalizada dinâmica URI

Ao utilizar políticas personalizadas Azure AD B2C, pode enviar um parâmetro no caminho url, ou uma corda de consulta. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. O parâmetro pode ser qualquer [reclamação resolver](claim-resolver-overview.md), como o ID da aplicação, id de idioma ou parâmetro de corda de consulta personalizada, como `campaignId`.

### <a name="sending-query-string-parameters"></a>Envio de parâmetros de cordas de consulta

Para enviar parâmetros de cordas de consulta, na [política do partido que depende,](relyingparty.md)adicione um elemento `ContentDefinitionParameters` como mostrado abaixo.

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

Na sua definição de conteúdo, altere o valor do `LoadUri` para `https://<app_name>.azurewebsites.net/home/unified`. A sua política personalizada `ContentDefinition` deve parecer o seguinte código:

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

Azure AD B2C envia o código ISO de duas letras para a língua, `fr` para francês:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre elementos de interface do usuário que podem ser personalizados, consulte o [Guia de referência para a personalização da interface do usuário para fluxos de usuários](customize-ui-overview.md).
