---
title: Emitir modelos na gestão de API do Azure | Documentos da Microsoft
description: Saiba como personalizar o conteúdo das páginas de problema no portal do programador na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 99732a61ab64f8600ca368d4af5f47451014a993
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760755"
---
# <a name="issue-templates-in-azure-api-management"></a>Emitir modelos na gestão de API do Azure
Gestão de API do Azure fornece-lhe a capacidade de personalizar o conteúdo de páginas de portal de programador usando um conjunto de modelos que configurar o seu conteúdo. Usando [DotLiquid](http://dotliquidmarkup.org/) sintaxe e o editor à sua escolha, tal como [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto fornecido de localizadas [recursos de cadeias de caracteres](api-management-template-resources.md#strings), [glifo recursos](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), tem uma grande flexibilidade para configurar o conteúdo das páginas, conforme ache usá-los.  
  
 Os modelos nesta secção permitem-lhe personalizar o conteúdo das páginas de problema no portal do programador.  
  
-   [Lista de problemas](#IssueList)  
  
> [!NOTE]
>  Modelos predefinidos de exemplo estão incluídos na documentação do seguinte, mas estão sujeitas a alterações devido a melhorias contínuas. Pode ver os modelos predefinidos em direto no portal do programador ao navegar para os modelos individuais pretendidos. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> Lista de problemas  
 O **lista de problemas** modelo permite-lhe personalizar o corpo da página de lista de problema no portal do programador.  
  
 ![Portal do Programador de lista de emitir](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Portal do Programador de lista de problema APIM")  
  
### <a name="default-template"></a>Modelo predefinido  
  
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
 O `Issue list` modelo pode usar o seguinte procedimento [controlos de página](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|Problemas|Coleção de [problema](api-management-template-data-model-reference.md#Issue) entidades.|Os problemas visíveis ao utilizador atual.|  
|Paginação|[Paginação](api-management-template-data-model-reference.md#Paging) entidade.|As informações de paginação para a coleção de aplicações.|  
|IsAuthenticated|boolean|Se o utilizador atual é com sessão iniciada para o portal do programador.|  
|CanReportIssues|boolean|Se o utilizador atual tem permissões para enviar um problema.|  
|Pesquisa|string|Esta propriedade foi preterida e não deve ser utilizada.|  
  
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

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](api-management-developer-portal-templates.md).
