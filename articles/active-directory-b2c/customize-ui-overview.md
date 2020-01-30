---
title: Personalizar a interface de utilizador
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface de utilizador para as suas aplicações que utilizam o Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d14e6f98f49f112c8b20abec573b48c3b12705db
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841238"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalize a interface de utilizador no Diretório Ativo Azure B2C

Branding e personalizar a interface de utilizador que o Azure Ative Directory B2C (Azure AD B2C) exibe aos seus clientes ajuda a proporcionar uma experiência perfeita ao utilizador na sua aplicação. Estas experiências incluem a inscrição, inscrição, edição de perfis e redefinição de palavras-passe. Este artigo introduz os métodos de personalização da interface do utilizador (UI) tanto para fluxos de utilizadores como para políticas personalizadas.

## <a name="ui-customization-in-different-scenarios"></a>Personalização da UI em diferentes cenários

Existem várias formas de personalizar o UI do utilizador experimenta a sua aplicação, cada uma adequada para diferentes cenários.

### <a name="user-flows"></a>Fluxos do utilizador

Se utilizar [os fluxos](user-flow-overview.md)de utilizador, pode alterar a aparência das páginas de fluxo do utilizador utilizando modelos de layout de *página incorporados,* ou utilizando o seu próprio HTML e CSS. Ambos os métodos são discutidos mais tarde neste artigo.

Utiliza o [portal Azure](tutorial-customize-ui.md) para configurar a personalização da UI para os fluxos de utilizadores.

### <a name="custom-policies"></a>Políticas personalizadas

Se estiver a utilizar [políticas personalizadas](custom-policy-overview.md) para fornecer inscrição ou inscrição, reset de palavra-passe ou edição de perfil na sua aplicação, utilize [ficheiros de política para personalizar o UI](custom-policy-ui-customization.md).

Se precisar de fornecer conteúdo dinâmico com base na decisão do cliente, utilize políticas personalizadas que possam [alterar o conteúdo da página dinamicamente](custom-policy-ui-customization-dynamic.md) dependendo de um parâmetro que é enviado numa cadeia de consulta. Por exemplo, pode alterar a imagem de fundo na página de inscrição ou inscrição do Azure AD B2C com base num parâmetro que passa da sua aplicação web ou móvel.

### <a name="javascript"></a>Javascript

Pode ativar o código JavaScript do lado do cliente tanto nos [fluxos](user-flow-javascript-overview.md) do utilizador como nas [políticas personalizadas.](page-layout.md)

### <a name="sign-in-only-ui-customization"></a>Assine apenas a personalização da UI

Se estiver a fornecer apenas o sessão, juntamente com a página de reset da palavra-passe e os e-mails de verificação, utilize os mesmos passos de personalização que são utilizados para uma [página de entrada de Anúncio sinuoso Azure.](../active-directory/fundamentals/customize-branding.md)

Se os clientes tentarem editar o seu perfil antes de iniciarem sessão, são redirecionados para uma página que personaliza utilizando os mesmos passos que são usados para personalizar a página de entrada de ad.a.

## <a name="page-layout-templates"></a>Modelos de layout de página

Os fluxos de utilizador fornecem vários modelos incorporados que pode escolher para dar ao seu utilizador páginas de experiência um visual profissional. Estes modelos de layout também podem e servem como ponto de partida para a sua própria personalização.

Em **'Personalizar'** no menu esquerdo, selecione **os layouts da página** e, em seguida, selecione **Modelo**.

![Queda da seleção do modelo na página de fluxo do utilizador do portal Azure](media/customize-ui-overview/template-selection.png)

Em seguida, selecione um modelo da lista. Aqui estão exemplos das páginas de inscrição para cada modelo:

| Oceano Azul | Cinza de ardósia | Clássico |
|:-:|:-:|:-:|
|![Exemplo do modelo Ocean Blue renderizado no sinal de inscrição na página](media/customize-ui-overview/template-ocean-blue.png)|![Exemplo do modelo De Ardósia Cinza renderizado no sinal de inscrição na página](media/customize-ui-overview/template-slate-gray.png)|![Exemplo do modelo clássico renderizado no sinal de inscrição na página](media/customize-ui-overview/template-classic.png)|

Quando escolhe um modelo, o layout selecionado é aplicado a todas as páginas do fluxo do utilizador, e o URI para cada página é visível no campo URI da **página Personalizada.**

## <a name="custom-html-and-css"></a>HTML personalizado e CSS

Se desejar desenhar o seu próprio layout de política com o seu HTML e CSS personalizados, pode fazê-lo trocando o toggle "Use o conteúdo de página personalizado" para cada um dos nomes de Layout presentes na sua política. Siga as instruções abaixo relativas às configurações de layout personalizadas:

O Azure AD B2C executa o código no navegador do seu cliente utilizando uma abordagem chamada [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/).

No prazo de execução, o conteúdo é carregado a partir de um URL que especifica no fluxo do utilizador ou na política personalizada. Cada página na experiência do utilizador carrega o seu conteúdo a partir do URL que especifica para essa página. Depois de o conteúdo ser carregado a partir do seu URL, é fundido com um fragmento HTML inserido pelo Azure AD B2C, e depois a página é exibida ao seu cliente.

Reveja as seguintes orientações antes de utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI:

- O Azure AD B2C **funde** o conteúdo HTML nas suas páginas. Não copie e tente alterar o conteúdo predefinido que o Azure AD B2C fornece. É melhor construir o seu conteúdo HTML de raiz e usar o conteúdo predefinido como referência.
- **O JavaScript** pode ser incluído no seu conteúdo personalizado tanto para [fluxos](user-flow-javascript-overview.md) de utilizador como [para políticas personalizadas.](javascript-samples.md)
- As **versões** de navegador suportadas são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Suporte limitado para Internet Explorer 9 e 8
  - Google Chrome 42.0 e acima
  - Mozilla Firefox 38.0 e acima
- Não inclua **etiquetas** de formulário no seu HTML. As etiquetas de formulário interferem com as operações POST geradas pelo HTML injetadas pelo Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Onde guardo conteúdo ui?

Ao utilizar os seus próprios ficheiros HTML e CSS para personalizar o UI, pode hospedar o seu conteúdo uI em qualquer ponto final HTTPS disponível publicamente que suporte o CORS. Por exemplo, [armazenamento De Blob Azure,](../storage/blobs/storage-blobs-introduction.md)servidores web, CDNs, AWS S3 ou sistemas de partilha de ficheiros.

O importante é que você hospeda o conteúdo num ponto final HTTPS disponível publicamente com CORS habilitado. Deve utilizar um URL absoluto quando o especificar no seu conteúdo.

## <a name="get-started-with-custom-html-and-css"></a>Começar com HTML e CSS personalizados

Começa a usar o seu próprio HTML e CSS nas páginas da experiência do utilizador seguindo estas orientações.

- Crie conteúdo HTML bem formado com um elemento `<div id="api"></div>` vazio localizado algures no `<body>`. Este elemento marca onde o conteúdo Azure AD B2C é inserido. O exemplo seguinte mostra uma página mínima:

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

- Utilize CSS para modelar os elementos UI que o Azure AD B2C insere na sua página. O exemplo seguinte mostra um simples ficheiro CSS que também inclui definições para os elementos HTML injetados de inscrição:

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

- Hospedar o seu conteúdo num ponto final HTTPS (com o CORS permitido). Tanto os métodos de pedido GET como OPTIONS devem ser ativados ao configurar o CORS.
- Crie ou edite um fluxo de utilizador ou uma política personalizada para utilizar o conteúdo que criou.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmentos HTML de Azure AD B2C

A tabela seguinte lista os fragmentos HTML que o Azure AD B2C se funde no elemento `<div id="api"></div>` localizado no seu conteúdo.

| Página inserida | Descrição do HTML |
| ------------- | ------------------- |
| Seleção de fornecedores de identidade | Contém uma lista de botões para fornecedores de identidade que o cliente pode escolher durante o sessão ou o inscreveu-se. Estes botões incluem fornecedores de identidade social como Facebook, Google ou contas locais (com base no endereço de e-mail ou nome do utilizador). |
| Inscrição na conta local | Contém um formulário para inscrição de conta local com base num endereço de e-mail ou num nome de utilizador. O formulário pode conter diferentes controlos de entrada, tais como caixa de entrada de texto, caixa de entrada de palavra-passe, botão de rádio, caixas de entrega seletivas únicas e caixas de verificação multi-selecionadas. |
| Inscrição na conta social | Pode aparecer ao inscrever-se usando uma conta existente de um fornecedor de identidade social, como o Facebook ou o Google. É usado quando devem ser recolhidas informações adicionais do cliente através de um formulário de inscrição. |
| Inscrição unificada ou inscrição | Lida com o sign-up e o sign-in de clientes que podem usar fornecedores de identidade social, como facebook, Google ou contas locais. |
| Autenticação multifator | Os clientes podem verificar os seus números de telefone (utilizando texto ou voz) durante o registo ou o check-in. |
| Erro | Fornece informações de erro ao cliente. |

## <a name="localize-content"></a>Conteúdo localize

Localiza o seu conteúdo HTML permitindo a [personalização](user-flow-language-customization.md) da linguagem no seu inquilino Azure AD B2C. Ativar esta funcionalidade permite que o Azure AD B2C encaminhou o parâmetro OpenID Connect `ui-locales` para o seu ponto final. O seu servidor de conteúdo pode utilizar este parâmetro para fornecer páginas HTML específicas do idioma.

O conteúdo pode ser retirado de diferentes lugares com base no local que é usado. No seu ponto final ativado pelo CORS, cria uma estrutura de pasta para hospedar conteúdo para idiomas específicos. Chamará o certo se usar o valor wildcard `{Culture:RFC5646}`.

Por exemplo, a sua página personalizada URI pode parecer:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Pode carregar a página em francês puxando conteúdo de:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exemplos

Pode encontrar vários ficheiros de modelo de amostra no repositório [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) no GitHub.

Os ficheiros HTML e CSS da amostra nos modelos estão localizados no diretório [/sample_templates.](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)

## <a name="next-steps"></a>Passos seguintes

- Se estiver a utilizar fluxos de **utilizadores,** pode começar a personalizar o seu UI com o tutorial:

    [Personalize a interface de utilizador das suas aplicações no Diretório Ativo Azure B2C](tutorial-customize-ui.md).
- Se estiver a usar **políticas personalizadas,** pode começar a personalizar a UI com o artigo:

    [Personalize a interface de utilizador da sua aplicação utilizando uma política personalizada no Diretório Ativo Azure B2C](custom-policy-ui-customization.md).
