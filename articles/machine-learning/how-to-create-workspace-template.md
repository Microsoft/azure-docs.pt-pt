---
title: Criar um espaço de trabalho com Azure Resource Manager modelo
titleSuffix: Azure Machine Learning
description: Saiba como usar um modelo de Azure Resource Manager para criar um novo espaço de trabalho Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 275eb545b431085627658eb5d8ac0a065d0cb00e
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867019"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Use um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho do Azure Machine Learning usando modelos de Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma única operação coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Ele também pode especificar parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para obter mais informações, consulte [implantar um aplicativo com Azure Resource Manager modelo](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se você não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo de uma CLI, você precisa de [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O seguinte modelo do Resource Manager pode ser usado para criar um espaço de trabalho Azure Machine Learning e recursos do Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Este modelo cria os seguintes serviços do Azure:

* Grupo de Recursos do Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contêiner que mantém os serviços. Os vários serviços são exigidos pelo espaço de trabalho Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* O **local** onde o grupo de recursos e os serviços serão criados.

    O modelo usará o local que você selecionar para a maioria dos recursos. A exceção é o serviço Application Insights, que não está disponível em todos os locais em que os outros serviços estão. Se você selecionar um local onde ele não está disponível, o serviço será criado no local do EUA Central do Sul.

* O **nome do espaço de trabalho**, que é o nome amigável do espaço de trabalho Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Embora o modelo associado a este documento crie um novo registro de contêiner do Azure, você também pode criar um novo espaço de trabalho sem criar um registro de contêiner. Se no registro de contêiner estiver presente no espaço de trabalho, um será criado quando você executar uma operação que requer um registro de contêiner. Por exemplo, treinar ou implantar um modelo.
>
> Você também pode fazer referência a um registro de contêiner ou conta de armazenamento existente no modelo Azure Resource Manager, em vez de criar um novo.

Para obter mais informações sobre modelos, consulte os seguintes artigos:

* [Criar modelos de Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Implantar um aplicativo com modelos de Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Tipos de recurso Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Utilizar o Portal do Azure

1. Siga as etapas em [implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando você chegar à tela __Editar modelo__ , Cole o modelo deste documento.
1. Selecione __salvar__ para usar o modelo. Forneça as seguintes informações e concorde com os termos e condições listados:

   * Assinatura: selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do espaço de trabalho: o nome a ser usado para o espaço de trabalho Azure Machine Learning que será criado. O nome do espaço de trabalho deve ter entre 3 e 33 caracteres. Ele pode conter apenas caracteres alfanuméricos e '-'.
   * Local: selecione o local onde os recursos serão criados.

Para obter mais informações, consulte [implantar recursos do modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Este exemplo pressupõe que você salvou o modelo em um arquivo chamado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [implantar o modelo do Resource Manager privado com o token SAS e Azure PowerShell](../azure-resource-manager/secure-template-with-sas-token.md).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Este exemplo pressupõe que você salvou o modelo em um arquivo chamado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Para obter mais informações, consulte [implantar recursos com modelos do Resource Manager e CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar o modelo do Resource Manager privado com o token SAS e CLI do Azure](../azure-resource-manager/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso de Azure Key Vault e modelos de Azure Resource Manager

Quando você usa um modelo de Azure Resource Manager para criar o espaço de trabalho e os recursos associados (incluindo Azure Key Vault), várias vezes. Por exemplo, usando o modelo várias vezes com os mesmos parâmetros como parte de um pipeline de implantação e integração contínua.

A maioria das operações de criação de recursos por meio de modelos é idempotente, mas Key Vault limpa as políticas de acesso toda vez que o modelo é usado. Limpar as políticas de acesso interrompe o acesso ao Key Vault para qualquer espaço de trabalho existente que o esteja usando. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM pode falhar.  

Para evitar esse problema, recomendamos uma das seguintes abordagens:

* Não implante o modelo mais de uma vez para os mesmos parâmetros. Ou exclua os recursos existentes antes de usar o modelo para recriá-los.
  
* Examine as políticas de acesso do Key Vault e use essas políticas para definir a propriedade `accessPolicies` do modelo. Para exibir as políticas de acesso, use o seguinte comando de CLI do Azure:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Para obter mais informações sobre como usar a seção `accessPolicies` do modelo, consulte a [referência do objeto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Verifique se o recurso de Key Vault já existe. Se tiver, não a recrie por meio do modelo. Por exemplo, para usar o Key Vault existente em vez de criar um novo, faça as seguintes alterações no modelo:

    * **Adicione** um parâmetro que aceite a ID de um recurso de Key Vault existente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Remova** a seção que cria um recurso de Key Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```
    
    * **Remova** a linha de `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` da seção `dependsOn` do espaço de trabalho. Além disso, **altere** a entrada `keyVault` na seção `properties` do espaço de trabalho para fazer referência ao parâmetro `keyVaultId`:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```
      
    Após essas alterações, você pode especificar a ID do recurso de Key Vault existente ao executar o modelo. O modelo, em seguida, reutilizará o Key Vault definindo a propriedade `keyVault` do espaço de trabalho como sua ID.

    Para obter a ID do Key Vault, você pode fazer referência à saída da execução do modelo original ou usar o CLI do Azure. O comando a seguir é um exemplo de como usar o CLI do Azure para obter a ID de recurso de Key Vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Esse comando retorna um valor semelhante ao seguinte texto:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Passos seguintes

* [Implante recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
