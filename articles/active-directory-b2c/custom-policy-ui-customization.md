---
title: Personalize a interface de utilizador da sua app com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba personalizar uma interface de utilizador utilizando uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 340c9629af89bfacb85b37503743fc5770070ae3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951847"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Personalize a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao completar os passos deste artigo, cria uma política personalizada de inscrição e inscrição com a sua marca e aparência. Com o Azure Ative Directory B2C (Azure AD B2C), obtém-se o controlo quase total dos conteúdos HTML e CSS que são apresentados aos utilizadores. Quando utiliza uma política personalizada, configura a personalização da UI em XML em vez de utilizar controlos no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos em [Começar com políticas personalizadas.](custom-policy-get-started.md) Você deve ter uma política personalizada de trabalho para se inscrever e iniciar s inscrição com contas locais.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Modificar o ficheiro de extensões

Para configurar a personalização da UI, copie o **ContentDefinition** e os seus elementos infantis do ficheiro base para o ficheiro de extensões.

1. Abra o arquivo base da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Este ficheiro base é um dos ficheiros de política incluídos no pacote de iniciação de políticas personalizadas, que deveria ter obtido no pré-requisito, [começar com políticas personalizadas](./custom-policy-get-started.md).
1. Procure e copie todo o conteúdo do elemento **ContentDefinitions.**
1. Abra o ficheiro de extensão. Por exemplo, *TrustFrameworkExtensions.xml.* Procure o elemento **Blocos de Construção.** Se o elemento não existir, adicione-o.
1. Cole todo o conteúdo do elemento **ContentDefinitions** que copiou como criança do elemento **BuildingBlocks.**
1. Procure o elemento **ContentDefinition** que contém `Id="api.signuporsignin"` no XML que copiou.
1. Altere o valor de **LoadUri** para o URL do ficheiro HTML que carregou para armazenamento. Por exemplo, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    A sua política personalizada deve parecer o seguinte corte de código:

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

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Faça upload e teste a sua política personalizada atualizada

#### <a name="51-upload-the-custom-policy"></a>5.1 Carre faça o upload da política personalizada

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Procure e selecione **Azure AD B2C**.
1. No âmbito **de Políticas**, selecione Identity **Experience Framework**.
1. Selecione **a política personalizada do Upload**.
1. Faça o upload do ficheiro de extensões que alterou anteriormente.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Teste a política personalizada utilizando **Run agora**

1. Selecione a política que fez o upload e, em seguida, selecione **Executar agora**.
1. Você deve ser capaz de se inscrever usando um endereço de e-mail.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Configurar conteúdo de página personalizada dinâmico URI

Ao utilizar as políticas personalizadas Azure AD B2C, pode enviar um parâmetro no caminho URL ou uma cadeia de consultas. Ao transmitir o parâmetro para o ponto final HTML, pode alterar dinamicamente o conteúdo da página. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou de início de sessão do Azure AD B2C, com base num parâmetro que transmite a partir da sua aplicação Web ou móvel. O parâmetro pode ser qualquer [reclamação,](claim-resolver-overview.md)como o ID da aplicação, id idioma ou parâmetro de cadeia de consulta personalizada, como `campaignId` .

### <a name="sending-query-string-parameters"></a>Envio de parâmetros de cadeia de consulta

Para enviar parâmetros de cadeia de consulta, na política do [partido em apoio,](relyingparty.md)adicione um `ContentDefinitionParameters` elemento como mostrado abaixo.

```xml
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

Na sua definição de conteúdo, altere o valor de `LoadUri` `https://<app_name>.azurewebsites.net/home/unified` . A sua política personalizada `ContentDefinition` deve parecer o seguinte corte de código:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Quando o Azure AD B2C carrega a página, faz uma chamada para o ponto final do seu servidor web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI de conteúdo de página dinâmico

O conteúdo pode ser retirado de diferentes locais com base nos parâmetros utilizados. No seu ponto final ativado pelo CORS, crie uma estrutura de pasta para hospedar conteúdo. Por exemplo, pode organizar o conteúdo na seguinte estrutura. *Pasta/pasta raiz por linguagem/os seus ficheiros html*. Por exemplo, a sua página personalizada URI pode parecer:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C envia o código ISO de duas letras para a língua, `fr` para francês:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre elementos de UI que possam ser personalizados, consulte [o guia de referência para personalização de UI para fluxos de utilizador.](customize-ui-overview.md)