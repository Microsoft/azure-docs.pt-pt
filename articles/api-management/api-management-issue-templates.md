---
title: Modelos de emissão em Azure API Management | Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas Emissão no portal do desenvolvedor na Azure API Management.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85846862"
---
# <a name="issue-templates-in-azure-api-management"></a>Modelos de emissão na Gestão API da Azure
A Azure API Management fornece-lhe a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configuram o seu conteúdo. Utilizando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor à sua escolha, como [DotLiquid para Designers,](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)e um conjunto fornecido de recursos de [cordas](api-management-template-resources.md#strings)localizados, [recursos glifos](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), você tem uma grande flexibilidade para configurar o conteúdo das páginas como você acha adequado usando estes modelos.  
  
 Os modelos desta secção permitem personalizar o conteúdo das páginas 'Emitir' no portal do desenvolvedor.  
  
-   [Lista de edição](#IssueList)  
  
> [!NOTE]
>  Os modelos predefinidos da amostra estão incluídos na seguinte documentação, mas estão sujeitos a alterações devido a melhorias contínuas. Pode ver os modelos de predefinição ao vivo no portal do desenvolvedor navegando para os modelos individuais pretendidos. Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a> Lista de edição  
 O modelo **de lista de problemas** permite-lhe personalizar o corpo da página da lista de problemas no portal do desenvolvedor.  
  
 ![Portal do Programador de Listas de Problemas](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Portal de Programadores de Listas de Problemas DA APIM")  
  
### <a name="default-template"></a>Modelo de padrão  
  
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
 O `Issue list` modelo pode utilizar os [seguintes controlos de página](api-management-page-controls.md).  
  
-   [controlo de paging](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Issues`|Entidades de Recolha de [Emissão.](api-management-template-data-model-reference.md#Issue)|Os problemas visíveis para o utilizador atual.|  
|`Paging`|[Entidade de paging.](api-management-template-data-model-reference.md#Paging)|A informação de paging para a recolha de aplicações.|  
|`IsAuthenticated`|boolean|Se o utilizador atual está inscrito no portal do desenvolvedor.|  
|`CanReportIssues`|boolean|Se o utilizador atual tem permissões para arquivar um problema.|  
|`Search`|string|Esta propriedade é depreciada e não deve ser usada.|  
  
### <a name="sample-template-data"></a>Dados do modelo de amostra  
  
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
Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](api-management-developer-portal-templates.md).
