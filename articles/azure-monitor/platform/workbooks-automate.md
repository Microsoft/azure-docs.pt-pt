---
title: Livros de trabalho do Monitor Azure e modelos de gestor de recursos azure
description: Simplificar relatórios complexos com livros de trabalho azure monitor pré-construídos e personalizados implantados através de modelos de gestor de recursos azure
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mbullwin
ms.openlocfilehash: 76ecc3ee17353ebd0bbead1bba959f85d521d0df
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982144"
---
# <a name="programmatically-manage-workbooks"></a>Gerir programáticamente livros

Os proprietários de recursos têm a opção de criar e gerir os seus livros programáticamente através de modelos de Gestor de Recursos.

Isto pode ser útil em cenários como:
* Implementação de relatórios de análise específicos de org ou de domínio, juntamente com implementações de recursos. Por exemplo, pode implementar livros de desempenho e falha específicos do org para as suas novas aplicações ou máquinas virtuais.
* Implementação de relatórios padrão ou dashboards utilizando livros de trabalho para recursos existentes.

O livro será criado no sub/grupo de recursos desejado e com o conteúdo especificado nos modelos do Gestor de Recursos.

Existem dois tipos de recursos de livro que podem ser geridos programáticamente:
* [Modelos de livro](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Casos de livro](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Modelo de Gestor de Recursos Azure para implementar um modelo de livro

1. Abra um livro que pretende implementar programáticamente.
2. Mude o livro para o modo de edição clicando no item da barra de ferramentas _Editar._
3. Abra o _Editor_ _</>_ Avançado utilizando o botão na barra de ferramentas.
4. Certifique-se de que está no separador _Modelo de Galeria._

    ![Separador de modelo de galeria](./media/workbooks-automate/gallery-template.png)
1. Copie o JSON no modelo da galeria para a área de prancheta.
2. Abaixo está um modelo de Gestor de Recursos Azure que implementa um modelo de livro para a galeria de livros Azure Monitor. Colar o JSON que copiou `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>`no lugar de . Um modelo de gestor de recursos Azure de referência que cria um modelo de livro pode ser encontrado [aqui](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. No `galleries` objeto preencha `name` `category` as chaves e chaves com os seus valores. Saiba mais sobre [os parâmetros](#parameters) na secção seguinte.
2. Implemente este modelo de Gestor de Recursos Azure utilizando o [portal Azure,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) [interface de linha de comando,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [PowerShell,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)etc.
3. Abra o portal Azure e navegue até à galeria de livros escolhida no modelo Do Gestor de Recursos Azure. No modelo de exemplo, navegue para a galeria do livro Azure Monitor:
    1. Abra o portal Azure e navegue para o Monitor Azure
    2. Aberto `Workbooks` da tabela de conteúdos
    3. Encontre o seu modelo `Deployed Templates` na galeria na categoria (será um dos itens roxos).

### <a name="parameters"></a>Parâmetros

|Parâmetros                |Explicação                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | O nome do recurso modelo do livro em Azure Resource Manager.                                  |
|`type`                    | Sempre microsoft.insights/modelos de livro                                                            |
| `location`               | A localização Azure onde o livro será criado.                                               |
| `apiVersion`             | Antevisão 2019-10-17                                                                                     |
| `type`                   | Sempre microsoft.insights/modelos de livro                                                            |
| `galleries`              | O conjunto de galerias para mostrar este modelo de livro em.                                                |
| `gallery.name`           | O nome amigável do modelo do livro na galeria.                                             |
| `gallery.category`       | O grupo na galeria para colocar o modelo dentro                                                     |
| `gallery.order`          | Um número que decide a ordem para mostrar o modelo dentro de uma categoria na galeria. A ordem mais baixa implica uma maior prioridade. |
| `gallery.resourceType`   | O tipo de recurso correspondente à galeria. Esta é geralmente a cadeia do tipo de recurso correspondente ao recurso (por exemplo, microsoft.operationalinsights/workspaces ). |
|`gallery.type`            | Referido como tipo de livro, esta é uma chave única que diferencia a galeria dentro de um tipo de recurso. Os Insights de Aplicação, por exemplo, têm tipos `workbook` e `tsg` correspondentes a diferentes galerias de livros. |

### <a name="galleries"></a>Galerias

| Galeria                                        | Tipo de recurso                                      | Tipo de livro |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Livros de livros no Monitor Azure                     | `Azure Monitor`                                    | `workbook`    |
| VM Insights no Monitor Azure                   | `Azure Monitor`                                    | `vm-insights` |
| Livros de log analytics espaço de trabalho           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Livros de trabalho em Insights de Aplicação              | `microsoft.insights/component`                     | `workbook`    |
| Guias de resolução de problemas em Insights de Aplicação | `microsoft.insights/component`                     | `tsg`         |
| Utilização em Insights de Aplicação                  | `microsoft.insights/component`                     | `usage`       |
| Livros de trabalho no serviço Kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Livros de trabalho em grupos de recursos                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Livros de livros em Diretório Ativo Azure            | `microsoft.aadiam/tenant`                          | `workbook`    |
| VM Insights em máquinas virtuais                | `microsoft.compute/virtualmachines`                | `insights`    |
| VM Insights em conjuntos de escala de máquina virtual      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Modelo de Gestor de Recursos Azure para implementar uma instância de livro

1. Abra um livro que pretende implementar programáticamente.
2. Mude o livro para o modo de edição clicando no item da barra de ferramentas _Editar._
3. Abra o _Editor_ _</>_ Avançado utilizando o botão na barra de ferramentas.
4. No editor, altere o modelo de _modelo_ para o _modelo de gestor_de recursos .
5. O modelo de Gestor de Recursos para criar aparece no editor. Copie o conteúdo e use-o como está ou misture-o com um modelo maior que também implementa o recurso-alvo.

    ![Imagem mostrando como obter o modelo de Gestor de Recursos de dentro do livro UI](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modelo de gestor de recursos azure da amostra
Este modelo mostra como implementar um livro simples que exibe um 'Hello World!'
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parâmetros do modelo

| Parâmetro | Explicação |
| :------------- |:-------------|
| `workbookDisplayName` | O nome amigável para o livro que é usado na Galeria ou Lista Guardada. Precisa ser único no âmbito do grupo de recursos e fonte |
| `workbookType` | A galeria onde o livro será mostrado. Os valores suportados `tsg`incluem livro, Monitor Azure, etc. |
| `workbookSourceId` | A identificação da instância de recursos a que o livro será associado. O novo livro aparecerá relacionado com esta instância de recursos - por exemplo, na tabela de conteúdos do recurso ao abrigo do Livro de _Trabalho._ Se quiser que o seu livro apareça na galeria do livro no Azure Monitor, utilize a cadeia _Azure Monitor_ em vez de um ID de recursos. |
| `workbookId` | O guia único para este caso do livro. Utilize _[newGuid)]_ para criar automaticamente um novo guia. |
| `kind` | Usado para especificar se o livro criado é partilhado ou privado. Use o valor _partilhado_ para livros partilhados e _utilizador_ para privados. |
| `location` | A localização Azure onde será criado o livro de trabalhos. Utilize _[recursosGroup().localização]_ para criá-lo no mesmo local que o grupo de recursos |
| `serializedData` | Contém o conteúdo ou carga útil a utilizar no livro. Use o modelo de Gestor de Recursos dos livros UI para obter o valor |

### <a name="workbook-types"></a>Tipos de livro
Os tipos de livro especificam em que tipo de galeria de livro a nova instância do livro aparecerá. As opções incluem:

| Tipo | Localização da galeria |
| :------------- |:-------------|
| `workbook` | O padrão usado na maioria dos relatórios, incluindo a galeria de livros de trabalho de Insights de Aplicação, Monitor Azure, etc.  |
| `tsg` | A galeria Desacato guias em Insights de Aplicação |
| `usage` | A _galeria Mais_ sob _Utilização_ em Insights de Aplicação |

### <a name="limitations"></a>Limitações
Por uma razão técnica, este mecanismo não pode ser utilizado para criar casos de livro na galeria de Livros de _Insights_ de Aplicação. Estamos a trabalhar para resolver esta limitação. Entretanto, recomendamos que utilize a galeria Guia de `tsg`Resolução de Problemas (livroType: ) para implementar livros de trabalho relacionados com a Aplicação Insights.

## <a name="next-steps"></a>Passos seguintes

Explore como os livros de trabalho estão a ser usados para alimentar o novo [Monitor Azure para a experiência](../insights/storage-insights-overview.md)de armazenamento.
