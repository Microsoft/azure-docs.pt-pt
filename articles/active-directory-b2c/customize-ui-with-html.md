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
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869cf5a47831844b04e0461a95fb7d16aa4d1569
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111492"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalize a interface do utilizador no Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Marcar e personalizar a interface de utilizador que o Azure Ative Directory B2C (Azure AD B2C) exibe aos seus clientes ajuda a proporcionar uma experiência de utilizador perfeita na sua aplicação. Estas experiências incluem inscrição, inscrição, edição de perfis e reposição de passwords. Este artigo introduz os métodos de personalização da interface do utilizador (UI). 

> [!TIP]
> Se pretender modificar apenas o logótipo do banner, a imagem de fundo e a cor de fundo das páginas de fluxo do utilizador, pode experimentar a funcionalidade [de marca Da Empresa.](company-branding.md)


## <a name="custom-html-and-css-overview"></a>Visão geral personalizada de HTML e CSS


O Azure AD B2C executa código no navegador do seu cliente utilizando [a Partilha de Recursos de Origem Cruzada (CORS)](https://www.w3.org/TR/cors/). No tempo de execução, o conteúdo é carregado a partir de um URL que especifica no fluxo do utilizador ou na política personalizada. Cada página na experiência do utilizador carrega o seu conteúdo a partir do URL que especifica para essa página. Depois de o conteúdo ser carregado a partir do seu URL, é fundido com um fragmento HTML inserido pelo Azure AD B2C e, em seguida, a página é exibida ao seu cliente.

![Margem de conteúdo de página personalizada](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>Conteúdo personalizado da página HTML

Crie uma página HTML com a sua própria marca para servir o conteúdo da sua página personalizada. Esta página pode ser uma `*.html` página estática, ou uma página dinâmica como .NET, Node.js ou PHP.

O conteúdo da sua página personalizada pode conter quaisquer elementos HTML, incluindo CSS e JavaScript, mas não pode incluir elementos inseguros como os iframes. O único elemento necessário é um elemento de div com `id` definido para , tal como este dentro da sua página `api` `<div id="api"></div>` HTML.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>Personalize as páginas AZURE AD B2C padrão

Em vez de criar o conteúdo da sua página personalizada do zero, pode personalizar o conteúdo padrão da página do Azure AD B2C.

A tabela que se segue lista o conteúdo da página predefinida fornecido pelo Azure AD B2C. Descarregue os ficheiros e use-os como ponto de partida para criar as suas próprias páginas personalizadas.

| Página padrão | Descrição | ID de definição de conteúdo<br/>(apenas política personalizada) |
|:-----------------------|:--------|-------------|
| [exception.htm](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Página de erro**. Esta página é apresentada quando se encontra uma exceção ou um erro. | *api.erro* |
| [selfasserted.htm](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **Página auto-assertada.** Utilize este ficheiro como conteúdo de página personalizada para uma página de inscrição de conta social, uma página de inscrição de conta local, uma página de inscrição de conta local, redefinição de senha e muito mais. O formulário pode conter vários controlos de entrada, tais como: uma caixa de entrada de texto, uma caixa de entrada de senha, um botão de rádio, caixas de entrega de uma única seleção e caixas de verificação multi-selecionadas. | *api.localaccountsignin,* *api.localaccountsignup,* *api.localaccountpasswordreset,* *api.selfasserted* |
| [multifactor-1.0.0.htm](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Página de autenticação multi-factor**. Nesta página, os utilizadores podem verificar os seus números de telefone (utilizando texto ou voz) durante a inscrição ou a inscrição. | *api.phonefactor* |
| [updateprofile.htm](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Página de atualização de perfis**. Esta página contém um formulário que os utilizadores podem aceder para atualizar o seu perfil. Esta página é semelhante à página de inscrição de conta social, exceto para os campos de entrada de senha. | *api.selfasserted.profileupdate* |
| [unified.htm](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Página de inscrição ou inscrição** unificada . Esta página trata do processo de inscrição e inscrição do utilizador. Os utilizadores podem usar fornecedores de identidade empresarial, fornecedores de identidade social, como facebook ou Google+, ou contas locais. | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>Hospedar o conteúdo da página

Ao utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI, hospede o seu conteúdo de UI em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob](../storage/blobs/storage-blobs-introduction.md), [Azure App Services,](../app-service/index.yml)servidores web, CDNs, AWS S3 ou sistemas de partilha de ficheiros.

## <a name="guidelines-for-using-custom-page-content"></a>Diretrizes para a utilização de conteúdo de página personalizado

- Utilize um URL absoluto quando incluir recursos externos como suportes, ficheiros CSS e JavaScript no seu ficheiro HTML.
- Utilizando [a versão de layout](page-layout.md) da página 1.2.0 ou superior, pode adicionar o atributo nas suas `data-preload="true"` tags HTML para controlar a ordem de carga para CSS e JavaScript. Com `data-preload=true` , a página é construída antes de ser mostrada ao utilizador. Este atributo ajuda a evitar que a página "cintile" pré-carregando o ficheiro CSS, sem que o HTML não-estilo seja mostrado ao utilizador. O seguinte corte de código HTML mostra a utilização da `data-preload` etiqueta.
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- Recomendamos que comece com o conteúdo da página predefinido e construa em cima dele.
- Pode [incluir o JavaScript](javascript-and-page-layout.md) no seu conteúdo personalizado.
- As versões de navegador suportadas são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Suporte limitado para Internet Explorer 9 e 8
  - Google Chrome 42.0 e acima
  - Mozilla Firefox 38.0 e acima
  - Safari para iOS e macOS, versão 12 e superior
- Devido a restrições de segurança, o Azure AD B2C não suporta `frame` `iframe` elementos , ou `form` HTML.

## <a name="localize-content"></a>Localize conteúdo

Localize o seu conteúdo HTML, permitindo a [personalização de linguagem](language-customization.md) no seu inquilino Azure AD B2C. Ativar esta função permite que o Azure AD B2C reencamide o parâmetro OpenID Connect `ui_locales` para o seu ponto final. O seu servidor de conteúdo pode utilizar este parâmetro para fornecer páginas HTML específicas da linguagem.

O conteúdo pode ser retirado de diferentes lugares com base no local que é usado. No seu ponto final ativado pelo CORS, cria uma estrutura de pasta para hospedar conteúdo para idiomas específicos. Você vai chamar o certo se você usar o valor wildcard `{Culture:RFC5646}` .

Por exemplo, a sua página personalizada URI pode parecer:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Pode carregar a página em francês puxando conteúdo de:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>Walkthrough de conteúdo de página personalizada

Aqui está uma visão geral do processo:

1. Prepare uma localização para hospedar o seu conteúdo de página personalizado (um ponto final HTTPS acessível ao público, ativado pelo CORS).
1. Faça o download e personalize um ficheiro de conteúdo de página predefinido, por `unified.html` exemplo.
1. Publique o seu conteúdo de página personalizado o seu ponto final HTTPS disponível publicamente.
1. Desaprove a partilha de recursos de origem cruzada (CORS) para a sua aplicação web.
1. Aponte a sua política para o seu conteúdo de política personalizada URI.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


### <a name="1-create-your-html-content"></a>1. Crie o seu conteúdo HTML

Crie um conteúdo de página personalizado com a marca do seu produto no título.

1. Copie o seguinte corte HTML. É html5 bem formado com um elemento vazio chamado *\<div id="api"\>\</div\>* localizado dentro das *\<body\>* etiquetas. Este elemento indica onde deve ser inserido o conteúdo Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Cole o corte copiado em um editor de texto
1. Utilize o CSS para modelar os elementos UI que o Azure AD B2C insere na sua página. O exemplo a seguir mostra um simples ficheiro CSS que também inclui definições para os elementos HTML injetados:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  Guarde o ficheiro como *customize-ui.html*.

> [!NOTE]
> Os elementos do formulário HTML serão removidos devido a restrições de segurança se utilizar login.microsoftonline.com. Se pretender utilizar elementos de forma HTML no seu conteúdo HTML personalizado, [utilize b2clogin.com](b2clogin.md).

### <a name="2-create-an-azure-blob-storage-account"></a>2. Criar uma conta de armazenamento Azure Blob

Neste artigo, utilizamos o armazenamento Azure Blob para hospedar o nosso conteúdo. Pode optar por hospedar o seu conteúdo num servidor web, mas tem de [ativar o CORS no seu servidor web.](https://enable-cors.org/server.html)

Para hospedar o seu conteúdo HTML no armazenamento Blob, execute os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu **Hub,** selecione **Nova** conta de Armazenamento de  >    >  **Armazenamento.**
1. Selecione uma **Subscrição** para a sua conta de armazenamento.
1. Crie um **grupo de Recursos** ou selecione um existente.
1. Insira um **Nome** único para a sua conta de armazenamento.
1. Selecione a **localização Geográfica** para a sua conta de armazenamento.
1. **O modelo de implementação** pode continuar a ser **o Gestor de Recursos.**
1. **O desempenho** pode continuar a ser **Standard**.
1. Alterar **Conta Tipo** para **Blob armazenamento**.
1. **A replicação** pode permanecer **RA-GRS**.
1. **O nível de acesso** pode permanecer **quente**.
1. Selecione **Review + criar** para criar a conta de armazenamento.
    Após a implementação concluída, a página **da conta de Armazenamento** abre-se automaticamente.

#### <a name="21-create-a-container"></a>2.1 Criar um recipiente

Para criar um recipiente público no armazenamento blob, execute os seguintes passos:

1. No **serviço Blob** no menu da esquerda, selecione **Blobs**.
1. Selecione **+Recipiente**.
1. Para **nome,** *introduza raiz*. O nome pode ser um nome à sua escolha, por exemplo, *contoso,* mas usamos *raiz* neste exemplo para simplicidade.
1. Para **o nível de acesso público**, selecione **Blob,** em **seguida, OK**.
1. Selecione **raiz** para abrir o novo recipiente.

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 Faça upload dos seus ficheiros de conteúdo de página personalizada

1. Selecione **Carregar**.
1. Selecione o ícone de pasta ao lado **de Selecionar um ficheiro**.
1. Navegue e selecione **customize-ui.html**, que criou anteriormente na secção de personalização da Página UI.
1. Se pretender fazer o upload para uma sub-dobragem, expanda **o Advanced** e introduza um nome de pasta no Upload para **a pasta**.
1. Selecione **Carregar**.
1. Selecione a **bolha decustomize-ui.html** que fez o upload.
1. À direita da caixa de texto **URL,** selecione o ícone **copy to clipboard** para copiar o URL para a sua área de transferência.
1. No navegador web, navegue para o URL que copiou para verificar se a bolha que carregou está acessível. Se for inacessível, por exemplo, se encontrar um `ResourceNotFound` erro, certifique-se de que o tipo de acesso ao recipiente está definido para **borbulhar**.

### <a name="3-configure-cors"></a>3. Configure CORS

Configure o armazenamento blob para partilha de recursos de origem cruzada executando os seguintes passos:

1. No menu, selecione **CORS**.
1. Para **origens permitidas,** insira `https://your-tenant-name.b2clogin.com` . `your-tenant-name`Substitua-o pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Use todas as letras minúsculas ao inserir o nome do seu inquilino.
1. Para **Métodos Permitidos**, selecione ambos `GET` e `OPTIONS` .
1. Para **cabeçalhos permitidos,** introduza um asterisco (*).
1. Para **cabeçalhos expostos,** introduza um asterisco (*).
1. Para **a idade máxima,** insira 200.
1. Selecione **Guardar**.

#### <a name="31-test-cors"></a>3.1 Ensaio CORS

Valide que está pronto executando os seguintes passos:

1. Repita o passo DE CONFIGURAÇÃO CORS. Para **origens permitidas,** insira `https://www.test-cors.org`
1. Navegue até [www.test-cors.org](https://www.test-cors.org/) 
1. Para a caixa **de URL remota,** cole o URL do seu ficheiro HTML. Por exemplo, `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. Selecione **Enviar Pedido**.
    O resultado deve ser `XHR status: 200` . 
    Se receber um erro, certifique-se de que as definições do CORS estão corretas. Também poderá ter de limpar a cache do seu navegador ou abrir uma sessão de navegação privada pressionando ctrl+Shift+P.


::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. Atualizar o fluxo do utilizador

1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **os fluxos do Utilizador** e, em seguida, selecione o fluxo do utilizador *B2C_1_signupsignin1.*
1. Selecione **os layouts de página** e, em seguida, na página de **inscrição ou de inscrição unificada**, clique em **Sim** para Utilizar o conteúdo da **página personalizada**.
1. Na **página personalizada URI**, insira o URI para o *ficheirocustom-ui.html* que gravou anteriormente.
1. No topo da página, **selecione Guardar**.

### <a name="5-test-the-user-flow"></a>5. Testar o fluxo do utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador** e selecione o fluxo de utilizador *B2C_1_signupsignin1.*
1. No topo da página, clique no fluxo do **utilizador executar**.
1. Clique no botão **de fluxo do utilizador Executar.**

Deve ver uma página semelhante ao exemplo a seguir com os elementos centrados no ficheiro CSS que criou:

![Navegador web mostrando inscrição ou sinal na página com elementos de UI personalizados](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

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

::: zone-end

## <a name="sample-templates"></a>Modelos de exemplo

Pode encontrar modelos de amostra para personalização da UI aqui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Este projeto contém os seguintes modelos:
- [Azul-do-oceano](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Ardósia Cinza](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Para utilizar a amostra:

1. Clone o repo na sua máquina local. Escolha uma pasta de modelo `/ocean_blue` ou `/slate_gray` .
1. Faça o upload de todos os ficheiros sob a pasta do modelo e da `/assets` pasta, para o armazenamento blob, conforme descrito nas secções anteriores.
1. Em seguida, abra cada `\*.html` ficheiro na raiz de qualquer um ou `/ocean_blue` `/slate_gray` substitua todas as instâncias de URLs relativos com os URLs dos ficheiros css, imagens e tipos de letra que carregou no passo 2. Por exemplo:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Para
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Guarde os `\*.html` ficheiros e faça o upload para o armazenamento da Blob.
1. Agora modifique a política, apontando para o seu ficheiro HTML, como mencionado anteriormente.
1. Se vir tipos de letra, imagens ou CSS em falta, verifique as suas referências na política de extensões e nos \* ficheiros .html.


## <a name="next-steps"></a>Passos seguintes

Saiba como ativar o [código JavaScript do lado do cliente.](javascript-and-page-layout.md)



