---
title: Recue no erro para a implementação bem sucedida
description: Especifique que uma implementação falhada deve voltar para uma implementação bem sucedida.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79460148"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Rollback no erro para implantação bem sucedida

Quando uma implementação falha, pode recolocar automaticamente uma implementação mais cedo e bem sucedida do seu histórico de implantação. Esta funcionalidade é útil se você tem um bom estado conhecido para a sua implementação de infraestrutura e quer reverter para este estado. Há uma série de ressalvas e restrições:

- A reafectação é executada exatamente como foi executada anteriormente com os mesmos parâmetros. Não pode mudar os parâmetros.
- A implementação anterior é executada utilizando o [modo completo](./deployment-modes.md#complete-mode). Quaisquer recursos não incluídos na implementação anterior são eliminados e quaisquer configurações de recursos são definidas para o seu estado anterior. Certifique-se de que compreende completamente os [modos de implantação](./deployment-modes.md).
- A reafectação só afeta os recursos, quaisquer alterações de dados não são afetadas.
- Só pode utilizar esta funcionalidade com implementações de grupos de recursos, não implementações de grupos de subscrição ou gestão. Para obter mais informações sobre a implementação do nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição.](./deploy-to-subscription.md)
- Só pode utilizar esta opção com implementações de nível de raiz. As implementações de um modelo aninhado não estão disponíveis para redistribuição.

Para utilizar esta opção, as suas implementações devem ter nomes únicos para que possam ser identificadas na história. Se não tiver nomes únicos, a implementação falhada atual pode substituir a implementação anteriormente bem sucedida na história.

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

- Para lançar o seu serviço com segurança para mais de uma região, consulte [o Gestor de Implantação do Azure](deployment-manager-overview.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os [modos de implementação do Gestor de Recursos Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos Azure Resource Manager](template-syntax.md).
- Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
