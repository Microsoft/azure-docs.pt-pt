---
title: Recue no erro para a implementação bem sucedida
description: Especifique que uma implementação falhada deve voltar para uma implementação bem sucedida.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492217"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Rollback no erro para implantação bem sucedida

Quando uma implementação falha, pode recolocar automaticamente uma implementação mais cedo e bem sucedida do seu histórico de implantação. Esta funcionalidade é útil se você tem um bom estado conhecido para a sua implementação de infraestrutura e quer reverter para este estado. Pode especificar uma determinada implantação anterior ou a última implementação bem sucedida.

> [!IMPORTANT]
> Esta funcionalidade reloque uma implementação falhada recolocando uma implementação anterior. Este resultado pode ser diferente do que seria de esperar de desfazer a implementação falhada. Certifique-se de que compreende como a implantação anterior é redistribuída.

## <a name="considerations-for-redeploying"></a>Considerações para a reafectação

Antes de utilizar esta funcionalidade, considere estes detalhes sobre como a redistribuição é tratada:

- A implementação anterior é executada utilizando o [modo completo](./deployment-modes.md#complete-mode), mesmo que tenha utilizado o [modo incremental](./deployment-modes.md#incremental-mode) durante a implementação anterior. A reefectação em modo completo pode produzir resultados inesperados quando a implementação anterior foi utilizada incrementalmente. O modo completo significa que quaisquer recursos não incluídos na implantação anterior são eliminados. Especifique uma implementação anterior que represente todos os recursos e seus estados que pretende existir no grupo de recursos. Para obter mais informações, consulte [os modos de implantação](./deployment-modes.md).
- A reafectação é executada exatamente como foi executada anteriormente com os mesmos parâmetros. Não pode mudar os parâmetros.
- A reafectação só afeta os recursos, quaisquer alterações de dados não são afetadas.
- Só pode utilizar esta funcionalidade com implementações de grupos de recursos. Não suporta subscrições, grupos de gestão ou implantações de nível de inquilino. Para obter mais informações sobre a implementação do nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição.](./deploy-to-subscription.md)
- Só pode utilizar esta opção com implementações de nível de raiz. As implementações de um modelo aninhado não estão disponíveis para redistribuição.

Para utilizar esta opção, as suas implementações devem ter nomes únicos no histórico de implementação. Só com nomes únicos é que uma implantação específica pode ser identificada. Se não tiver nomes únicos, uma implantação falhada pode substituir uma implementação bem sucedida na história.

Se especificar uma implementação anterior que não exista no histórico de implementação, o revés retorna um erro.

## <a name="powershell"></a>PowerShell

Para recolocar a última implantação bem sucedida, adicione o `-RollbackToLastDeployment` parâmetro como uma bandeira.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Para recolocar uma implantação específica, utilize o `-RollBackDeploymentName` parâmetro e forneça o nome da implantação. A implantação especificada deve ter sido bem sucedida.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>CLI do Azure

Para recolocar a última implantação bem sucedida, adicione o `--rollback-on-error` parâmetro como uma bandeira.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Para recolocar uma implantação específica, utilize o `--rollback-on-error` parâmetro e forneça o nome da implantação. A implantação especificada deve ter sido bem sucedida.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Para recolocar a última implementação bem sucedida se a implementação atual falhar, utilize:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Para recolocar uma implantação específica se a implementação atual falhar, utilize:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A implantação especificada deve ter sido bem sucedida.

## <a name="next-steps"></a>Passos seguintes

- Para compreender os modos completos e incrementais, consulte os [modos de implementação do Gestor de Recursos Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
