---
title: Sobre a personalização da interface do usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como personalizar a interface do usuário para seus aplicativos que usam Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bbce41271914df4bb8a75804bc66ae538fe766fa
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716751"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Sobre a personalização da interface do usuário no Azure Active Directory B2C

A capacidade de você marcar e personalizar a interface do usuário (IU) que Azure Active Directory (Azure AD) B2C serve para seus aplicativos é importante para fornecer uma experiência direta ao seu cliente. Essas experiências incluem inscrição, entrada, edição de perfil e redefinição de senha. Este artigo fornece informações para ajudá-lo a personalizar a interface do usuário de seus aplicativos.

Dependendo de suas necessidades quando se trata dessas experiências, você personaliza a interface do usuário de seu aplicativo de maneiras diferentes. Por exemplo:

- Se você estiver usando [fluxos de usuário](active-directory-b2c-reference-policies.md) para fornecer experiências de inscrição ou entrada, redefinição de senha ou edição de perfil em seu aplicativo, use o [portal do Azure para personalizar a interface do usuário](tutorial-customize-ui.md).
- Se você estiver usando um fluxo de usuário v2, poderá usar um [modelo de layout de página](#page-layout-templates) para alterar a aparência de suas páginas de fluxo de usuário sem personalização adicional. Por exemplo, você pode aplicar um tema cinza azulado ou acinzentado a todas as páginas em seu fluxo de usuário.
- Se você estiver fornecendo somente entrada, sua página de redefinição de senha e emails de verificação que o acompanham, use as mesmas etapas de personalização usadas para uma [página de entrada do Azure ad](../active-directory/fundamentals/customize-branding.md).
- Se os clientes tentarem editar seu perfil antes de entrarem, eles serão redirecionados para uma página que você personalizar usando as mesmas etapas que são usadas para personalizar a página de entrada do Azure AD.
- Se você estiver usando [políticas personalizadas](active-directory-b2c-overview-custom.md) para fornecer inscrição ou entrada, redefinição de senha ou edição de perfil em seu aplicativo, use os [arquivos de política para personalizar a interface do usuário](active-directory-b2c-ui-customization-custom.md).
- Se você precisar fornecer conteúdo dinâmico com base na decisão de um cliente, use [políticas personalizadas que podem alterar o conteúdo da página](active-directory-b2c-ui-customization-custom-dynamic.md) dependendo de um parâmetro enviado em uma cadeia de caracteres de consulta. Por exemplo, a imagem de plano de fundo na página Azure AD B2C inscrição ou entrada é alterada, com base em um parâmetro que você passa de seu aplicativo Web ou móvel.
- Você pode habilitar o código do lado do cliente JavaScript em seus Azure AD B2C [fluxos de usuário](user-flow-javascript-overview.md) ou [políticas personalizadas](page-layout.md).

Azure AD B2C executa o código no navegador do cliente e usa uma abordagem moderna chamada [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/). Em tempo de execução, o conteúdo é carregado de uma URL que você especifica em um fluxo de usuário ou política. Você especifica URLs diferentes para páginas diferentes. Depois que o conteúdo é carregado de sua URL, ele é mesclado com um fragmento de HTML inserido de Azure AD B2C e, em seguida, exibido para o cliente.

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, examine as diretrizes a seguir antes de começar:

- Azure AD B2C mescla o conteúdo HTML em suas páginas. Não copie e tente alterar o conteúdo padrão que o Azure AD B2C fornece. É melhor criar seu conteúdo HTML do zero e usar o conteúdo padrão como referência.
- O JavaScript agora pode ser incluído em seu conteúdo personalizado.
- As versões de navegador com suporte são:
    - Internet Explorer 11, 10 e Microsoft Edge
    - Suporte limitado para o Internet Explorer 9 e 8
    - Google Chrome 42,0 e superior
    - Mozilla Firefox 38,0 e superior
- Certifique-se de não incluir marcas de formulário em seu HTML porque ela interfere nas operações de POSTAgem geradas pelo HTML injetado de Azure AD B2C.

## <a name="page-layout-templates"></a>Modelos de layout de página

Para fluxos de usuário v2, você pode escolher um modelo predefinido que dá às suas páginas padrão uma aparência melhor e serve como uma boa base para sua própria personalização.

No menu à esquerda, em **Personalizar**, selecione **layouts de página**. Em seguida, selecione **modelo (versão prévia)** .

![Lista suspensa seleção de modelos na página fluxo do usuário do portal do Azure](media/customize-ui-overview/template.png)

Selecione um modelo na lista. Por exemplo, o modelo **azul do oceano** aplica o layout a seguir às suas páginas de fluxo de usuário:

![Exemplo do modelo azul do oceano renderizado na página de entrada de inscrição](media/customize-ui-overview/ocean-blue.png)

Quando você escolhe um modelo, o layout selecionado é aplicado a todas as páginas em seu fluxo de usuário e o URI para cada página é visível no campo **URI de página personalizada** .

## <a name="where-do-i-store-ui-content"></a>Onde armazenar o conteúdo da interface do usuário?

Ao usar seus próprios arquivos HTML e CSS para personalizar a interface do usuário, você pode hospedar o conteúdo da interface do usuário em qualquer lugar, como no [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md), servidores Web, CDNS, AWS S3 ou sistemas de compartilhamento de arquivos. O ponto importante é que você hospeda o conteúdo em um ponto de extremidade HTTPS disponível publicamente com CORS habilitado. Você deve usar uma URL absoluta ao especificá-la em seu conteúdo.

## <a name="how-do-i-get-started"></a>Como posso começar?

Você faz o seguinte para personalizar a interface do usuário:

- Crie conteúdo HTML bem formado com um elemento vazio `<div id="api"></div>` localizado `<body>`em algum lugar no. Esse elemento marca onde o conteúdo do Azure AD B2C é inserido. O exemplo a seguir mostra uma página mínima:

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

- Hospede seu conteúdo em um ponto de extremidade HTTPS (com CORS permitido). Ambos os métodos de solicitação GET e OPTIONS devem ser habilitados durante a configuração do CORS.
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

- Crie ou edite uma política para usar o conteúdo que você criou.

A tabela a seguir lista os fragmentos HTML que Azure ad B2C mescla `<div id="api"></div>` no elemento localizado em seu conteúdo.

| Página inserida | Descrição do HTML |
| ------------- | ------------------- |
| Seleção do provedor de identidade | Contém uma lista de botões para provedores de identidade que o cliente pode escolher durante a inscrição ou entrada. Esses botões incluem provedores de identidade social, como o Facebook, o Google ou contas locais (com base no endereço de email ou no nome de usuário). |
| Inscrição na conta local | Contém um formulário para a inscrição de conta local com base em um endereço de email ou um nome de usuário. O formulário pode conter diferentes controles de entrada, como caixa de entrada de texto, caixa de entrada de senha, botão de opção, caixas suspensas de seleção única e caixas de seleção de várias seleções. |
| Inscrição em conta social | Pode aparecer ao se inscrever usando uma conta existente de um provedor de identidade social, como o Facebook ou o Google. Ele é usado quando informações adicionais devem ser coletadas do cliente usando um formulário de inscrição. |
| Inscrição ou entrada unificada | O lida com a inscrição e a entrada de clientes que podem usar provedores de identidade social, como Facebook, Google ou contas locais. |
| Autenticação multifator | Os clientes podem verificar seus números de telefone (usando texto ou voz) durante a inscrição ou entrada. |
| Erro | Fornece informações de erro para o cliente. |


## <a name="how-do-i-localize-content"></a>Como fazer localizar conteúdo?

Você localiza o conteúdo HTML habilitando a [personalização de idioma](active-directory-b2c-reference-language-customization.md) em seu locatário Azure ad B2C. Habilitar esse recurso permite que Azure ad B2C encaminhe o parâmetro `ui-locales` OpenID Connect para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML específicas a um idioma.

O conteúdo pode ser extraído de locais diferentes com base na localidade usada. No ponto de extremidade habilitado para CORS, você configura uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará o correto se usar o valor curinga {Culture: RFC5646}. Por exemplo, o URI da página personalizada pode ser `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`semelhante a. Você pode carregar a página em francês Obtendo conteúdo de`https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Exemplos

Para obter exemplos de personalização, baixe e examine esses [arquivos de modelo de exemplo](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Passos Seguintes

- Se você estiver usando fluxos de usuário, você pode começar a personalizar sua interface de usuário com o tutorial: [Personalize a interface do usuário de seus aplicativos no Azure Active Directory B2C](tutorial-customize-ui.md).
- Se você estiver usando políticas personalizadas, poderá começar a personalizar a interface do usuário com o artigo: [Personalize a interface do usuário do seu aplicativo usando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).

