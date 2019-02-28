---
title: Pesquisas guardadas em soluções de gestão | Documentos da Microsoft
description: Soluções de gestão incluem, geralmente, pesquisas guardadas no Log Analytics para analisar dados recolhidos pela solução. Este artigo descreve como definir pesquisas guardadas num modelo do Resource Manager, para que possam ser incluídos em soluções de gestão do Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64b79d613463ac2097a89a8e3ca3870b885a3332
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985262"
---
# <a name="adding-log-analytics-saved-searches-to-management-solution-preview"></a>A adição do Log Analytics, pesquisas guardadas para a solução de gestão (pré-visualização)

> [!IMPORTANT]
> Uma versão anterior deste artigo inclui detalhes sobre a criação de um alerta com um modelo do Resource Manager. Estas informações é de data agora que [alertas do Log Analytics tem sido expandidas para o Azure Monitor](../platform/alerts-extend.md). Para obter detalhes sobre como criar um alerta de registo com um modelo do Resource Manager, consulte [gerir alertas de registo com o modelo de recursos do Azure](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeitas a alterações.

Este artigo descreve como definir o Log Analytics, guardar pesquisas num [modelo de gestão de recursos](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) para que possam ser incluídos na [soluções de gestão](solutions-creating.md).

> [!NOTE]
> Os exemplos neste artigo utilizam parâmetros e variáveis que são necessárias ou comuns para soluções de gestão e descrito em [estrutura e compilação de uma solução de gestão no Azure](solutions-creating.md)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já está familiarizado com a [criar uma solução de gestão](solutions-creating.md) e a estrutura de um [modelo do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) e o arquivo da solução.


## <a name="log-analytics-workspace"></a>Área de trabalho do Log Analytics
Todos os recursos do Log Analytics estão contidos num [área de trabalho](../../azure-monitor/platform/manage-access.md). Conforme descrito em [área de trabalho do Log Analytics e a conta de automatização](solutions.md#log-analytics-workspace-and-automation-account), a área de trabalho não está incluída na solução de gestão, mas tem de existir antes da solução está instalada. Se não estiver disponível, em seguida, a instalação da solução falha.

O nome da área de trabalho é no nome de cada recurso do Log Analytics. Isso é feito na solução com o **área de trabalho** parâmetro como no seguinte exemplo de um recurso de SavedSearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Versão de API de análise do registo
Todos os recursos do Log Analytics definidos num modelo do Resource Manager de ter uma propriedade **apiVersion** que define a versão da API deve usar o recurso.

A tabela seguinte lista a versão de API para o recurso utilizado neste exemplo.

| Tipo de recurso | Versão de API | Consulta |
|:---|:---|:---|
| savedSearches | 2017-03-15-pré-visualização | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>Pesquisas Guardadas
Incluem [pesquisas guardadas](../../azure-monitor/log-query/log-query-overview.md) numa solução para permitir que os utilizadores para consultar os dados recolhidos pela sua solução. Pesquisas de guardado, são apresentados em **pesquisas guardadas** no portal do Azure. Uma pesquisa guardada também é necessária para cada alerta.

[A pesquisa guardada do log Analytics](../../azure-monitor/log-query/log-query-overview.md) recursos têm um tipo de `Microsoft.OperationalInsights/workspaces/savedSearches` e ter a seguinte estrutura. Isto inclui as variáveis e parâmetros comuns, para que pode copiar e cole este fragmento de código no seu ficheiro de solução e alterar os nomes de parâmetro.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Cada propriedade de uma procura guardada é descrita na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| categoria | A categoria para a pesquisa guardada.  Qualquer pesquisas guardadas na mesma solução, muitas vezes, irão partilhar uma única categoria para que estes são agrupados em conjunto na consola do. |
| DisplayName | Nome a apresentar para a pesquisa guardada no portal. |
| consulta | Consulta seja executada. |

> [!NOTE]
> Poderá ter de utilizar os carateres de escape na consulta, se ele inclui carateres que poderão ser interpretados como JSON. Por exemplo, se a consulta foi **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, deve ser gravado no arquivo da solução como **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.


## <a name="next-steps"></a>Passos Seguintes
* [Adicionar vistas](solutions-resources-views.md) à sua solução de gestão.
* [Adicionar runbooks de automatização e outros recursos](solutions-resources-automation.md) à sua solução de gestão.
