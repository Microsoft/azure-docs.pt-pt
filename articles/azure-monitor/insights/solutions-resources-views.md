---
title: Modos de exibição em soluções de gestão | Documentos da Microsoft
description: 'Soluções de gestão, normalmente, irão incluir uma ou mais exibições para visualizar os dados.  Este artigo descreve como exportar uma vista criada pelo Designer do modo de exibição e incluí-lo numa solução de gestão. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769136"
---
# <a name="views-in-management-solutions-preview"></a>Modos de exibição em soluções de gestão (pré-visualização)
> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeitas a alterações.    


[Soluções de gestão](solutions.md) geralmente inclui uma ou mais exibições para visualizar os dados.  Este artigo descreve como exportar um modo de exibição criado pela [estruturador de vistas](../../azure-monitor/platform/view-designer.md) e incluí-lo numa solução de gestão.  

> [!NOTE]
> Os exemplos neste artigo utilizam parâmetros e variáveis que são necessárias ou comuns para soluções de gestão e descrito em [estrutura e compilação de uma solução de gestão no Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já está familiarizado com a [criar uma solução de gestão](solutions-creating.md) e a estrutura de um arquivo de solução.

## <a name="overview"></a>Descrição geral
Para incluir uma vista numa solução de gestão, crie uma **resource** para o mesmo no [arquivo da solução](solutions-creating.md).  O JSON que descreve a configuração detalhada da exibição é complexo, normalmente, porém e não algo que um autor de solução típica seria capaz de criar manualmente.  O método mais comum é criar a vista utilizando a [estruturador de vistas](../../azure-monitor/platform/view-designer.md), exportá-lo e, em seguida, adicione a respetiva configuração detalhada para a solução.

Seguem-se os passos básicos para adicionar uma exibição para uma solução.  Cada passo é descrito mais detalhadamente nas secções abaixo.

1. Exporte o modo de exibição para um ficheiro.
2. Crie o recurso de vista da solução.
3. Adicione os detalhes da vista.

## <a name="export-the-view-to-a-file"></a>Exportar o modo de exibição para um ficheiro
Siga as instruções em [estruturador de vista do Log Analytics](../../azure-monitor/platform/view-designer.md) para exportar uma exibição para um ficheiro.  O ficheiro exportado será no formato JSON com o mesmo [elementos como o arquivo da solução](solutions-solution-file.md).  

O **recursos** elemento do ficheiro de vista terá um recurso com um tipo de **Microsoft.OperationalInsights/workspaces** que representa a área de trabalho do Log Analytics.  Este elemento terá um subelemento com um tipo de **vistas** que representam a visão e contém a respetiva configuração detalhada.  Irá copiar os detalhes deste elemento e, em seguida, copie-as para a sua solução.

## <a name="create-the-view-resource-in-the-solution"></a>Criar o recurso de vista da solução
Adicione o seguinte recurso de vista para o **recursos** elemento do seu ficheiro de solução.  Esta opção utiliza as variáveis que são descritas abaixo que também tem de adicionar.  Tenha em atenção que o **Dashboard** e **OverviewTile** propriedades são espaços reservados que vão substituir com as propriedades correspondentes a partir do ficheiro de vista exportado.

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

Adicione as seguintes variáveis para o elemento de variáveis do arquivo da solução e substitua os valores para os de sua solução.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Tenha em atenção que pode copiar o recurso de toda a vista do seu ficheiro de vista exportado, mas terá de efetuar as seguintes alterações para que funcione na sua solução.  

* O **tipo** para a vista de recursos tem de ser alterado de **vistas** para **Microsoft.OperationalInsights/workspaces**.
* O **nome** propriedade para o recurso de exibição precisa ser alterada para incluir o nome de área de trabalho.
* A dependência da área de trabalho tem de ser removido, uma vez que o recurso de área de trabalho não está definido na solução.
* **DisplayName** propriedade tem de ser adicionada para o modo de exibição.  O **Id**, **nome**, e **DisplayName** todos devem corresponder.
* Os nomes de parâmetros devem ser alterados para corresponder ao conjunto de parâmetros necessário.
* Variáveis devem ser definidas na solução e utilizadas nas propriedades adequadas.

### <a name="log-analytics-api-version"></a>Versão de API de análise do registo
Todos os recursos do Log Analytics definidos num modelo do Resource Manager de ter uma propriedade **apiVersion** que define a versão da API deve usar o recurso.  Esta versão é diferente das vistas com consultas que usam o [legados e a linguagem de consulta atualizado](../../azure-monitor/log-query/log-query-overview.md).  

 A seguinte tabela especifica as versões de API do Log Analytics para exibições em áreas de trabalho de legado e atualizadas: 

| Versão de área de trabalho | Versão de API | Consulta |
|:---|:---|:---|
| V1 (Legado)   | 2015-11-01-pré-visualização | Formato de legado.<br> Exemplo: Type=Event EventLevelName = Error  |
| v2 (atualizado) | 2015-11-01-pré-visualização | Formato de legado.  Convertido em formato atualizado na instalação.<br> Exemplo: Type=Event EventLevelName = Error<br>Convertido em: Event &#124; where EventLevelName == "Error"  |
| v2 (atualizado) | 2017-03-03-pré-visualização | Formato de atualização. <br>Exemplo: Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Adicionar os detalhes da vista
O recurso de exibição no ficheiro exportado vista irá conter dois elementos no **propriedades** com o nome de elemento **Dashboard** e **OverviewTile** que contêm consultar os detalhes configuração da vista.  Copie esses dois elementos e o respetivo conteúdo para o **propriedades** elemento do recurso de vista em seu arquivo de solução.

## <a name="example"></a>Exemplo
Por exemplo, o exemplo a seguir mostra um ficheiro de solução simples com um modo de exibição.  Reticências (...) são exibidos para o **Dashboard** e **OverviewTile** conteúdo por motivos de espaço.

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




## <a name="next-steps"></a>Passos Seguintes
* Saiba os detalhes completos da criação [soluções de gestão](solutions-creating.md).
* Incluem [runbooks de automatização na sua solução de gestão](solutions-resources-automation.md).
