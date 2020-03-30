---
title: Recue no erro para uma implementação bem sucedida
description: Especifique que uma implantação falhada deve voltar a ser implantada com sucesso.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460148"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Retrocesso no erro para implementação bem sucedida

Quando uma implementação falha, pode recolocar automaticamente uma implementação mais precoce e bem sucedida do seu histórico de implantação. Esta funcionalidade é útil se tiver um bom estado conhecido para a sua implantação de infraestruturas e quiser voltar a este estado. Há uma série de ressalvas e restrições:

- A recolocação é executada exatamente como foi executada anteriormente com os mesmos parâmetros. Não pode mudar os parâmetros.
- A implementação anterior é executada utilizando o [modo completo](./deployment-modes.md#complete-mode). Quaisquer recursos não incluídos na implementação anterior são eliminados, e quaisquer configurações de recursos são definidas para o seu estado anterior. Certifique-se de que compreende completamente os modos de [implantação](./deployment-modes.md).
- A redistribuição apenas afeta os recursos, quaisquer alterações de dados não são afetadas.
- Só pode utilizar esta funcionalidade com implementações de grupos de recursos, não com implementações de nível de grupo de subscrição ou de gestão. Para obter mais informações sobre a implementação do nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](./deploy-to-subscription.md).
- Só pode utilizar esta opção com implementações de nível raiz. As implementações de um modelo aninhado não estão disponíveis para reafectação.

Para utilizar esta opção, as suas implementações devem ter nomes únicos para que possam ser identificados na história. Se não tiver nomes únicos, a atual implementação falhada poderá substituir a implantação anteriormente bem sucedida na história.

## <a name="powershell"></a>PowerShell

Para recolocar a última implantação bem sucedida, adicione o `-RollbackToLastDeployment` parâmetro como uma bandeira.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Para reimplantar uma implantação `-RollBackDeploymentName` específica, utilize o parâmetro e forneça o nome da implantação. A implantação especificada deve ter sido bem sucedida.

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

Para reimplantar uma implantação `--rollback-on-error` específica, utilize o parâmetro e forneça o nome da implantação. A implantação especificada deve ter sido bem sucedida.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Para reimplantar a última implementação bem sucedida se a implementação atual falhar, utilize:

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

Para reimplantar uma implementação específica se a implementação atual falhar, utilize:

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

- Para lançar com segurança o seu serviço para mais de uma região, consulte o Gestor de [Destacamento seleções do Azure.](deployment-manager-overview.md)
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os modos de implementação do Gestor de [Recursos Do Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](template-syntax.md)de Recursos Azure .
- Para obter informações sobre a implementação de um modelo que requer uma ficha SAS, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md).
