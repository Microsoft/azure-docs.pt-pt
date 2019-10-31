---
title: Gerenciar programaticamente pastas de trabalho Azure Monitor com modelos de Azure Resource Manager | Microsoft docs
description: Simplifique relatórios complexos com pastas de trabalho Azure Monitor parametrizadas predefinidas e personalizadas implantadas por meio de modelos de Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d5e22093fa796a9fbd60dc2bc242f37a6cac7cf0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166112"
---
# <a name="programmatically-manage-workbooks"></a>Gerenciar pastas de trabalho programaticamente

Os proprietários de recursos têm a opção de criar e gerenciar suas pastas de trabalho programaticamente por meio de modelos do Resource Manager. 

Isso pode ser útil em cenários como:
* Implantando relatórios de análise específicos da organização ou do domínio juntamente com implantações de recursos. Por exemplo, você pode implantar pastas de trabalho de desempenho e falha específicas da organização para seus novos aplicativos ou máquinas virtuais.
* Implantando relatórios padrão ou dashboards usando pastas de trabalho para recursos existentes.

A pasta de trabalho será criada no grupo de recursos/sub desejado e com o conteúdo especificado nos modelos do Resource Manager.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Modelo de Azure Resource Manager para implantar pastas de trabalho
1. Abra uma pasta de trabalho que você deseja implantar programaticamente.
2. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
3. Abra o _Editor avançado_ usando o botão _</>_ na barra de ferramentas.
4. No editor, alterne _tipo de modelo_ para _modelo do Resource Manager_.
5. O modelo do Resource Manager para a criação é mostrado no editor. Copie o conteúdo e use-o no estado em que se encontra ou mescle-o com um modelo maior que também implanta o recurso de destino.

    ![Imagem mostrando como obter o modelo do Resource Manager de dentro da interface do usuário da pasta de trabalho](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Modelo de Azure Resource Manager de exemplo
Este modelo mostra como implantar uma pasta de trabalho simples que exibe um ' Olá, Mundo! '
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
| `workbookDisplayName` | O nome amigável da pasta de trabalho usada na galeria ou na lista salva. Precisa ser exclusivo no escopo do grupo de recursos e da origem |
| `workbookType` | A galeria na qual a pasta de trabalho será mostrada. Os valores com suporte incluem Workbook, `tsg`, Azure Monitor, etc. |
| `workbookSourceId` | A ID da instância de recurso à qual a pasta de trabalho será associada. A nova pasta de trabalho aparecerá relacionada a essa instância de recurso, por exemplo, na tabela de conteúdo do recurso na _pasta de trabalho_. Se você quiser que sua pasta de trabalho apareça na Galeria de pastas de trabalho no Azure Monitor, use a cadeia de caracteres _Azure monitor_ em vez de uma ID de recurso. |
| `workbookId` | O GUID exclusivo para esta instância de pasta de trabalho. Use _[newGuid ()]_ para criar automaticamente um novo GUID. |
| `kind` | Usado para especificar se a pasta de trabalho criada é compartilhada ou privada. Use o valor _compartilhado_ para pastas de trabalho compartilhadas e para _usuários_ particulares. |
| `location` | O local do Azure onde a pasta de trabalho será criada. Use _[resourcegroup (). Location]_ para criá-lo no mesmo local que o grupo de recursos |
| `serializedData` | Contém o conteúdo ou a carga a ser usada na pasta de trabalho. Use o modelo do Resource Manager da interface do usuário de pastas de trabalho para obter o valor |

### <a name="workbook-types"></a>Tipos de pasta de trabalho
Tipos de pasta de trabalho especifique a Galeria da pasta de trabalho na qual a nova instância da pasta de trabalho será exibida. As opções incluem:

| Tipo | Localização da Galeria |
| :------------- |:-------------|
| `workbook` | O padrão usado na maioria dos relatórios, incluindo a Galeria de pastas de trabalho do Application Insights, Azure Monitor, etc.  |
| `tsg` | A Galeria de guias de solução de problemas no Application Insights |
| `usage` | A _mais_ galeria em _uso_ no Application insights |

### <a name="limitations"></a>Limitações
Por um motivo técnico, esse mecanismo não pode ser usado para criar instâncias da pasta de trabalho na Galeria de _pastas de trabalho_ do Application insights. Estamos trabalhando para resolver essa limitação. Enquanto isso, recomendamos que você use a Galeria de guias de solução de problemas (WorkbookType: `tsg`) para implantar Application Insights pastas de trabalho relacionadas.

## <a name="next-steps"></a>Passos seguintes

Explore como as pastas de trabalho estão sendo usadas para capacitar a nova [Azure monitor de experiência de armazenamento](../insights/storage-insights-overview.md).

