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
ms.date: 09/24/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4566e3d40a67a9cd9d61f78884df11536c0809bc
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949297"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalize a interface do utilizador no Azure Ative Directory B2C

Marcar e personalizar a interface de utilizador que o Azure Ative Directory B2C (Azure AD B2C) exibe aos seus clientes ajuda a proporcionar uma experiência de utilizador perfeita na sua aplicação. Estas experiências incluem inscrição, inscrição, edição de perfis e reposição de passwords. Este artigo introduz os métodos de personalização da interface do utilizador (UI) tanto para os fluxos de utilizador como para as políticas personalizadas.

## <a name="ui-customization-in-different-scenarios"></a>Personalização da UI em diferentes cenários

Existem várias formas de personalizar a UI das experiências do utilizador na sua aplicação, cada uma adequada para diferentes cenários.

### <a name="user-flows"></a>Fluxos do utilizador

Se utilizar [fluxos de utilizador,](user-flow-overview.md)pode alterar a aparência das páginas de fluxo do utilizador utilizando modelos de layout de *página incorporados,* ou utilizando o seu próprio HTML e CSS. Ambos os métodos são discutidos mais tarde neste artigo.

Utilize o [portal Azure](tutorial-customize-ui.md) para configurar a personalização da UI para os fluxos dos utilizadores.

> [!TIP]
> Se pretender modificar apenas o logótipo do banner, a imagem de fundo e a cor de fundo das páginas de fluxo do utilizador, pode experimentar a funcionalidade [de marca Da Empresa](#company-branding) descrita mais tarde neste artigo.

### <a name="custom-policies"></a>Políticas personalizadas

Se estiver a utilizar [políticas personalizadas](custom-policy-overview.md) para fornecer inscrição ou inscrição, redefinição de palavra-passe ou edição de perfis na sua aplicação, utilize [ficheiros de política para personalizar o UI](custom-policy-ui-customization.md).

Se precisar de fornecer conteúdo dinâmico com base na decisão de um cliente, utilize políticas personalizadas que possam alterar o conteúdo da [página dinamicamente](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri) dependendo de um parâmetro enviado numa cadeia de consultas. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou inscrição Azure AD B2C com base num parâmetro que passa a partir da sua web ou aplicação móvel.

### <a name="javascript"></a>JavaScript

Pode ativar o código JavaScript do lado do cliente tanto nos [fluxos do utilizador](user-flow-javascript-overview.md) como nas políticas [personalizadas.](page-layout.md)

### <a name="sign-in-only-ui-customization"></a>Iniciar a personalização da UI apenas

Se estiver a fornecer apenas o início de sposição, juntamente com a página de reset da palavra-passe que acompanha a página e os e-mails de verificação, utilize os mesmos passos de personalização que são utilizados para uma [página de entrada AD AZure](../active-directory/fundamentals/customize-branding.md).

Se os clientes tentarem editar o seu perfil antes de iniciar sessão, são redirecionados para uma página que personaliza utilizando os mesmos passos que são usados para personalizar a página de pré-in Azure AD.

## <a name="page-layout-templates"></a>Modelos de layout de página

Os fluxos de utilizador fornecem vários modelos incorporados que pode escolher para dar às páginas de experiência do utilizador um aspeto profissional. Estes modelos de layout também podem e servem como ponto de partida para a sua própria personalização.

Em **Personalizar** no menu esquerdo, selecione **layouts de página** e, em seguida, selecione **Modelo**.

![Drop-down de seleção de modelo na página de fluxo do utilizador do portal Azure](media/customize-ui-overview/template-selection.png)

Em seguida, selecione um modelo da lista. Aqui estão exemplos das páginas de inscrição para cada modelo:

| Azul-do-oceano | Ardósia Cinza | Clássico |
|:-:|:-:|:-:|
|![Exemplo do modelo Ocean Blue renderizado no sinal de inscrição na página](media/customize-ui-overview/template-ocean-blue.png)|![Exemplo do modelo cinza de ardósia renderizado no sinal de inscrição na página](media/customize-ui-overview/template-slate-gray.png)|![Exemplo do modelo clássico renderizado no sinal de inscrição na página](media/customize-ui-overview/template-classic.png)|

Ao escolher um modelo, o layout selecionado é aplicado a todas as páginas do fluxo do seu utilizador e o URI para cada página é visível no campo URI da **página personalizada.**

## <a name="custom-html-and-css"></a>HTML e CSS personalizados

Se desejar conceber o seu próprio layout de política com o seu HTML e CSS personalizados, pode fazê-lo trocando o alternador de "Use conteúdo de página personalizada" para cada um dos nomes de Layout presentes na sua política. Siga as instruções abaixo relativas às configurações de layout personalizadas:

O Azure AD B2C executa código no navegador do seu cliente utilizando uma abordagem chamada [Partilha de Recursos de Origem Cruzada (CORS)](https://www.w3.org/TR/cors/).

No tempo de execução, o conteúdo é carregado a partir de um URL que especifica no fluxo do utilizador ou na política personalizada. Cada página na experiência do utilizador carrega o seu conteúdo a partir do URL que especifica para essa página. Depois de o conteúdo ser carregado a partir do seu URL, é fundido com um fragmento HTML inserido pelo Azure AD B2C e, em seguida, a página é exibida ao seu cliente.

Reveja as seguintes orientações antes de utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI:

- Azure AD B2C **funde** o conteúdo HTML nas suas páginas. Não copie e tente alterar o conteúdo predefinido que o Azure AD B2C fornece. É melhor construir o seu conteúdo HTML a partir do zero e usar o conteúdo predefinido como referência.
- **O JavaScript** pode ser incluído no seu conteúdo personalizado tanto para fluxos de utilizador como [para políticas](javascript-samples.md) [personalizadas.](user-flow-javascript-overview.md)
- As **versões de navegador suportadas** são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Suporte limitado para Internet Explorer 9 e 8
  - Google Chrome 42.0 e acima
  - Mozilla Firefox 38.0 e acima
  - Safari para iOS e macOS, versão 12 e superior
- Não inclua **etiquetas de formulário** no seu HTML. As etiquetas de formulário interferem com as operações POST geradas pelo HTML injetado pelo Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Onde guardo conteúdo de UI?

Ao utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI, pode hospedar o seu conteúdo de UI em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento Azure Blob,](../storage/blobs/storage-blobs-introduction.md)servidores web, CDNs, AWS S3 ou sistemas de partilha de ficheiros.

O importante é que hospeda o conteúdo num ponto final HTTPS disponível publicamente com [o CORS ativado](https://enable-cors.org/server.html). Deve utilizar um URL absoluto quando o especificar no seu conteúdo.

> [!NOTE]
> Para obter detalhes sobre a criação de conteúdo HTML, o upload de conteúdos para o armazenamento de Azure Blob e a configuração do CORS, consulte a secção de artigo de personalização de conteúdos de [página personalizada.](custom-policy-ui-customization.md#custom-page-content-walkthrough)

## <a name="get-started-with-custom-html-and-css"></a>Começar com HTML e CSS personalizados

Inicie a utilização do seu próprio HTML e CSS nas suas páginas de experiência de utilizador seguindo estas diretrizes.

- Crie conteúdo HTML bem formado com um elemento vazio `<div id="api"></div>` localizado algures no `<body>` . Este elemento marca onde o conteúdo Azure AD B2C é inserido. O exemplo a seguir mostra uma página mínima:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Utilize o CSS para modelar os elementos UI que o Azure AD B2C insere na sua página. O exemplo a seguir mostra um simples ficheiro CSS que também inclui definições para os elementos HTML injetados:

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

- Hospedar o seu conteúdo num ponto final HTTPS (com o CORS permitido). Os métodos de pedido GET e OPTIONS devem ser ativados ao configurar o CORS.
- Crie ou edite uma política de fluxo de utilizador ou de costume para utilizar o conteúdo que criou.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmentos html de Azure AD B2C

A tabela que se segue lista os fragmentos HTML que o Azure AD B2C se funde no `<div id="api"></div>` elemento localizado no seu conteúdo.

| Página inserida | Descrição de HTML |
| ------------- | ------------------- |
| Seleção de fornecedores de identidade | Contém uma lista de botões para fornecedores de identidade que o cliente pode escolher durante a inscrição ou a inscrição. Estes botões incluem fornecedores de identidade social, como Facebook, Google ou contas locais (com base no endereço de e-mail ou nome de utilizador). |
| Inscrição de conta local | Contém um formulário para inscrição de conta local com base num endereço de e-mail ou num nome de utilizador. O formulário pode conter diferentes controlos de entrada, tais como caixa de entrada de texto, caixa de entrada de palavra-passe, botão de rádio, caixas de entrega de uma única seleção e caixas de verificação multi-selecionadas. |
| Inscrição de conta social | Pode aparecer quando se inscreve usando uma conta existente de um fornecedor de identidade social, como facebook ou Google. É usado quando informações adicionais devem ser recolhidas do cliente usando um formulário de inscrição. |
| Inscrição ou inscrição unificada | Lida com a inscrição e o sismo de clientes que podem utilizar fornecedores de identidade social como facebook, Google ou contas locais. |
| Autenticação multifator | Os clientes podem verificar os seus números de telefone (usando texto ou voz) durante a inscrição ou a inscrição. |
| Erro | Fornece informações de erro ao cliente. |

## <a name="company-branding"></a>Imagem corporativa

Pode personalizar as páginas de fluxo do utilizador com um logotipo de banner, imagem de fundo e cor de fundo utilizando [a marca](../active-directory/fundamentals/customize-branding.md)Azure Ative Directory Company .

Para personalizar as suas páginas de fluxo de utilizador, primeiro configura a marca da empresa no Azure Ative Directory, depois ativa-a nos layouts de página dos fluxos do seu utilizador em Azure AD B2C.

### <a name="configure-company-branding"></a>Configurar o branding da empresa

Comece por definir o logótipo do banner, imagem de fundo e cor de fundo dentro **da marca da Empresa.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Manage**, selecione **marca da Empresa.**
1. Siga os passos em [Adicionar marca à página de inscrição do Azure Ative Directory da sua organização.](../active-directory/fundamentals/customize-branding.md)

Tenha estas coisas em mente quando configurar a marca da empresa em Azure AD B2C:

* A marca da empresa em Azure AD B2C está atualmente limitada à **imagem de fundo,** **logotipo do banner**e personalização da cor de **fundo.** As outras propriedades no painel de marca da empresa, por exemplo, as que se encontram em **configurações Avançadas,** não são *suportadas.*
* Nas páginas de fluxo do utilizador, a cor de fundo é mostrada antes da imagem de fundo ser carregada. Recomendamos que escolha uma cor de fundo que corresponda de perto as cores da sua imagem de fundo para uma experiência de carregamento mais suave.

### <a name="enable-branding-in-user-flow-pages"></a>Ativar a marca nas páginas de fluxo do utilizador

Depois de configurar a marca da empresa, ative-a nos fluxos do utilizador.

1. No menu esquerdo do portal Azure, selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **fluxos de utilizador (políticas)**.
1. Selecione o fluxo de utilizador para o qual pretende ativar a marca da empresa. A marca da empresa não é **suportada** para os tipos padrão *de inscrição do utilizador* e *edição* de perfil padrão.
1. Em **Personalizar, selecione** **layouts de página**e, em seguida, selecione o layout que gostaria de marcar. Por exemplo, **selecione Unified's sign up ou iniciar sedução na página**.
1. Para a versão layout da **página (pré-visualização)**, escolha a versão **1.2.0** ou superior.
1. Selecione **Guardar**.

Se quiser marcar todas as páginas no fluxo do utilizador, desacorda a versão de layout da página para cada layout de página no fluxo do utilizador.

![Seleção de layout de página em Azure AD B2C no portal Azure](media/customize-ui-overview/portal-02-page-layout-select.png)

Este exemplo anotado mostra um logotipo de banner personalizado e imagem de fundo em um *Sign up e assinar na* página de fluxo do utilizador que usa o modelo Ocean Blue:

![Página de inscrição/inscrição marcada servida por Azure AD B2C](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>Utilize ativos de marca da empresa em HTML personalizado

Para utilizar os ativos de marca da sua empresa em HTML personalizado, adicione as seguintes etiquetas fora da `<div id="api">` etiqueta:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

A fonte de imagem é substituída pela imagem de fundo e logotipo do banner. Conforme descrito na secção Get start com a secção [HTML e CSS personalizadas,](#get-started-with-custom-html-and-css) use as classes CSS para modelar e posicionar os ativos na página .

## <a name="localize-content"></a>Localize conteúdo

Localize o seu conteúdo HTML, permitindo a [personalização de linguagem](user-flow-language-customization.md) no seu inquilino Azure AD B2C. Ativar esta função permite que o Azure AD B2C reencamide o parâmetro OpenID Connect `ui_locales` para o seu ponto final. O seu servidor de conteúdo pode utilizar este parâmetro para fornecer páginas HTML específicas da linguagem.

O conteúdo pode ser retirado de diferentes lugares com base no local que é usado. No seu ponto final ativado pelo CORS, cria uma estrutura de pasta para hospedar conteúdo para idiomas específicos. Você vai chamar o certo se você usar o valor wildcard `{Culture:RFC5646}` .

Por exemplo, a sua página personalizada URI pode parecer:

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Pode carregar a página em francês puxando conteúdo de:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exemplos

Pode encontrar vários ficheiros de modelos de amostra no repositório [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) no GitHub.

Os ficheiros HTML e CSS da amostra nos modelos estão localizados no [diretório /sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Passos seguintes

- Se estiver a utilizar **fluxos de utilizador,** pode começar a personalizar a sua UI com o tutorial:

    [Personalize a interface de utilizador das suas aplicações no Azure Ative Directory B2C](tutorial-customize-ui.md).
- Se estiver a utilizar **políticas personalizadas,** pode começar a personalizar a UI com o artigo:

    [Personalize a interface de utilizador da sua aplicação utilizando uma política personalizada no Azure Ative Directory B2C](custom-policy-ui-customization.md).
