---
title: Personalize o portal de desenvolvedores de gestão API usando modelos
titleSuffix: Azure API Management
description: Saiba como personalizar o portal de desenvolvimento da Azure API Management utilizando modelos.
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
ms.openlocfilehash: 16c0b3e6dc4bd9097312e3a8c43618e66781ef85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092890"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal de desenvolvimento da Azure API Management usando modelos

Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout]
* [Atualizar os estilos utilizados para elementos de página em todo o portal do programador][customize-styles]
* [Modifique os modelos utilizados para as páginas geradas pelo portal][portal-templates] (explicados neste guia)

Os modelos são utilizados para personalizar o conteúdo das páginas do portal do programador geradas pelo sistema (por exemplo, docs API, produtos, autenticação do utilizador, etc.). Utilizando a sintaxe [DotLiquid,](http://dotliquidmarkup.org/) e um conjunto fornecido de recursos de cordas localizados, ícones e controlos de página, você tem uma grande flexibilidade para configurar o conteúdo das páginas como você entender.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Visão geral dos modelos do portal do desenvolvedor

Os modelos de edição são feitos a partir do **portal Developer** enquanto estão a ser iniciados como administrador. Para chegar lá primeiro abra o portal Azure e clique no **portal Developer** a partir da barra de ferramentas de serviço da sua instância de Gestão de API.

Para aceder aos modelos do portal do desenvolvedor, clique no ícone de personalização à esquerda para exibir o menu de personalização e clique em **Modelos**.

![Screenshot que realça o ícone de personalização para exibir o menu de personalização.][api-management-customize-menu]

A lista de modelos apresenta várias categorias de modelos que cobrem as diferentes páginas do portal do desenvolvedor. Cada modelo é diferente, mas os passos para editá-los e publicar as alterações são os mesmos. Para editar um modelo, clique no nome do modelo.

![Modelos de portal de desenvolvedores][api-management-templates-menu]

Clicar num modelo leva-o à página do portal do desenvolvedor que é personalizável por esse modelo. Neste exemplo, é apresentado o modelo **da lista de produtos.** O modelo **da lista de produtos** controla a área do ecrã indicada pelo retângulo vermelho.

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como os modelos de perfil do **utilizador,** personalizam diferentes partes da mesma página.

![Modelos de perfil do utilizador][api-management-user-profile-templates]

O editor de cada modelo de portal de desenvolvedores tem duas secções exibidas na parte inferior da página. O lado esquerdo exibe o painel de edição para o modelo, e o lado direito exibe o modelo de dados para o modelo.

O painel de edição do modelo contém a marcação que controla a aparência e o comportamento da página correspondente no portal do desenvolvedor. A marcação no modelo utiliza a sintaxe [DotLiquid.](http://dotliquidmarkup.org/) Um editor popular da DotLiquid é [DotLiquid para Designers.](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) Quaisquer alterações feitas ao modelo durante a edição são apresentadas em tempo real no navegador, mas não são visíveis para os seus clientes até [que você guarde](#to-save-a-template) e [publique](#to-publish-a-template) o modelo.

![Marcação do modelo][api-management-template]

O **painel de dados do modelo** fornece um guia para o modelo de dados para as entidades que estão disponíveis para uso em um modelo particular. Fornece este guia exibindo os dados ao vivo que são atualmente apresentados no portal do desenvolvedor. Pode expandir as vidraças do modelo clicando no retângulo no canto superior direito do painel de **dados do modelo.**

![Modelo de dados do modelo][api-management-template-data]

No exemplo anterior, existem dois produtos apresentados no portal do desenvolvedor que foram recuperados a partir dos dados apresentados no painel de dados do **modelo,** como mostra o seguinte exemplo:

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

A marcação no modelo da **lista de produtos** processa os dados para fornecer a saída desejada através da recolha de produtos para apresentar informações e uma ligação a cada produto. Observe os `<search-control>` elementos e `<page-control>` elementos na marcação. Estes controlam a visualização dos controlos de pesquisa e paging na página. `ProductsStrings|PageTitleProducts` é uma referência de corda localizada que contém o `h2` texto do cabeçalho para a página. Para obter uma lista de recursos de cordas, controlos de página e ícones disponíveis para utilização em modelos de portal de desenvolvedores de desenvolvedores, consulte [a referência de modelos do portal do desenvolvedor da API Management](api-management-developer-portal-templates-reference.md).

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
Para guardar um modelo, clique em guardar no editor de modelo.

![Guardar o modelo][api-management-save-template]

As alterações guardadas não estão ao vivo no portal do desenvolvedor até serem publicadas.

## <a name="to-publish-a-template"></a>Para publicar um modelo
Os modelos guardados podem ser publicados individualmente ou todos juntos. Para publicar um modelo individual, clique em publicar no editor de modelo.

![Publicar modelo][api-management-publish-template]

Clique **em Sim** para confirmar e fazer o modelo ao vivo no portal do desenvolvedor.

![Screenshot que mostra onde você seleciona Sim para fazer o modelo ao vivo.][api-management-publish-template-confirm]

Para publicar todas as versões de modelo não publicadas atualmente, clique em **Publicar** na lista de modelos. Os modelos não publicados são designados por um asterisco seguindo o nome do modelo. Neste exemplo, a **lista de produtos** e modelos de **produto** estão a ser publicados.

![Publicar modelos][api-management-publish-templates]

Clique **em Publicar personalizações** para confirmar.

![Confirmar publicação][api-management-publish-customizations]

Os modelos recém-publicados são eficazes imediatamente no portal do desenvolvedor.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior
Para reverter um modelo para a versão publicada anteriormente, clique em reverter no editor de modelo.

![Screenshot que realça o ícone que você usa para reverter um modelo.][api-management-revert-template]

Clique em **Sim** para confirmar.

![Screenshot que mostra onde seleciona Sim para confirmar as alterações.][api-management-revert-template-confirm]

A versão previamente publicada de um modelo é transmitida ao vivo no portal do desenvolvedor uma vez que a operação reverteda está concluída.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão padrão
Restaurar os modelos para a sua versão padrão é um processo em duas etapas. Primeiro os modelos devem ser restaurados e, em seguida, as versões restauradas devem ser publicadas.

Para restaurar um único modelo para a versão predefinida clique para restaurar no editor de modelo.

![Modelo reverter][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos nas suas versões predefinitivos, clique em **Restaurar os modelos predefinidos** na lista de modelos.

![Modelos de restauro][api-management-restore-templates]

Os modelos restaurados devem então ser publicados individualmente ou todos de uma só vez, seguindo os passos para [publicar um modelo](#to-publish-a-template).

## <a name="next-steps"></a>Passos seguintes
Para obter informações de referência para modelos de portal de desenvolvedores, recursos de cordas, ícones e controlos de página, consulte [a referência de modelos do portal do desenvolvedor da API Management](api-management-developer-portal-templates-reference.md).

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
