---
title: Pontos de vista em soluções de gestão  Microsoft Docs
description: 'As soluções de gestão normalmente incluem uma ou mais vistas para visualizar dados.  Este artigo descreve como exportar uma visão criada pelo View Designer e incluí-la numa solução de gestão. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663050"
---
# <a name="views-in-management-solutions-preview"></a>Pontos de vista em soluções de gestão (Pré-visualização)
> [!NOTE]
> Esta é uma documentação preliminar para a criação de soluções de gestão que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.    


[As soluções](solutions.md) de gestão normalmente incluem uma ou mais vistas para visualizar dados.  Este artigo descreve como exportar uma visão criada pelo [View Designer](../../azure-monitor/platform/view-designer.md) e incluí-la numa solução de gestão.  

> [!NOTE]
> As amostras deste artigo utilizam parâmetros e variáveis que são necessários ou comuns a soluções de gestão e descritas no [Design e constroem uma solução de gestão em Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já está familiarizado com a forma de [criar uma solução](solutions-creating.md) de gestão e a estrutura de um ficheiro de solução.

## <a name="overview"></a>Descrição geral
Para incluir uma visão numa solução de gestão, cria-se um **recurso** para o mesmo no ficheiro de [solução.](solutions-creating.md)  O JSON que descreve a configuração detalhada da vista é tipicamente complexo e não algo que um autor típico da solução seria capaz de criar manualmente.  O método mais comum é criar a vista usando o [View Designer,](../../azure-monitor/platform/view-designer.md)exportá-la e, em seguida, adicionar a sua configuração detalhada à solução.

Os passos básicos para adicionar uma visão a uma solução são os seguintes.  Cada passo é descrito mais detalhadamente nas secções abaixo.

1. Exporte a vista para um arquivo.
2. Crie o recurso de visualização na solução.
3. Adicione os detalhes da vista.

## <a name="export-the-view-to-a-file"></a>Exportar a vista para um arquivo
Siga as instruções no [Log Analytics View Designer](../../azure-monitor/platform/view-designer.md) para exportar uma vista para um ficheiro.  O ficheiro exportado estará em formato JSON com os [mesmos elementos que o ficheiro de solução](solutions-solution-file.md).  

O elemento **de recursos** do ficheiro view terá um recurso com um tipo de **Microsoft.OperationalInsights/espaços** de trabalho que representa o espaço de trabalho log Analytics.  Este elemento terá um subelemento com um tipo de ponto de **vista** que representa a vista e contém a sua configuração detalhada.  Irá copiar os detalhes deste elemento e, em seguida, copiá-lo na sua solução.

## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de visualização na solução
Adicione o recurso de visualização seguinte ao elemento **de recursos** do seu ficheiro de solução.  Isto usa variáveis que são descritas abaixo que você também deve adicionar.  Note que as propriedades **do Dashboard** e **do OverviewTile** são espaços reservados que irá substituir com as propriedades correspondentes do ficheiro de visualização exportado.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Adicione as seguintes variáveis ao elemento variável do ficheiro de solução e substitua os valores pelos valores para a sua solução.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Tenha em anote que poderá copiar todo o recurso de visualização do seu ficheiro de visualização exportado, mas terá de efazer as seguintes alterações para que funcione na sua solução.  

* O **tipo** de recurso de visualização tem de ser alterado de **vistas** para **Microsoft.OperationalInsights/espaços de trabalho**.
* A propriedade **de nome** para o recurso de vista precisa ser alterada para incluir o nome do espaço de trabalho.
* A dependência do espaço de trabalho precisa de ser removida, uma vez que o recurso do espaço de trabalho não está definido na solução.
* A propriedade **DisplayName** precisa de ser adicionada à vista.  O **Id,** **O Nome**e o Nome do **Ecrã** devem coincidir.
* Os nomes dos parâmetros devem ser alterados de acordo com o conjunto de parâmetros exigido.
* As variáveis devem ser definidas na solução e utilizadas nas propriedades apropriadas.

### <a name="log-analytics-api-version"></a>Versão API de Log Analytics
Todos os recursos do Log Analytics definidos num modelo de Gestor de Recursos têm uma **apiVersão** de propriedade que define a versão da API que o recurso deve usar.  Esta versão é diferente para vistas com consultas que usam o [legado e a linguagem de consulta atualizada.](../../azure-monitor/log-query/log-query-overview.md)  

 O quadro seguinte especifica as versões Log Analytics API para visualizações em espaços de trabalho legados e atualizados: 

| Versão workspace | Versão API | Consulta |
|:---|:---|:---|
| v1 (legado)   | Antevisão 2015-11-01 | Formato legado.<br> Exemplo: Type=EventLevelName = Erro  |
| v2 (atualizado) | Antevisão 2015-11-01 | Formato legado.  Convertido em formato atualizado na instalação.<br> Exemplo: Type=EventLevelName = Erro<br>Convertido para: &#124; Evento onde EventLevelName == "Error"  |
| v2 (atualizado) | Antevisão 2017-03-03 | Formato de upgrade. <br>Exemplo: &#124; Evento onde eventlevelName == "Error"  |


## <a name="add-the-view-details"></a>Adicione os detalhes da vista
O recurso de visualização no ficheiro de vista exportado conterá dois elementos no elemento **de propriedades** chamado **Dashboard** e **OverviewTile** que contêm a configuração detalhada da vista.  Copie estes dois elementos e o seu conteúdo no elemento **de propriedades** do recurso de visualização no seu ficheiro de solução.

## <a name="example"></a>Exemplo
Por exemplo, a amostra que se segue mostra um simples ficheiro de solução com vista.  As elipses (...) são mostradas para o **dashboard** e **conteúdo do OverviewTile** por razões espaciais.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Passos seguintes
* Conheça todos os detalhes da criação de soluções de [gestão.](solutions-creating.md)
* Inclua [os livros de execução da Automação na sua solução de gestão.](solutions-resources-automation.md)
