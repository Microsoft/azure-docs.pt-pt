---
title: Aceda e personalize o portal de desenvolvimento gerido - Azure API Management ! Microsoft Docs
description: Saiba como utilizar a versão gerida do portal de desenvolvimento na Gestão da API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: 3ceb8fd154e8ad533f4bf6bc9eb0ec3900749f8b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075370"
---
# <a name="access-and-customize-developer-portal"></a>Aceder e personalizar portal de desenvolvedores

O portal do desenvolvedor é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores da API podem descobrir as suas APIs, aprender a usá-las e solicitar acesso.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Aceda à versão gerida do portal do programador
> * Navegar pela sua interface administrativa
> * Personalizar o conteúdo
> * Publicar as alterações
> * Ver o portal publicado

Pode encontrar mais detalhes sobre o portal do desenvolvedor na visão geral do portal de desenvolvimento da [Azure API Management.](api-management-howto-developer-portal.md)

![Portal de desenvolvimento de gestão da API - modo de administração](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Pré-requisitos

- Complete o seguinte quickstart: [Criar uma instância de gestão API Azure](get-started-create-service-instance.md)
- Importar e publicar um caso de Gestão API da Azure. Para mais informações, consulte [Importar e publicar](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Aceda ao portal como administrador

Siga os passos abaixo para aceder à versão gerida do portal.

1. Aceda ao seu serviço de Gestão API no portal Azure.
1. Clique no botão **portal Developer** na barra de navegação superior. Será aberto um novo separador de navegador com uma versão administrativa do portal.

## <a name="understand-the-portals-administrative-interface"></a>Compreender a interface administrativa do portal

### <a name="default-content"></a>Conteúdo predefinido 

Se estiver a aceder ao portal pela primeira vez, o conteúdo predefinido será automaticamente a provisionado em segundo plano. O conteúdo predefinido foi concebido para mostrar as capacidades do portal e minimizar a quantidade de personalizações necessárias para personalizar o seu portal. Pode saber mais sobre o que está incluído no conteúdo do portal na visão geral do portal de desenvolvimento da [API Management.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Editor visual

Pode personalizar o conteúdo do portal com o editor visual. As secções de menu à esquerda permitem criar ou modificar páginas, meios de comunicação, layouts, menus, estilos ou configurações do site. Os itens de menu na parte inferior permitem alternar entre viewports (por exemplo, móveis ou desktop), ver os elementos do portal visíveis para utilizadores autenticados ou anónimos, ou guardar ou desfazer ações.

Pode adicionar linhas a uma página clicando num ícone azul com um sinal de mais. Os widgets (por exemplo, texto, imagens ou lista de APIs) podem ser adicionados premindo um ícone cinzento com um sinal de mais. Pode reorganizar os itens numa página com a interação arrastar e largar. 

### <a name="layouts-and-pages"></a>Layouts e páginas

![Páginas e layouts](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Os layouts definem como as páginas são exibidas. Por exemplo, no conteúdo padrão, existem dois layouts - um aplica-se à página inicial, e o outro a todas as páginas restantes.

Um layout é aplicado a uma página combinando o seu modelo de URL com o URL da página. Por exemplo, o layout com um modelo de URL de `/wiki/*` será aplicado em todas as páginas com o segmento no `/wiki/` URL: , `/wiki/getting-started` `/wiki/styles` etc.

Na imagem acima, o conteúdo pertencente ao layout é marcado em azul, enquanto a página é marcada a vermelho. As secções do menu são marcadas respectivamente.

### <a name="styling-guide"></a>Guia de estilo

![Guia de estilo](media/api-management-howto-developer-portal-customize/styling-guide.png)

O styling guide é um painel criado com designers em mente. Permite supervisionar e modelar todos os elementos visuais do seu portal. O estilo é hierárquico - muitos elementos herdam propriedades de outros elementos. Por exemplo, os elementos dos botões usam cores para texto e fundo. Para alterar a cor de um botão, é necessário alterar a variante de cor original.

Para editar uma variante, clique nela e selecione o ícone de lápis que aparece em cima dela. Assim que fizer as alterações na janela pop-up, feche-a.

### <a name="save-button"></a>Botão Guardar

![Botão Guardar](media/api-management-howto-developer-portal-customize/save-button.png)

Sempre que fizer uma alteração no portal, tem de guardá-lo manualmente premindo o botão **Guardar** no menu na parte inferior. Quando guarda as alterações, o conteúdo modificado é automaticamente enviado para o seu serviço de Gestão API.

## <a name="customize-the-portals-content"></a>Personalize o conteúdo do portal

Antes de disponibilizar o seu portal aos visitantes, deverá personalizar o conteúdo gerado automaticamente. As alterações recomendadas incluem os layouts, estilos e o conteúdo da página inicial.

> [!NOTE]
> Devido a considerações de integração, as seguintes páginas não podem ser removidas ou movidas sob um URL diferente: , , , , , , , , , , `/404` `/500` , , , , `/captcha` , , `/change-password` , , , , `/config.json` , , , , `/confirm/invitation` , , , , , , , , , `/confirm-v2/identities/basic/signup` , , , , , , , , `/confirm-v2/password` , , , , , , , , , `/internal-status-0123456789abcdef` , , , , , , , , `/publish` , , , , , , , , , , , , `/signin` , , , , , , , , `/signin-sso` `/signup` ,

### <a name="home-page"></a>Página de boas-vindas

A página **Inicial** predefinida está cheia de conteúdo falso. Pode remover as secções inteiras com o conteúdo ou manter a estrutura e ajustar os elementos um a um. Substitua o texto e as imagens gerados pelo seu próprio e certifique-se de que os links apontam para as localizações desejadas.

### <a name="layouts"></a>Layouts

Substitua o logótipo gerado automaticamente na barra de navegação pela sua própria imagem.

### <a name="styling"></a>Styling

Embora não precise de ajustar quaisquer estilos, pode considerar ajustar elementos específicos. Por exemplo, mude a cor primária para combinar com a cor da sua marca.

### <a name="customization-example"></a>Exemplo de personalização

No vídeo abaixo demonstramos como editar o conteúdo do portal, personalizar o visual do site e publicar as alterações.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publicar o portal

Para disponibilizar o seu portal e as suas últimas alterações aos visitantes, tem de o publicar.

1. Certifique-se de que guardou as suas alterações clicando no ícone **Guardar.**
1. Clique no **site publicar** na secção **Operações** do menu. Esta operação poderá demorar alguns minutos.  
    ![Portal de publicação](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> O portal precisa de ser republicado após alterações na configuração do serviço de gestão da API, tais como a atribuição de um domínio personalizado, a atualização dos fornecedores de identidade, a definição de delegação, a especificação de termos de inscrição e de produto, e muito mais.

## <a name="visit-the-published-portal"></a>Visite o portal publicado

Depois de publicar o portal, pode aceder-lhe no mesmo URL que o painel administrativo, por `https://contoso-api.developer.azure-api.net` exemplo. Veja-o numa sessão de navegador separada (modo de navegação incógnito /privado) como um visitante externo.

## <a name="apply-the-cors-policy-on-apis"></a>Aplicar a política do CORS em APIs

É necessário ativar o CORS (partilha de recursos de origem cruzada) nas suas APIs para permitir que os visitantes do seu portal testem as APIs através da consola interativa incorporada. Consulte [este artigo de documentação](api-management-howto-developer-portal.md#cors) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes
- [Otimize e poupe nos gastos na nuvem](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Saiba mais sobre o portal de desenvolvedores:

- [Visão geral do portal de desenvolvimento da AZure API Management](api-management-howto-developer-portal.md)