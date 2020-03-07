---
title: Modelos de produto em Gestão API Azure  Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas do produto no portal de desenvolvimento de gestão da API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374368"
---
# <a name="product-templates-in-azure-api-management"></a>Modelos de produto na Gestão aPI Azure

A Azure API Management oferece-lhe a capacidade de personalizar o conteúdo das páginas do portal do programador utilizando um conjunto de modelos que configuram o seu conteúdo. Utilizando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor à sua escolha, como [o DotLiquid para Designers,](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)e um conjunto fornecido de recursos de [cordas](api-management-template-resources.md#strings)localizados, recursos [de Gliph](api-management-template-resources.md#glyphs)e [controlos](api-management-page-controls.md)de página, tem uma grande flexibilidade para configurar o conteúdo das páginas como entender que se ajuste usando estes modelos.  
  
 Os modelos desta secção permitem personalizar o conteúdo das páginas do produto no portal do desenvolvedor.  
  
-   [Lista de produtos](#ProductList)  
  
-   [Produto](#Product)  
  
> [!NOTE]
>  Os modelos de padrão da amostra estão incluídos na seguinte documentação, mas estão sujeitos a alterações devido a melhorias contínuas. Pode ver os modelos de predefinição ao vivo no portal do desenvolvedor navegando para os modelos individuais desejados. Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a>Lista de produtos  
 O modelo **da lista** de produtos permite-lhe personalizar o corpo da página da lista de produtos no portal do desenvolvedor.  
  
 ![Lista de produtos](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
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
  
### <a name="controls"></a>Controlos  
 O modelo `Product list` pode utilizar os [seguintes controlos](api-management-page-controls.md)de página .  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [controlo de pesquisa](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Paginação|[Entidade paginga.](api-management-template-data-model-reference.md#Paging)|A informação de paging para a recolha de produtos.|  
|Filtragem|[Entidade de filtragem.](api-management-template-data-model-reference.md#Filtering)|A informação de filtragem para a página da lista de produtos.|  
|Produtos|Recolha de entidades de [produto.](api-management-template-data-model-reference.md#Product)|Os produtos visíveis para o utilizador atual.|  
  
### <a name="sample-template-data"></a>Dados do modelo de amostra  
  
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
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
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
  
##  <a name="Product"></a>Produto  
 O modelo de **Produto** permite-lhe personalizar o corpo da página do produto no portal do desenvolvedor.  
  
 ![Página de produto do portal do desenvolvedor](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Controlos  
 O modelo `Product list` pode utilizar os [seguintes controlos](api-management-page-controls.md)de página .  
  
-   [botão de assinatura](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Produto|[Produto](api-management-template-data-model-reference.md#Product)|O produto especificado.|  
|IsDeveloperSubscribed|boolean|Se o utilizador atual está subscrito a este produto.|  
|Estado de Assinaturas|número|O estado da assinatura. Os estados possíveis são:<br /><br /> -   `0 - suspended` – a subscrição está bloqueada e o assinante não pode ligar para quaisquer APIs do produto.<br />-   `1 - active` – a subscrição está ativa.<br />-   `2 - expired` – a subscrição atingiu a data de validade e foi desativada.<br />-   `3 - submitted` – o pedido de subscrição foi feito pelo programador, mas ainda não foi aprovado ou rejeitado.<br />-   `4 - rejected` – o pedido de subscrição foi negado por um administrador.<br />-   `5 - cancelled` – a subscrição foi cancelada pelo programador ou administrador.|  
|Limites|array|Esta propriedade é depreciada e não deve ser usada.|  
|DelegatedSubscriptionEnabled|boolean|Se a [delegação](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) está habilitada para esta subscrição.|  
|DelegatedSubscriptionUrl|string|Se a delegação estiver ativada, o URL de subscrição delegado.|  
|IsAgreed|boolean|Se o produto tiver termos, se o utilizador atual concordou com os termos.|  
|Subscrições|Recolha de entidades [sumárias de subscrição.](api-management-template-data-model-reference.md#SubscriptionSummary)|As assinaturas do produto.|  
|Apis|Coleção de entidades [DaPi.](api-management-template-data-model-reference.md#API)|As APIs neste produto.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Se o utilizador atual é elegível para subscrever este produto no que diz respeito ao limite de subscrição.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Se o utilizador atual é elegível para subscrever este produto no que diz respeito a múltiplas subscrições que estão ou não a ser permitidas.|  
  
### <a name="sample-template-data"></a>Dados do modelo de amostra  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](api-management-developer-portal-templates.md).
