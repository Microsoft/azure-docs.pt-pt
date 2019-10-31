---
title: Modelos de problemas no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas de problemas no portal do desenvolvedor no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176637"
---
# <a name="issue-templates-in-azure-api-management"></a>Modelos de problemas no gerenciamento de API do Azure
O gerenciamento de API do Azure fornece a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configuram seu conteúdo. Usando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor de sua escolha, como [DotLiquid para designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto fornecido de [recursos de cadeia de caracteres](api-management-template-resources.md#strings)localizados, recursos de [glifos](api-management-template-resources.md#glyphs)e [controles de página](api-management-page-controls.md), você tem grande flexibilidade para configurar o conteúdo das páginas como você vê para se ajustar usando esses modelos.  
  
 Os modelos nesta seção permitem que você personalize o conteúdo das páginas de problemas no portal do desenvolvedor.  
  
-   [Lista de problemas](#IssueList)  
  
> [!NOTE]
>  Os modelos padrão de exemplo estão incluídos na documentação a seguir, mas estão sujeitos a alterações devido a melhorias contínuas. Você pode exibir os modelos padrão ao vivo no portal do desenvolvedor navegando até os modelos individuais desejados. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a>Lista de problemas  
 O modelo de **lista de problemas** permite que você personalize o corpo da página de lista de problemas no portal do desenvolvedor.  
  
 ![Portal do desenvolvedor da lista de problemas](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Portal do desenvolvedor da lista de problemas do APIM")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Controlos  
 O modelo de `Issue list` pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [controle de paginação](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Issues`|Coleção de entidades de [problema](api-management-template-data-model-reference.md#Issue) .|Os problemas visíveis para o usuário atual.|  
|`Paging`|Entidade de [paginação](api-management-template-data-model-reference.md#Paging) .|As informações de paginação da coleção de aplicativos.|  
|`IsAuthenticated`|boolean|Se o usuário atual está conectado ao portal do desenvolvedor.|  
|`CanReportIssues`|boolean|Se o usuário atual tem permissões para arquivar um problema.|  
|`Search`|string|Esta propriedade foi preterida e não deve ser usada.|  
  
### <a name="sample-template-data"></a>Dados de modelo de exemplo  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
