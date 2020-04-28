---
title: Personalize o portal de desenvolvimento de gestão da API usando modelos
titleSuffix: Azure API Management
description: Aprenda a personalizar o portal de desenvolvimento de gestão da API Azure utilizando modelos.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430778"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal de desenvolvimento de gestão da API Azure usando modelos

Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout]
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modificar os modelos utilizados para páginas geradas pelo portal][portal-templates] (explicado neste guia)

Os modelos são usados para personalizar o conteúdo das páginas do portal de desenvolvimento geradopelo sistema (por exemplo, docs, produtos, autenticação do utilizador, etc.). Utilizando a sintaxe [DotLiquid,](http://dotliquidmarkup.org/) e um conjunto fornecido de recursos de cordas localizados, ícones e controlos de página, você tem uma grande flexibilidade para configurar o conteúdo das páginas como você entender.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Visão geral dos modelos do portal do desenvolvedor

Os modelos de edição são feitos a partir do **portal Developer** enquanto são iniciados como administrador. Para chegar lá abra primeiro o portal Azure e clique no **portal Developer** a partir da barra de ferramentas de serviço da sua instância de Gestão API.

Para aceder aos modelos do portal do desenvolvedor, clique no ícone personalizado à esquerda para exibir o menu de personalização e clique em **Modelos**.

![Modelos de portal de desenvolvimento][api-management-customize-menu]

A lista de modelos apresenta várias categorias de modelos que cobrem as diferentes páginas do portal de desenvolvimento. Cada modelo é diferente, mas os passos para editá-los e publicar as alterações são os mesmos. Para editar um modelo, clique no nome do modelo.

![Modelos de portal de desenvolvimento][api-management-templates-menu]

Clicar num modelo leva-o à página do portal do desenvolvedor que é personalizável por esse modelo. Neste exemplo, o modelo da lista de **produtos** é apresentado. O modelo **da lista** de produtos controla a área do ecrã indicada pelo retângulo vermelho.

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como os modelos de Perfil de **Utilizador,** personalizam diferentes partes da mesma página.

![Modelos de perfil do utilizador][api-management-user-profile-templates]

O editor de cada modelo de portal de desenvolvimento tem duas secções exibidas na parte inferior da página. O lado esquerdo exibe o painel de edição para o modelo, e o lado direito exibe o modelo de dados para o modelo.

O painel de edição do modelo contém a marcação que controla a aparência e o comportamento da página correspondente no portal do desenvolvedor. A marcação no modelo utiliza a sintaxe [DotLiquid.](http://dotliquidmarkup.org/) Um editor popular para do DotLiquid é [dotLiquid para Designers.](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) Quaisquer alterações feitas no modelo durante a edição são apresentadas em tempo real no navegador, mas não são visíveis para os seus clientes até [que guarde](#to-save-a-template) e [publique](#to-publish-a-template) o modelo.

![Marcação de modelo][api-management-template]

O painel de **dados do Modelo** fornece um guia para o modelo de dados para as entidades que estão disponíveis para uso num determinado modelo. Fornece este guia exibindo os dados ao vivo que estão atualmente apresentados no portal do desenvolvedor. Pode expandir as vidraças do modelo clicando no retângulo no canto superior direito do painel de dados do **modelo.**

![Modelo de dados de modelo][api-management-template-data]

No exemplo anterior, existem dois produtos apresentados no portal de desenvolvimento que foram recuperados a partir dos dados apresentados no painel de **dados do Modelo,** como mostra o seguinte exemplo:

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

A marcação no modelo da lista de **produtos** processa os dados para fornecer a saída desejada através da recolha de produtos para exibir informações e uma ligação a cada produto individual. Repare nos `<search-control>` `<page-control>` elementos e elementos da marcação. Estes controlam a visualização dos controlos de pesquisa e de paging na página. `ProductsStrings|PageTitleProducts`é uma referência de cadeia `h2` localizada que contém o texto cabeçalho para a página. Para obter uma lista de recursos de cadeias, controlos de página e ícones disponíveis para uso em modelos de portal de desenvolvimento, consulte a referência dos modelos de modelos de portal de desenvolvimento da [API Management](api-management-developer-portal-templates-reference.md).

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

## <a name="to-save-a-template"></a>Para salvar um modelo
Para guardar um modelo, clique em guardar no editor do modelo.

![Guardar o modelo][api-management-save-template]

As alterações guardadas não são transmitidas ao vivo no portal de desenvolvimento até serem publicadas.

## <a name="to-publish-a-template"></a>Para publicar um modelo
Os modelos guardados podem ser publicados individualmente, ou todos juntos. Para publicar um modelo individual, clique em publicar no editor do modelo.

![Publicar modelo][api-management-publish-template]

Clique **em Sim** para confirmar e fazer o modelo ao vivo no portal do desenvolvedor.

![Confirmar publicação][api-management-publish-template-confirm]

Para publicar todas as versões de modelo sem publicação atualmente, clique em **Publicar** na lista de modelos. Os modelos não publicados são designados por um asterisco seguindo o nome do modelo. Neste exemplo, a lista de **produtos** e os modelos de **produto** estão a ser publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **Publicar personalizações** para confirmar.

![Confirmar publicação][api-management-publish-customizations]

Os modelos recém-publicados são eficazes imediatamente no portal do desenvolvedor.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior
Para reverter um modelo para a versão publicada anterior, clique em reverter no editor do modelo.

![Reverter modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão anteriormente publicada de um modelo é ao vivo no portal do desenvolvedor uma vez que a operação de reversão esteja completa.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão padrão
Restaurar os modelos para a sua versão padrão é um processo de duas etapas. Primeiro, os modelos devem ser restaurados e, em seguida, as versões restauradas devem ser publicadas.

Para restaurar um único modelo para a versão padrão clique restaurar no editor do modelo.

![Reverter modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos nas suas versões predefinidas, clique em **restaurar os modelos predefinidos** na lista de modelos.

![Restaurar os modelos][api-management-restore-templates]

Os modelos restaurados devem então ser publicados individualmente ou todos de uma só vez, seguindo os passos em [publicar um modelo](#to-publish-a-template).

## <a name="next-steps"></a>Passos seguintes
Para obter informações de referência para modelos de portal de desenvolvimento, recursos de cadeia, ícones e controlos de página, consulte a referência dos modelos de modelos de modelos de portal de [desenvolvimento da API Management](api-management-developer-portal-templates-reference.md).

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
