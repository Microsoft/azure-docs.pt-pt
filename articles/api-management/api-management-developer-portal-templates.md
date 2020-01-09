---
title: Personalizar o portal do desenvolvedor de gerenciamento de API usando modelos
titleSuffix: Azure API Management
description: Saiba como personalizar o portal do desenvolvedor de gerenciamento de API do Azure usando modelos.
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430778"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Como personalizar o portal do desenvolvedor de gerenciamento de API do Azure usando modelos

Existem três formas fundamentais de personalizar o portal do programador na Gestão de API do Azure:

* [Editar o conteúdo de páginas estáticas e elementos de esquema da página][modify-content-layout]
* [Atualizar os estilos usados para elementos de página no portal do desenvolvedor][customize-styles]
* [Modificar os modelos usados para páginas geradas pelo portal][portal-templates] (explicado neste guia)

Os modelos são usados para personalizar o conteúdo das páginas do portal do desenvolvedor geradas pelo sistema (por exemplo, documentos de API, produtos, autenticação de usuário, etc.). Usando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e um conjunto fornecido de recursos de cadeia de caracteres localizados, ícones e controles de página, você tem grande flexibilidade para configurar o conteúdo das páginas como você vê adequado.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Visão geral dos modelos do portal do desenvolvedor

A edição de modelos é feita no **portal do desenvolvedor** enquanto estiver sendo conectado como um administrador. Para começar, primeiro abra o portal do Azure e clique em **portal do desenvolvedor** na barra de ferramentas de serviço da sua instância de gerenciamento de API.

Para acessar os modelos do portal do desenvolvedor, clique no ícone Personalizar à esquerda para exibir o menu personalização e clique em **modelos**.

![Modelos do portal do desenvolvedor][api-management-customize-menu]

A lista de modelos exibe várias categorias de modelos que abrangem as diferentes páginas no portal do desenvolvedor. Cada modelo é diferente, mas as etapas para editá-los e publicar as alterações são as mesmas. Para editar um modelo, clique no nome do modelo.

![Modelos do portal do desenvolvedor][api-management-templates-menu]

Clicar em um modelo leva você para a página do portal do desenvolvedor que é personalizável por esse modelo. Neste exemplo, o modelo de **lista de produtos** é exibido. O modelo de **lista de produtos** controla a área da tela indicada pelo retângulo vermelho.

![Modelo de lista de produtos][api-management-developer-portal-templates-overview]

Alguns modelos, como os modelos de **perfil de usuário** , personalizam diferentes partes da mesma página.

![Modelos de perfil de usuário][api-management-user-profile-templates]

O editor para cada modelo do portal do desenvolvedor tem duas seções exibidas na parte inferior da página. O lado esquerdo exibe o painel de edição para o modelo e o lado direito exibe o modelo de dados para o modelo.

O painel de edição de modelo contém a marcação que controla a aparência e o comportamento da página correspondente no portal do desenvolvedor. A marcação no modelo usa a sintaxe [DotLiquid](http://dotliquidmarkup.org/) . Um editor popular para DotLiquid é [DotLiquid para designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Todas as alterações feitas no modelo durante a edição são exibidas em tempo real no navegador, mas não são visíveis para seus clientes até que você [salve](#to-save-a-template) e [publique](#to-publish-a-template) o modelo.

![Marcação de modelo][api-management-template]

O painel **dados do modelo** fornece um guia para o modelo de dados para as entidades que estão disponíveis para uso em um modelo específico. Ele fornece este guia exibindo os dados dinâmicos que são atualmente exibidos no portal do desenvolvedor. Você pode expandir os painéis de modelo clicando no retângulo no canto superior direito do painel **dados do modelo** .

![Modelo de dados de modelo][api-management-template-data]

No exemplo anterior, há dois produtos exibidos no portal do desenvolvedor que foram recuperados dos dados exibidos no painel dados do **modelo** , conforme mostrado no exemplo a seguir:

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

A marcação no modelo de **lista de produtos** processa os dados para fornecer a saída desejada Iterando pela coleção de produtos para exibir informações e um link para cada produto individual. Observe os elementos `<search-control>` e `<page-control>` na marcação. Eles controlam a exibição dos controles de pesquisa e de paginação na página. `ProductsStrings|PageTitleProducts` é uma referência de cadeia de caracteres localizada que contém o texto do cabeçalho de `h2` para a página. Para obter uma lista de recursos de cadeia de caracteres, controles de página e ícones disponíveis para uso em modelos de portal do desenvolvedor, consulte [referência de modelos do portal do desenvolvedor de gerenciamento de API](api-management-developer-portal-templates-reference.md).

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
Para salvar um modelo, clique em salvar no editor de modelos.

![Guardar modelo][api-management-save-template]

As alterações salvas não são ativas no portal do desenvolvedor até que sejam publicadas.

## <a name="to-publish-a-template"></a>Para publicar um modelo
Os modelos salvos podem ser publicados individualmente ou todos juntos. Para publicar um modelo individual, clique em publicar no editor de modelos.

![Publicar modelo][api-management-publish-template]

Clique em **Sim** para confirmar e tornar o modelo ativo no portal do desenvolvedor.

![Confirmar publicação][api-management-publish-template-confirm]

Para publicar todas as versões de modelo atualmente não publicadas, clique em **publicar** na lista de modelos. Os modelos não publicados são designados por um asterisco após o nome do modelo. Neste exemplo, a **lista de produtos** e os modelos de **produtos** estão sendo publicados.

![Publicar modelos][api-management-publish-templates]

Clique em **publicar personalizações** para confirmar.

![Confirmar publicação][api-management-publish-customizations]

Os modelos publicados recentemente entram em vigor imediatamente no portal do desenvolvedor.

## <a name="to-revert-a-template-to-the-previous-version"></a>Para reverter um modelo para a versão anterior
Para reverter um modelo para a versão publicada anterior, clique em reverter no editor de modelos.

![Reverter modelo][api-management-revert-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-revert-template-confirm]

A versão publicada anteriormente de um modelo é ativa no portal do desenvolvedor quando a operação de reversão é concluída.

## <a name="to-restore-a-template-to-the-default-version"></a>Para restaurar um modelo para a versão padrão
A restauração de modelos para sua versão padrão é um processo de duas etapas. Primeiro, os modelos devem ser restaurados e as versões restauradas devem ser publicadas.

Para restaurar um único modelo para a versão padrão, clique em restaurar no editor de modelos.

![Reverter modelo][api-management-reset-template]

Clique em **Sim** para confirmar.

![Confirmar][api-management-reset-template-confirm]

Para restaurar todos os modelos para suas versões padrão, clique em **restaurar modelos padrão** na lista de modelos.

![Restaurar modelos][api-management-restore-templates]

Os modelos restaurados devem ser publicados individualmente ou todos de uma vez seguindo as etapas em [para publicar um modelo](#to-publish-a-template).

## <a name="next-steps"></a>Passos seguintes
Para obter informações de referência para modelos do portal do desenvolvedor, recursos de cadeia de caracteres, ícones e controles de página, consulte [referência de modelos do portal do desenvolvedor de gerenciamento de API](api-management-developer-portal-templates-reference.md).

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
