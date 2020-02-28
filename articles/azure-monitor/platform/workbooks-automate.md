---
title: Livros de trabalho do Monitor Azure e modelos de gestor de recursos azure
description: Simplificar relatórios complexos com livros de trabalho azure monitor pré-construídos e personalizados implantados através de modelos de gestor de recursos azure
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658409"
---
# <a name="programmatically-manage-workbooks"></a>Gerir programáticamente livros

Os proprietários de recursos têm a opção de criar e gerir os seus livros programáticamente através de modelos de Gestor de Recursos. 

Isto pode ser útil em cenários como:
* Implementação de relatórios de análise específicos de org ou de domínio, juntamente com implementações de recursos. Por exemplo, pode implementar livros de desempenho e falha específicos do org para as suas novas aplicações ou máquinas virtuais.
* Implementação de relatórios padrão ou dashboards utilizando livros de trabalho para recursos existentes.

O livro será criado no sub/grupo de recursos desejado e com o conteúdo especificado nos modelos do Gestor de Recursos.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Modelo de Gestor de Recursos Azure para implementação de livros de trabalho
1. Abra um livro que pretende implementar programáticamente.
2. Mude o livro para o modo de edição clicando no item da barra de ferramentas _Editar._
3. Abra o _Editor Avançado_ utilizando o botão _</>_ na barra de ferramentas.
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
| `workbookType` | A galeria onde o livro será mostrado. Os valores suportados incluem livro, `tsg`, Monitor Azure, etc. |
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
Por uma razão técnica, este mecanismo não pode ser utilizado para criar casos de livro na galeria de Livros de _Insights_ de Aplicação. Estamos a trabalhar para resolver esta limitação. Entretanto, recomendamos que utilize a galeria Guia de Resolução de Problemas (livroType: `tsg`) para implementar livros de trabalho relacionados com a Aplicação Insights.

## <a name="next-steps"></a>Passos seguintes

Explore como os livros de trabalho estão a ser usados para alimentar o novo [Monitor Azure para a experiência](../insights/storage-insights-overview.md)de armazenamento.

