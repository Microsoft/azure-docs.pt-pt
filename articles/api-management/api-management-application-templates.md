---
title: Modelos de aplicativo no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas do aplicativo no portal do desenvolvedor no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: efb668971bc4a8c5a0968836aab71107aa6b88d5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072659"
---
# <a name="application-templates-in-azure-api-management"></a>Modelos de aplicativo no gerenciamento de API do Azure
O gerenciamento de API do Azure fornece a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configuram seu conteúdo. Usando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor de sua escolha, como [DotLiquid para designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto fornecido de [recursos de cadeia de caracteres](api-management-template-resources.md#strings)localizados, recursos de glifos e [controles de página](api-management-page-controls.md), você tem grande flexibilidade para configurar [](api-management-template-resources.md#glyphs) o conteúdo das páginas como você vê para se ajustar usando esses modelos.  
  
 Os modelos nesta seção permitem que você personalize o conteúdo das páginas do aplicativo no portal do desenvolvedor.  
  
-   [Lista de aplicativos](#ProductList)  
  
-   [Aplicação](#Application)  
  
> [!NOTE]
>  Os modelos padrão de exemplo estão incluídos na documentação a seguir, mas estão sujeitos a alterações devido a melhorias contínuas. Você pode exibir os modelos padrão ao vivo no portal do desenvolvedor navegando até os modelos individuais desejados. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a>Lista de aplicativos  
 O modelo de **lista de aplicativos** permite que você personalize o corpo da página de lista de aplicativos no portal do desenvolvedor.  
  
 ![Página de lista de aplicativos modelos do portal do desenvolvedor](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "Página de lista de aplicativos do APIM modelos do portal do desenvolvedor")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
 O `Product list` modelo pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Paging`|[](api-management-template-data-model-reference.md#Paging) Entidade de paginação.|As informações de paginação da coleção de aplicativos.|  
|`Applications`|Coleção de entidades de [aplicativo](api-management-template-data-model-reference.md#Application) .|Os aplicativos são visíveis para o usuário atual.|  
|`CategoryName`|Cadeia de caracteres|A categoria do aplicativo.|  
  
### <a name="sample-template-data"></a>Dados de modelo de exemplo  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="Application"></a>Aplicativo  
 O modelo de **aplicativo** permite que você personalize o corpo da página do aplicativo no portal do desenvolvedor.  
  
 ![Página do aplicativo modelos do portal do desenvolvedor](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "Modelos do portal do desenvolvedor da página do aplicativo APIM")  
  
### <a name="default-template"></a>Modelo predefinido  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Controlos  
 O `Application` modelo não permite o uso de nenhum [controle de página](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modelo de dados  
 Entidade de [aplicativo](api-management-template-data-model-reference.md#Application) .  
  
### <a name="sample-template-data"></a>Dados de modelo de exemplo  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
