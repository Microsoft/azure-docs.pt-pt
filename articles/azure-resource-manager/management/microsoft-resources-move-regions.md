---
title: Mover regiões para recursos em Microsoft.Resources
description: Mostrar como mover recursos que estão no espaço de nomes microsoft.Resources para novas regiões.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88951057"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Mover recursos da Microsoft.Resources para uma nova região

Talvez seja necessário mover um recurso existente para uma nova região. Este artigo mostra como mover dois tipos de recursos - modeloSpecs e implementaçõesScripts - que estão no espaço de nomes Microsoft.Resources.

## <a name="move-template-specs-to-new-region"></a>Mova as especificações do modelo para a nova região

Se você tem uma [especificação de modelo](../templates/template-specs.md) em uma região e quer movê-lo para uma nova região, você pode exportar a especificação do modelo e reimplantá-lo.

1. Utilize o comando para exportar uma especificação de modelo existente. Para os valores dos parâmetros, forneça os valores que correspondam à especificação do modelo que pretende exportar.

   Para a Azure PowerShell, utilize:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Para a CLI do Azure, utilize:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Use a especificação do modelo exportado para criar uma nova especificação de modelo. Os exemplos que se seguem mostram `westus` para a nova região, mas pode fornecer a região que deseja.

   Para a Azure PowerShell, utilize:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Para a CLI do Azure, utilize:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Mover scripts de implantação para nova região

1. Selecione o grupo de recursos que contém o script de implementação que pretende mover-se para uma nova região.

1. [Exporte o modelo.](../templates/export-template-portal.md) Ao exportar, selecione o script de implementação e quaisquer outros recursos necessários.

1. No modelo exportado, elimine as seguintes propriedades:

   * inquilinoId
   * principalId
   * clientId

1. O modelo exportado tem um valor codificado para a região do script de implantação.

   ```json
   "location": "westus2",
   ```

   Altere o modelo para permitir um parâmetro para definir a localização. Para obter mais informações, consulte [a localização do recurso definido no modelo ARM](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Implemente o modelo exportado](../templates/deploy-powershell.md) e especifique uma nova região para o script de implementação.

## <a name="next-steps"></a>Passos seguintes

* Para saber sobre a mudança de recursos para um novo grupo de recursos ou subscrição, consulte [os recursos da Move para um novo grupo de recursos ou subscrição.](move-resource-group-and-subscription.md)
* Para aprender a transferir recursos para uma nova região, consulte [a moving Azure resources across regions.](move-region.md)
