---
title: Personalizar o portal de programador da gestão de API através de modelos-Azure | Documentos da Microsoft
description: Saiba como personalizar o portal do Programador de API Management do Azure através de modelos.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 00d5e3df78e85d19a519786dad1a1b176ad7fa08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837236"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal do Programador de API Management do Azure através de modelos

Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout]
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal] [ portal-templates] (explicado neste guia)

Os modelos são utilizados para personalizar o conteúdo das páginas de portal de programador gerados pelo sistema (por exemplo, documentos de API, produtos, autenticação de utilizador, etc.). Usando [DotLiquid](http://dotliquidmarkup.org/) sintaxe e um conjunto fornecido de recursos de seqüência localizada de caracteres, ícones e controles da página, tem uma grande flexibilidade para configurar o conteúdo das páginas como quiser.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Descrição geral de modelos de portais de programador

Editando modelos é feita a partir da **portal do programador** ao que está a ser iniciado a sessão como administrador. Para aceder a abrir o Portal do Azure e clique em **portal do programador** da barra de ferramentas de serviço da sua instância de gestão de API.

Para acessar os modelos de portal do programador, clique no ícone de personalizar à esquerda para exibir o menu de personalização e clique em **modelos**.

![Modelos de portais de programador][api-management-customize-menu]

A lista de modelos apresenta várias categorias de modelos que abordam as páginas diferentes no portal do programador. Cada modelo é diferente, mas os passos para editá-los e publicar as alterações são os mesmos. Para editar um modelo, clique no nome do modelo.

![Modelos de portais de programador][api-management-templates-menu]

Clicar num modelo, leva-o para a página de portal de programador é personalizável por esse modelo. Neste exemplo, o **lista de produtos** modelo é exibido. O **lista de produtos** modelo controla a área do ecrã indicado pelo retângulo vermelho.

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como o **perfil de utilizador** modelos, personalizar a partes diferentes da mesma página.

![Modelos de perfil de utilizador][api-management-user-profile-templates]

O editor para cada modelo do portal de programador tem duas secções apresentadas na parte inferior da página. Lado esquerdo exibe o painel de edição para o modelo e, no lado direito apresenta o modelo de dados para o modelo.

O modelo de painel de edição contém a marcação que controla o aspeto e comportamento da página correspondente no portal do programador. A marcação no modelo utiliza a [DotLiquid](http://dotliquidmarkup.org/) sintaxe. É um editor popular para DotLiquid [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Todas as alterações feitas ao modelo durante a edição são apresentadas em tempo real no navegador, mas não estão visíveis para os seus clientes até [salvar](#to-save-a-template) e [publicar](#to-publish-a-template) o modelo.

![Marcação de modelo][api-management-template]

O **dados de modelo** painel fornece um guia para o modelo de dados para as entidades que estão disponíveis para uso num modelo específico. Este guia fornece ao apresentar os dados em direto que atualmente estão a ser apresentados no portal do programador. Pode expandir os painéis de modelo ao clicar o retângulo no canto superior direito do **dados de modelo** painel.

![Modelo de dados do modelo][api-management-template-data]

No exemplo anterior, existem dois produtos apresentados no portal do programador que foram obtidos a partir dos dados apresentados no **dados de modelo** painel, conforme mostrado no exemplo a seguir:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

A marcação na **lista de produtos** modelo processa os dados para fornecer a saída desejada com a iteração por meio da coleção de produtos para apresentar informações e uma ligação para cada produto individual. Tenha em atenção a `<search-control>` e `<page-control>` elementos na marcação. Estes controlam a apresentação da pesquisa e controles da página de paginação. `ProductsStrings|PageTitleProducts` é uma referência de seqüência localizada de caracteres que contém o `h2` texto do cabeçalho da página. Para obter uma lista de recursos de cadeia de caracteres, controles de página e ícones disponíveis para utilização em modelos de portais de programador, consulte [referência de modelos de portais de Programador de gestão de API](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Para guardar um modelo
Para guardar um modelo, clique em salvar no editor de modelo.

![Guardar o modelo][api-management-save-template]

Alterações guardadas não estão em direto no portal do programador, até serem publicados.

## <a name="to-publish-a-template"></a>Para publicar um modelo
Modelos guardados podem ser publicados, tanto individualmente todos os elementos. Para publicar um modelo individual, clique em publicar no editor de modelo.

![Publicar modelo][api-management-publish-template]

Clique em **Sim** para confirmar e fazer com que o modelo em direto no portal do programador.

![Confirmar a publicar][api-management-publish-template-confirm]

Para publicar todas as versões de modelo atualmente não publicado, clique em **publicar** na lista de modelos. Os modelos não publicados são designados por um asterisco após o nome de modelo. Neste exemplo, o **lista de produtos** e **produto** modelos estão a ser publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **publicar personalizações** para confirmar.

![Confirmar a publicar][api-management-publish-customizations]

Modelos publicados recentemente entram em vigor imediatamente no portal do programador.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior
Para reverter um modelo para a versão publicada anterior, clique em reverter no editor de modelo.

![Reverter o modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão publicada anteriormente de um modelo é em direto no portal do programador, depois de concluída a operação de reversão.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão predefinida
Restaurar modelos para a sua versão predefinida é um processo de dois passos. Primeiro tem de ser restaurados os modelos e, em seguida, as versões restauradas tem de ser publicadas.

Para restaurar um único modelo para a versão predefinida, clique em restaurar no editor de modelo.

![Reverter o modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos para suas versões de predefinido, clique em **restaurar modelos predefinidos** na lista de modelos.

![Restaurar modelos][api-management-restore-templates]

Os modelos restaurados, em seguida, devem ser publicados individualmente ou em simultâneo ao seguir os passos em [para publicar um modelo](#to-publish-a-template).

## <a name="next-steps"></a>Passos Seguintes
Para informações de referência para os modelos de portais de programador, recursos de cadeia de caracteres, ícones e controlos de página, consulte [referência de modelos de portais de Programador de gestão de API](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
