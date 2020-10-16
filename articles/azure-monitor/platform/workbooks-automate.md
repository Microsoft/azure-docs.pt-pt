---
title: Azure Monitor Workbooks e Azure Resource Manager
description: Simplificar relatórios complexos com livros de trabalho do Monitor Azure pré-construídos e personalizados implantados através de modelos de gestores de recursos Azure
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929732"
---
# <a name="programmatically-manage-workbooks"></a>Gerir programáticamente livros de trabalho

Os proprietários de recursos têm a opção de criar e gerir os seus livros programáticoticamente através de modelos de Gestor de Recursos.

Isto pode ser útil em cenários como:
* Implementação de relatórios de análise específicos de org ou domínio, juntamente com implementações de recursos. Por exemplo, pode implementar livros de desempenho e falha específicos da org para as suas novas apps ou máquinas virtuais.
* Implementação de relatórios padrão ou dashboards utilizando livros de trabalho para recursos existentes.

O livro será criado no sub/grupo de recursos pretendido e com o conteúdo especificado nos modelos de Gestor de Recursos.

Existem dois tipos de recursos de livro que podem ser geridos programáticamente:
* [Modelos de livro](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Instâncias de livro](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Modelo de gestor de recursos Azure para implantar um modelo de livro

1. Abra um livro que pretende implementar programáticamente.
2. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
3. Abra o _Editor Avançado_ utilizando o _</>_ botão na barra de ferramentas.
4. Certifique-se de que está no _separador Modelo de Galeria._

    ![Separador de modelo de galeria](./media/workbooks-automate/gallery-template.png)
1. Copie o JSON no modelo da galeria para a prancheta.
2. Abaixo está uma amostra Azure Resource Manager modelo que implementa um modelo de livro para a galeria de livros do Azure Monitor. Cole o JSON que copiou no lugar de `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . Um modelo de gestor de recursos Azure de referência que cria um modelo de livro pode ser encontrado [aqui.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)

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
1. No `galleries` objeto preencha as `name` `category` teclas e as teclas com os seus valores. Saiba mais sobre [os parâmetros](#parameters) na secção seguinte.
2. Implemente este modelo de Gestor de Recursos Azure utilizando o [portal Azure,](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) [interface de linha de comando,](../../azure-resource-manager/templates/deploy-cli.md) [PowerShell,](../../azure-resource-manager/templates/deploy-powershell.md)etc.
3. Abra o portal Azure e navegue para a galeria de livros escolhida no modelo Azure Resource Manager. No modelo de exemplo, navegue na galeria de livros do Azure Monitor:
    1. Abra o portal Azure e navegue até ao Azure Monitor
    2. Aberto `Workbooks` a partir da tabela de conteúdos
    3. Encontre o seu modelo na galeria na categoria `Deployed Templates` (será um dos itens roxos).

### <a name="parameters"></a>Parâmetros

|Parâmetros                |Explicação                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | O nome do recurso de modelo de livro no Azure Resource Manager.                                  |
|`type`                    | Sempre microsoft.insights/workbooktemplates                                                            |
| `location`               | A localização Azure em que o livro será criado.                                               |
| `apiVersion`             | Pré-visualização 2019-10-17                                                                                     |
| `type`                   | Sempre microsoft.insights/workbooktemplates                                                            |
| `galleries`              | O conjunto de galerias para mostrar este modelo de livro dentro                                                |
| `gallery.name`           | O nome amigável do modelo do livro na galeria.                                             |
| `gallery.category`       | O grupo na galeria para colocar o modelo dentro                                                     |
| `gallery.order`          | Um número que decide a ordem para mostrar o modelo dentro de uma categoria na galeria. Ordem inferior implica maior prioridade. |
| `gallery.resourceType`   | O tipo de recurso correspondente à galeria. Esta é geralmente a cadeia do tipo de recurso correspondente ao recurso (por exemplo, microsoft.operationalinsights/workspaces). |
|`gallery.type`            | Referido como tipo de livro, esta é uma chave única que diferencia a galeria dentro de um tipo de recurso. Os Insights de Aplicação, por exemplo, têm tipos `workbook` e `tsg` correspondem a diferentes galerias de livros. |

### <a name="galleries"></a>Galerias

| Galeria                                        | Tipo de recurso                                      | Tipo de livro |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Livros de trabalho no Azure Monitor                     | `Azure Monitor`                                    | `workbook`    |
| VM Insights no Monitor Azure                   | `Azure Monitor`                                    | `vm-insights` |
| Livros de trabalho no espaço de trabalho de analítica de log           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Livros de trabalho em Insights de Aplicações              | `microsoft.insights/component`                     | `workbook`    |
| Guias de resolução de problemas em Insights de Aplicações | `microsoft.insights/component`                     | `tsg`         |
| Utilização em Insights de Aplicações                  | `microsoft.insights/component`                     | `usage`       |
| Livros de trabalho no serviço Kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Livros de trabalho em grupos de recursos                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Livros de trabalho no Diretório Ativo Azure            | `microsoft.aadiam/tenant`                          | `workbook`    |
| VM Insights em máquinas virtuais                | `microsoft.compute/virtualmachines`                | `insights`    |
| VM Insights em conjuntos de escala de máquina virtual      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Modelo de gestor de recursos Azure para implantar uma instância de livro

1. Abra um livro que pretende implementar programáticamente.
2. Mude o livro para editar o modo clicando no item da barra de ferramentas _Editar._
3. Abra o _Editor Avançado_ utilizando o _</>_ botão na barra de ferramentas.
4. No editor, _altere_ o modelo do tipo de modelo para _o modelo de gestor de recursos._
5. O modelo de Gestor de Recursos para criar aparece no editor. Copie o conteúdo e use como-é ou funda-o com um modelo maior que também implementa o recurso-alvo.

    ![Imagem mostrando como obter o modelo de Gestor de Recursos a partir do UI do livro](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modelo de gestor de recursos Azure amostra
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
| `workbookDisplayName` | O nome amigável para o livro que é usado na Galeria ou Lista Guardado. Precisa ser único no âmbito do grupo de recursos e fonte |
| `workbookType` | A galeria onde o livro será mostrado. Os valores suportados incluem livro, `tsg` Azure Monitor, etc. |
| `workbookSourceId` | O ID da instância de recursos a que o livro será associado. O novo livro de trabalho aparecerá relacionado com este exemplo de recursos - por exemplo, na tabela de conteúdos dos recursos no _Livro de Trabalho_. Se quiser que o seu livro apareça na galeria de livros em Azure Monitor, utilize o _Azure Monitor_ em vez de um ID de recurso. |
| `workbookId` | O guia único para este caso de livro. Utilize _[newGuid)]_ para criar automaticamente um novo guia. |
| `kind` | Usado para especificar se o livro criado é partilhado ou privado. Use o valor _partilhado_ para livros partilhados e _utilizador_ para os privados. |
| `location` | O local do Azure onde o livro será criado. Utilize _[grupo de recursos().localização]_ para criá-lo no mesmo local que o grupo de recursos |
| `serializedData` | Contém o conteúdo ou a carga útil a utilizar no livro. Utilize o modelo de Gestor de Recursos a partir dos livros de UI para obter o valor |

### <a name="workbook-types"></a>Tipos de livros de trabalho
Os tipos de livros de trabalho especificam qual o tipo de galeria de livros que a nova instância do livro aparecerá. As opções incluem:

| Tipo | Localização da galeria |
| :------------- |:-------------|
| `workbook` | O padrão usado na maioria dos relatórios, incluindo a galeria de livros de trabalho de Insights de Aplicações, Monitor Azure, etc.  |
| `tsg` | A galeria de guias de resolução de problemas em Insights de Aplicação |
| `usage` | A _galeria Mais_ sob _Uso_ em Insights de Aplicação |

### <a name="limitations"></a>Limitações
Por uma razão técnica, este mecanismo não pode ser utilizado para criar casos de livros na galeria de livros de _trabalho_ de Insights de Aplicação. Estamos a trabalhar para resolver esta limitação. Entretanto, recomendamos que utilize a galeria Do Guia de Resolução de Problemas (manualType: `tsg` ) para implementar livros relacionados com a Aplicação Insights.

## <a name="next-steps"></a>Passos seguintes

Explore como os livros estão a ser usados para alimentar o novo [Azure Monitor para a experiência de armazenamento.](../insights/storage-insights-overview.md)
