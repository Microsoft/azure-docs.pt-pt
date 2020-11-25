---
title: Tutorial - Acesso e personalização do portal de desenvolvimento - Azure API Management / Microsoft Docs
description: Siga este para tutorial para aprender a personalizar o portal de desenvolvimento da API Management, um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012987"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Tutorial: Aceda e personalize o portal do desenvolvedor

O *portal do desenvolvedor* é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores da API podem descobrir as suas APIs, aprender a usá-las e solicitar acesso.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Aceda à versão gerida do portal do programador
> * Navegar pela sua interface administrativa
> * Personalizar o conteúdo
> * Publicar as alterações
> * Ver o portal publicado

Pode encontrar mais detalhes sobre o portal do desenvolvedor na visão geral do portal de desenvolvimento da [Azure API Management.](api-management-howto-developer-portal.md)

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Portal de desenvolvimento de gestão API - modo de administrador" border="false":::

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: [Criar uma instância de gestão API Azure](get-started-create-service-instance.md)
- Importar e publicar um caso de Gestão API da Azure. Para mais informações, consulte [Importar e publicar](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Aceda ao portal como administrador

Siga os passos abaixo para aceder à versão gerida do portal.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Selecione o botão **portal Developer** na barra de navegação superior. Será aberto um novo separador de navegador com uma versão administrativa do portal.

## <a name="understand-the-portals-administrative-interface"></a>Compreender a interface administrativa do portal

### <a name="default-content"></a>Conteúdo predefinido 

Se estiver a aceder ao portal pela primeira vez, o conteúdo predefinido é automaticamente a provisionado em segundo plano. O conteúdo predefinido foi concebido para mostrar as capacidades do portal e minimizar as personalizações necessárias para personalizar o seu portal. Pode saber mais sobre o que está incluído no conteúdo do portal na visão geral do portal de desenvolvimento da [API Management.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Editor visual

Pode personalizar o conteúdo do portal com o editor visual. 
* As secções de menu à esquerda permitem criar ou modificar páginas, meios de comunicação, layouts, menus, estilos ou configurações do site. 
* Os itens de menu na parte inferior permitem alternar entre viewports (por exemplo, móveis ou desktop), ver os elementos do portal visíveis para utilizadores autenticados ou anónimos, ou guardar ou desfazer ações.
* Adicione linhas a uma página clicando num ícone azul com um sinal de mais. 
* Os widgets (por exemplo, texto, imagens ou lista de APIs) podem ser adicionados premindo um ícone cinzento com um sinal de mais.
* Reorganize os itens numa página com a interação arrastar e largar. 

### <a name="layouts-and-pages"></a>Layouts e páginas

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Páginas e layouts" border="false":::

Os layouts definem como as páginas são exibidas. Por exemplo, no conteúdo predefinido, existem dois layouts: um aplica-se à página inicial e o outro a todas as páginas restantes.

Um layout é aplicado a uma página combinando o seu modelo de URL com o URL da página. Por exemplo, um layout com um modelo de URL de `/wiki/*` será aplicado em todas as páginas com o segmento no `/wiki/` URL: , `/wiki/getting-started` `/wiki/styles` etc.

Na imagem anterior, o conteúdo pertencente ao layout é marcado em azul, enquanto a página é marcada a vermelho. As secções do menu são marcadas respectivamente.

### <a name="styling-guide"></a>Guia de estilo

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Guia de estilo" border="false":::

O styling guide é um painel criado com designers em mente. Permite supervisionar e modelar todos os elementos visuais do seu portal. O estilo é hierárquico - muitos elementos herdam propriedades de outros elementos. Por exemplo, os elementos dos botões usam cores para texto e fundo. Para alterar a cor de um botão, é necessário alterar a variante de cor original.

Para editar uma variante, selecione-a e selecione o ícone de lápis que aparece em cima dela. Depois de fazer as alterações na janela pop-up, feche-a.

### <a name="save-button"></a>Botão Guardar

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Botão Guardar" border="false":::

Sempre que fizer uma alteração no portal, tem de guardá-lo manualmente selecionando o botão **Guardar** no menu na parte inferior, ou premir [Ctrl]+[S]. Quando guarda as alterações, o conteúdo modificado é automaticamente enviado para o seu serviço de Gestão API.

## <a name="customize-the-portals-content"></a>Personalize o conteúdo do portal

Antes de disponibilizar o seu portal aos visitantes, deverá personalizar o conteúdo gerado automaticamente. As alterações recomendadas incluem os layouts, estilos e o conteúdo da página inicial.

> [!NOTE]
> Devido a considerações de integração, as seguintes páginas não podem ser removidas ou movidas sob um URL diferente: , , , , , , , , , , `/404` `/500` , , , , `/captcha` , , `/change-password` , , , , `/config.json` , , , , `/confirm/invitation` , , , , , , , , , `/confirm-v2/identities/basic/signup` , , , , , , , , `/confirm-v2/password` , , , , , , , , , `/internal-status-0123456789abcdef` , , , , , , , , `/publish` , , , , , , , , , , , , `/signin` , , , , , , , , `/signin-sso` `/signup` ,

### <a name="home-page"></a>Página de boas-vindas

A página **Inicial** predefinida está cheia de conteúdo reservado. Pode remover secções inteiras que contenham este conteúdo ou manter a estrutura e ajustar os elementos um a um. Substitua o texto e as imagens gerados pelo seu próprio e certifique-se de que os links apontam para as localizações desejadas.

### <a name="layouts"></a>Layouts

Substitua o logótipo gerado automaticamente na barra de navegação pela sua própria imagem.

### <a name="styling"></a>Styling

Embora não precise de ajustar quaisquer estilos, pode considerar ajustar elementos específicos. Por exemplo, mude a cor primária para combinar com a cor da sua marca.

### <a name="customization-example"></a>Exemplo de personalização

No vídeo seguinte, demonstramos como editar o conteúdo do portal, personalizar o visual do site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publicar o portal

Para disponibilizar o seu portal e as suas últimas alterações aos visitantes, tem de *o publicar.* Pode publicar o portal dentro da interface administrativa do portal ou a partir do portal Azure.

### <a name="publish-from-the-administrative-interface"></a>Publicar a partir da interface administrativa

1. Certifique-se de que guardou as suas alterações selecionando o ícone **Guardar.**
1. Na secção **operações** do menu, **selecione Publicar o site.** Esta operação poderá demorar alguns minutos.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Portal de publicação" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publicar a partir do portal Azure

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. No menu esquerdo, no **portal Developer,** selecione **Portal overview**.
1. Na janela **de visão geral** do Portal, selecione **Publicar**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publicar portal a partir do portal Azure":::

> [!NOTE]
> O portal precisa de ser republica após alterações na configuração do serviço de gestão da API. Por exemplo, reeditar o portal após a atribuição de um domínio personalizado, atualizando os fornecedores de identidade, definindo a delegação ou especificando os termos de inscrição e produto.


## <a name="visit-the-published-portal"></a>Visite o portal publicado

Depois de publicar o portal, pode aceder-lhe no mesmo URL que o painel administrativo, por `https://contoso-api.developer.azure-api.net` exemplo. Veja-o numa sessão de navegador separada (utilizando o modo de navegação incógnito ou privado) como visitante externo.

## <a name="apply-the-cors-policy-on-apis"></a>Aplicar a política do CORS em APIs

Para que os visitantes do seu portal testem as APIs através da consola interativa incorporada, ative o CORS (partilha de recursos de origem cruzada) nas suas APIs. Para mais informações, consulte a visão geral do [portal de desenvolvimento da Azure API Management](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvedores:

- [Visão geral do portal de desenvolvimento da AZure API Management](api-management-howto-developer-portal.md)
- [Migrar para o novo portal](developer-portal-deprecated-migration.md) de desenvolvedores a partir do portal do legado deprecado.