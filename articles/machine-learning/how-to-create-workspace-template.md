---
title: Crie um espaço de trabalho com o modelo do Gestor de Recursos Azure
titleSuffix: Azure Machine Learning
description: Aprenda a usar um modelo de Gestor de Recursos Azure para criar um novo espaço de trabalho de aprendizagem automática Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: 39c694f4e2afbf5d781a8fde43a7db9c4a255466
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392676"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho Azure Machine Learning usando modelos de Gestor de Recursos Azure. Um modelo de Gestor de Recursos facilita a criação de recursos como uma única operação coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Também pode especificar parâmetros de implantação. Os parâmetros são utilizados para fornecer valores de entrada ao utilizar o gabarito.

Para obter mais informações, consulte [Implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura Azure**. Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para utilizar um modelo a partir de um CLI, você precisa de [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O modelo seguinte do Gestor de Recursos pode ser usado para criar um espaço de trabalho de aprendizagem de máquinas Azure e recursos Azure associados:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Este modelo cria os seguintes serviços Azure:

* Grupo de Recursos Azure
* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Registo de Contentores do Azure
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contentor que detém os serviços. Os vários serviços são requeridos pelo espaço de trabalho Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* O **local** onde o grupo de recursos e os serviços serão criados.

    O modelo utilizará o local que seleciona para a maioria dos recursos. A exceção é o serviço Application Insights, que não está disponível em todos os locais que os outros serviços são. Se selecionar um local onde não esteja disponível, o serviço será criado na localização do Centro Sul dos EUA.

* O **nome do espaço de trabalho,** que é o nome amigável do espaço de trabalho Azure Machine Learning.

    > [!NOTE]
    > O nome do espaço de trabalho é insensível a caso.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Enquanto o modelo associado a este documento cria um novo registo de contentores Azure, também pode criar um novo espaço de trabalho sem criar um registo de contentores. Um deles será criado quando efetuar uma operação que requer um registo de contentores. Por exemplo, treinar ou implementar um modelo.
>
> Também pode fazer referência a um registo de contentores ou uma conta de armazenamento existente no modelo Azure Resource Manager, em vez de criar uma nova. No entanto, o registo do contentor que utiliza deve ter a __conta de administração__ ativada. Para obter informações sobre a habilitação da conta de administração, consulte [a conta Admin](/azure/container-registry/container-registry-authentication#admin-account).

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Para obter mais informações sobre os modelos, consulte os seguintes artigos:

* [Modelos de gestor de recursos do autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implementar uma aplicação com modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServes de recursos](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Modelo avançado

O modelo de exemplo a seguir demonstra como criar um espaço de trabalho com três configurações:

* Permitir configurações de alta confidencialidade para o espaço de trabalho
* Ativar a encriptação para o espaço de trabalho
* Usa um cofre de chaves Azure existente para recuperar chaves geridas pelo cliente

Para obter mais informações, consulte [a Encriptação em repouso](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Existem alguns requisitos específicos que a sua subscrição deve satisfazer antes de usar este modelo:
> * A aplicação __Azure Machine Learning__ deve ser um __contribuinte__ para a sua subscrição Azure.
> * Deve ter um Cofre de Chave Azure existente que contenha uma chave de encriptação.
> * Você deve ter uma política de acesso no Azure Key Vault que concede __obter,__ __embrulhar,__ e __desembrulhar__ o acesso à aplicação __DB Azure Cosmos.__
> * O Azure Key Vault deve estar na mesma região onde planeia criar o espaço de trabalho Azure Machine Learning.

__Para adicionar a app Azure Machine Learning como contribuinte,__ utilize os seguintes comandos:

1. Para autenticar a Azure a partir do CLI, utilize o seguinte comando:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Para obter o ID do objeto da aplicação Azure Machine Learning, utilize o seguinte comando. O valor pode ser diferente para cada uma das suas subscrições Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Este comando devolve o ID do objeto, que é um GUID.

1. Para adicionar o ID do objeto como contribuinte à sua subscrição, utilize o seguinte comando. `<object-ID>`Substitua-o pelo GUID do passo anterior. Substitua `<subscription-ID>` pelo nome ou ID da sua subscrição Azure:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Para adicionar uma chave ao seu Cofre de Chave Azure,__ utilize as informações na [Adição de uma chave, segredo ou certificado à](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) secção do cofre de chaves do Cofre de Chaves de Gestão utilizando o artigo __Azure CLI.__

__Para adicionar uma política de acesso ao cofre de chaves, utilize os seguintes comandos:__

1. Para obter o iD do objeto da aplicação DB Azure Cosmos, utilize o seguinte comando. O valor pode ser diferente para cada uma das suas subscrições Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Este comando devolve o ID do objeto, que é um GUID.

1. Para definir a política, utilize o seguinte comando. `<keyvault-name>`Substitua-o pelo nome do cofre de chave Azure existente. `<object-ID>`Substitua-a pelo GUID do passo anterior:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Para obter os valores__ para o `cmk_keyvault` (ID do Cofre chave) e os `resource_cmk_uri` parâmetros (chave URI) necessários por este modelo, utilize os seguintes passos:

1. Para obter o ID do cofre de chaves, utilize o seguinte comando:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Este comando devolve um valor semelhante a `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault` .

1. Para obter o valor do URI para a tecla gerida pelo cliente, utilize o seguinte comando:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Este comando devolve um valor semelhante a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` .

__Modelo de exemplo__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Uma vez criado um espaço de trabalho, não é possível alterar as definições para dados confidenciais, encriptação, ID do cofre de chaves ou identificadores chave. Para alterar estes valores, é necessário criar um novo espaço de trabalho utilizando os novos valores.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Siga os passos na [implementação de recursos a partir do modelo personalizado.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template) Quando chegar ao ecrã do __modelo editar,__ cole o modelo a partir deste documento.
1. __Selecione Guardar__ para usar o modelo. Forneça as seguintes informações e concorde com os termos e condições listados:

   * Subscrição: Selecione a subscrição Azure para utilizar para estes recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do espaço de trabalho: O nome a usar para o espaço de trabalho Azure Machine Learning que será criado. O nome do espaço de trabalho deve ter entre 3 e 33 caracteres. Só pode conter caracteres alfanuméricos e "-".
   * Localização: Selecione o local onde os recursos serão criados.

Para obter mais informações, consulte [implementar recursos a partir do modelo personalizado.](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Este exemplo pressupõe que guardou o modelo para um ficheiro nomeado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [implementar o modelo de Gestor de Recursos Privados com token SAS e Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Este exemplo pressupõe que guardou o modelo para um ficheiro nomeado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../azure-resource-manager/templates/deploy-cli.md) e [implementar o modelo de Gestor de Recursos Privados com token SAS e Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso a Azure Key Vault e modelos de Gestor de Recursos Azure

Quando utilizar um modelo de Gestor de Recursos Azure para criar o espaço de trabalho e os recursos associados (incluindo o Cofre da Chave Azure), várias vezes. Por exemplo, utilizar o modelo várias vezes com os mesmos parâmetros como parte de um gasoduto de integração e implantação contínua.

A maioria das operações de criação de recursos através de modelos são idempotentes, mas o Key Vault limpa as políticas de acesso cada vez que o modelo é usado. Limpar as políticas de acesso quebra o acesso ao Cofre-Chave para qualquer espaço de trabalho existente que o utilize. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM podem falhar.  

Para evitar este problema, recomendamos uma das seguintes abordagens:

* Não desloque o modelo mais de uma vez para os mesmos parâmetros. Ou elimine os recursos existentes antes de usar o modelo para recriá-los.

* Examine as políticas de acesso ao Cofre chave e, em seguida, use estas políticas para definir a `accessPolicies` propriedade do modelo. Para ver as políticas de acesso, utilize o seguinte comando Azure CLI:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Para obter mais informações sobre a `accessPolicies` utilização da secção do modelo, consulte a referência do [objeto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Verifique se o recurso Key Vault já existe. Se o fizer, não o recrie através do modelo. Por exemplo, para utilizar o Cofre de Chaves existente em vez de criar um novo, faça as seguintes alterações ao modelo:

    * **Adicione** um parâmetro que aceite o ID de um recurso de Cofre de Chaves existente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Remova** a secção que cria um recurso Key Vault:

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

    * **Retire** a `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` linha da secção do espaço de `dependsOn` trabalho. **Altere** também a entrada na secção do espaço de trabalho para fazer referência ao `keyVault` `properties` `keyVaultId` parâmetro:

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

    Após estas alterações, pode especificar o ID do recurso de cofre de teclas existente ao executar o modelo. O modelo reutilizará então o Cofre de Chaves definindo a `keyVault` propriedade do espaço de trabalho para o seu ID.

    Para obter o ID do Cofre de Chaves, pode referenciar a saída do modelo original ou utilizar o Azure CLI. O seguinte comando é um exemplo de utilização do CLI Azure para obter o ID do recurso Key Vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Passos seguintes

* [Implementar recursos com modelos de Gestor de Recursos e Gestor de Recursos REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Criar e implantar grupos de recursos Azure através do Visual Studio.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
