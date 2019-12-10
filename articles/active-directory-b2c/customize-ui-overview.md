---
title: Personalizar a interface de utilizador
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do usuário para seus aplicativos que usam Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d737d010f323a5d5b230091ad07ba530d25d6e51
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949411"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Personalizar a interface do usuário no Azure Active Directory B2C

A identidade visual e a personalização da interface do usuário que o Azure Active Directory B2C (Azure AD B2C) exibe para seus clientes ajuda a fornecer uma experiência de usuário direta em seu aplicativo. Essas experiências incluem a inscrição, a entrada, a edição de perfil e a redefinição de senha. Este artigo apresenta os métodos de personalização da interface do usuário para fluxos de usuário e políticas personalizadas.

## <a name="ui-customization-in-different-scenarios"></a>Personalização da interface do usuário em cenários diferentes

Há várias maneiras de personalizar a interface do usuário de seu aplicativo, cada um apropriado para cenários diferentes.

### <a name="user-flows"></a>Fluxos do utilizador

Se você usar [fluxos de usuário](active-directory-b2c-reference-policies.md), poderá alterar a aparência de suas páginas de fluxo de usuário usando modelos de *layout de página*internos ou usando seu próprio HTML e CSS. Os dois métodos são discutidos posteriormente neste artigo.

Você usa o [portal do Azure](tutorial-customize-ui.md) para configurar a personalização da interface do usuário para fluxos de usuários.

### <a name="custom-policies"></a>Políticas personalizadas

Se você estiver usando [políticas personalizadas](active-directory-b2c-overview-custom.md) para fornecer inscrição ou entrada, redefinição de senha ou edição de perfil em seu aplicativo, use [arquivos de política para personalizar a interface do usuário](active-directory-b2c-ui-customization-custom.md).

Se você precisar fornecer conteúdo dinâmico com base na decisão de um cliente, use políticas personalizadas que possam [alterar o conteúdo da página dinamicamente](active-directory-b2c-ui-customization-custom-dynamic.md) , dependendo de um parâmetro enviado em uma cadeia de caracteres de consulta. Por exemplo, você pode alterar a imagem de plano de fundo na página Azure AD B2C inscrição ou entrada com base em um parâmetro que você passa de seu aplicativo Web ou móvel.

### <a name="javascript"></a>JavaScript

Você pode habilitar o código JavaScript do lado do cliente tanto em [fluxos de usuário](user-flow-javascript-overview.md) quanto em [políticas personalizadas](page-layout.md).

### <a name="sign-in-only-ui-customization"></a>Personalização da interface do usuário somente de entrada

Se você estiver fornecendo somente entrada, junto com sua página de redefinição de senha e emails de verificação que o acompanham, use as mesmas etapas de personalização usadas para uma [página de entrada do Azure ad](../active-directory/fundamentals/customize-branding.md).

Se os clientes tentarem editar seu perfil antes de entrar, eles serão redirecionados para uma página que você personalizar usando as mesmas etapas que são usadas para personalizar a página de entrada do Azure AD.

## <a name="page-layout-templates"></a>Modelos de layout de página

Os fluxos de usuário fornecem vários modelos internos que você pode escolher para dar às suas páginas de experiência do usuário uma aparência profissional. Esses modelos de layout também podem e servem como ponto de partida para sua própria personalização.

Em **Personalizar** no menu à esquerda, selecione **layouts de página** e, em seguida, selecione **modelo**.

![Lista suspensa seleção de modelos na página fluxo do usuário do portal do Azure](media/customize-ui-overview/template-selection.png)

Em seguida, selecione um modelo na lista. Aqui estão exemplos das páginas de entrada para cada modelo:

| Azul-marinho | Cinza-acinzentado | Clássico |
|:-:|:-:|:-:|
|![Exemplo do modelo azul do oceano renderizado na página de entrada de inscrição](media/customize-ui-overview/template-ocean-blue.png)|![Exemplo do modelo cinza-acinzentado renderizado na página de entrada de inscrição](media/customize-ui-overview/template-slate-gray.png)|![Exemplo do modelo clássico renderizado na página de entrada de inscrição](media/customize-ui-overview/template-classic.png)|

Quando você escolhe um modelo, o layout selecionado é aplicado a todas as páginas em seu fluxo de usuário e o URI para cada página é visível no campo **URI de página personalizada** .

## <a name="custom-html-and-css"></a>HTML e CSS personalizados

Azure AD B2C executa o código no navegador do cliente usando uma abordagem chamada [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).

No tempo de execução, o conteúdo é carregado de uma URL que você especifica em seu fluxo de usuário ou política personalizada. Cada página na experiência do usuário carrega seu conteúdo da URL que você especificar para essa página. Depois que o conteúdo é carregado de sua URL, ele é mesclado com um fragmento de HTML inserido por Azure AD B2C e, em seguida, a página é exibida para o cliente.

Examine as diretrizes a seguir antes de usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário:

- Azure AD B2C **mescla** o conteúdo HTML em suas páginas. Não copie e tente alterar o conteúdo padrão que o Azure AD B2C fornece. É melhor criar seu conteúdo HTML do zero e usar o conteúdo padrão como referência.
- O **JavaScript** pode ser incluído em seu conteúdo personalizado para [fluxos de usuário](user-flow-javascript-overview.md) e [políticas personalizadas](javascript-samples.md).
- **As versões de navegador** com suporte são:
  - Internet Explorer 11, 10 e Microsoft Edge
  - Suporte limitado para o Internet Explorer 9 e 8
  - Google Chrome 42,0 e superior
  - Mozilla Firefox 38,0 e superior
- Não inclua **marcas de formulário** em seu HTML. As marcas de formulário interferem com as operações de POSTAgem geradas pelo HTML injetadas por Azure AD B2C.

### <a name="where-do-i-store-ui-content"></a>Onde armazenar o conteúdo da interface do usuário?

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, você pode hospedar o conteúdo da interface do usuário em qualquer ponto de extremidade HTTPS publicamente disponível que ofereça suporte a CORS. Por exemplo, [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md), servidores Web, CDNS, AWS S3 ou sistemas de compartilhamento de arquivos.

O ponto importante é que você hospeda o conteúdo em um ponto de extremidade HTTPS disponível publicamente com CORS habilitado. Você deve usar uma URL absoluta ao especificá-la em seu conteúdo.

## <a name="get-started-with-custom-html-and-css"></a>Introdução ao HTML e CSS personalizados

Comece a usar seu próprio HTML e CSS em suas páginas de experiência do usuário seguindo estas diretrizes.

- Crie conteúdo HTML bem formado com um elemento de `<div id="api"></div>` vazio localizado em algum lugar na `<body>`. Esse elemento marca onde o conteúdo do Azure AD B2C é inserido. O exemplo a seguir mostra uma página mínima:

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

- Use o CSS para estilizar os elementos da interface do usuário que Azure AD B2C inserções em sua página. O exemplo a seguir mostra um arquivo CSS simples que também inclui configurações para os elementos HTML injetados de inscrição:

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

- Hospede seu conteúdo em um ponto de extremidade HTTPS (com CORS permitido). Ambos os métodos de solicitação GET e OPTIONS devem ser habilitados durante a configuração do CORS.
- Crie ou edite um fluxo de usuário ou uma política personalizada para usar o conteúdo que você criou.

### <a name="html-fragments-from-azure-ad-b2c"></a>Fragmentos de HTML do Azure AD B2C

A tabela a seguir lista os fragmentos HTML que Azure AD B2C mescla no elemento `<div id="api"></div>` localizado em seu conteúdo.

| Página inserida | Descrição do HTML |
| ------------- | ------------------- |
| Seleção do provedor de identidade | Contém uma lista de botões para provedores de identidade que o cliente pode escolher durante a inscrição ou entrada. Esses botões incluem provedores de identidade social, como o Facebook, o Google ou contas locais (com base no endereço de email ou no nome de usuário). |
| Inscrição na conta local | Contém um formulário para a inscrição de conta local com base em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada, como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| Inscrição em conta social | Pode aparecer ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou o Google. Ele é usado quando informações adicionais devem ser coletadas do cliente usando um formulário de inscrição. |
| Inscrição ou entrada unificada | O lida com a inscrição e a entrada de clientes que podem usar provedores de identidade social, como Facebook, Google ou contas locais. |
| Autenticação multifator | Os clientes podem verificar seus números de telefone (usando texto ou voz) durante a inscrição ou entrada. |
| Erro | Fornece informações de erro para o cliente. |

## <a name="localize-content"></a>Conteúdo de localização

Você localiza o conteúdo HTML habilitando a [personalização de idioma](active-directory-b2c-reference-language-customization.md) em seu locatário Azure ad B2C. Habilitar esse recurso permite que Azure AD B2C encaminhe o parâmetro OpenID Connect `ui-locales` para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML específicas a um idioma.

O conteúdo pode ser extraído de locais diferentes com base na localidade usada. No ponto de extremidade habilitado para CORS, você configura uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará o correto se usar o valor de curinga `{Culture:RFC5646}`.

Por exemplo, o URI da página personalizada pode ser semelhante a:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

Você pode carregar a página em francês Obtendo conteúdo de:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>Exemplos

Você pode encontrar vários arquivos de modelo de exemplo no repositório [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) no github.

Os arquivos HTML e CSS de exemplo nos modelos estão localizados no diretório [/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) .

## <a name="next-steps"></a>Passos seguintes

- Se você estiver usando **fluxos de usuário**, você pode começar a personalizar sua interface de usuário com o tutorial:

    [Personalize a interface do usuário de seus aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md).
- Se você estiver usando **políticas personalizadas**, poderá começar a personalizar a interface do usuário com o artigo:

    [Personalize a interface do usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
