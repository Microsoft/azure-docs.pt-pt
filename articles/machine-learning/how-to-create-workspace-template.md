---
title: Crie um espaço de trabalho com o modelo do Gestor de Recursos Azure
titleSuffix: Azure Machine Learning
description: Aprenda a usar um modelo de Gestor de Recursos Azure para criar um novo espaço de trabalho de aprendizagem automática Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: 9df8a67fd3dfbf23986f1cc5ed18392463fc7ecb
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522211"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Use um modelo de Gestor de Recursos Azure para criar um espaço de trabalho para a aprendizagem de máquinas Azure


<br>

Neste artigo, você aprende várias maneiras de criar um espaço de trabalho Azure Machine Learning usando modelos de Gestor de Recursos Azure. Um modelo de Gestor de Recursos facilita a criação de recursos como uma única operação coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Também pode especificar parâmetros de implantação. Os parâmetros são utilizados para fornecer valores de entrada ao utilizar o gabarito.

Para obter mais informações, consulte [Implementar uma aplicação com o modelo Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Se não tiver uma, experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para utilizar um modelo a partir de um CLI, você precisa de [Azure PowerShell](/powershell/azure/) ou do [Azure CLI](/cli/azure/install-azure-cli).

* Alguns cenários exigem que abra um bilhete de apoio. Por exemplo, a utilização de um espaço de trabalho ativado por Private Link com uma chave gerida pelo cliente. Para obter mais informações, consulte [Gerir e aumentar as quotas.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

## <a name="limitations"></a>Limitações

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>Modelo de gestor de recursos de espaço de trabalho

O modelo Azure Resource Manager utilizado ao longo deste documento pode ser encontrado no diretório avançado de aprendizagem de máquinas de [201](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) dos modelos de arranque rápido do Azure GitHub.

Este modelo cria os seguintes serviços Azure:

* Conta de Armazenamento do Azure
* Azure Key Vault
* Azure Application Insights
* Registo de Contentores do Azure
* Área de trabalho do Azure Machine Learning

O grupo de recursos é o contentor que detém os serviços. Os vários serviços são requeridos pelo espaço de trabalho Azure Machine Learning.

O modelo de exemplo tem dois parâmetros **necessários:**

* O **local** onde os recursos serão criados.

    O modelo utilizará o local que seleciona para a maioria dos recursos. A exceção é o serviço Application Insights, que não está disponível em todos os locais que os outros serviços são. Se selecionar um local onde não esteja disponível, o serviço será criado na localização do Centro Sul dos EUA.

* O **espaço de trabalhoName,** que é o nome amigável do espaço de trabalho Azure Machine Learning.

    > [!NOTE]
    > O nome do espaço de trabalho é insensível a caso.

    Os nomes dos outros serviços são gerados aleatoriamente.

> [!TIP]
> Enquanto o modelo associado a este documento cria um novo registo de contentores Azure, também pode criar um novo espaço de trabalho sem criar um registo de contentores. Um deles será criado quando efetuar uma operação que requer um registo de contentores. Por exemplo, treinar ou implementar um modelo.
>
> Também pode fazer referência a um registo de contentores ou uma conta de armazenamento existente no modelo Azure Resource Manager, em vez de criar uma nova. Ao fazê-lo, deve [utilizar uma identidade gerida](how-to-use-managed-identities.md) (pré-visualização) ou [ativar a conta de administração](../container-registry/container-registry-authentication.md#admin-account) para o registo do contentor.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Para obter mais informações sobre os modelos, consulte os seguintes artigos:

* [Modelos de gestor de recursos do autor Azure](../azure-resource-manager/templates/template-syntax.md)
* [Implementar uma aplicação com modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServes de recursos](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Implementar o modelo

Para implementar o seu modelo tem de criar um grupo de recursos.

Consulte a secção [do portal Azure](#use-the-azure-portal) se preferir utilizar a interface gráfica do utilizador.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Assim que o seu grupo de recursos for criado com sucesso, implemente o modelo com o seguinte comando:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Por padrão, todos os recursos criados como parte do modelo são novos. No entanto, também tem a opção de utilizar os recursos existentes. Ao fornecer parâmetros adicionais ao modelo, pode utilizar os recursos existentes. Por exemplo, se pretender utilizar uma conta de armazenamento existente, desconte o valor **de armazenamentoAcoconse** para **o existente** e forneça o nome da sua conta de armazenamento no parâmetro **de armazenamentoAme de contagem** de armazenamento.

> [!IMPORTANT]
> Se quiser utilizar uma conta de Armazenamento Azure existente, não pode ser uma conta premium (Premium_LRS e Premium_GRS). Também não pode ter um espaço hierárquico de nomes (usado com Azure Data Lake Storage Gen2). Nem o armazenamento premium nem o espaço hierárquico são suportados com a conta de armazenamento predefinido do espaço de trabalho. Nem o armazenamento premium nem os espaços hierárquicos são suportados com a conta de armazenamento _predefinido_ do espaço de trabalho. Pode utilizar um espaço de identificação premium ou hierárquico com contas de armazenamento _não padrão._

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Implementar um espaço de trabalho encriptado

O modelo de exemplo a seguir demonstra como criar um espaço de trabalho com três configurações:

* Permitir configurações de alta confidencialidade para o espaço de trabalho. Isto cria uma nova instância cosmos DB.
* Ativar a encriptação para o espaço de trabalho.
* Utiliza um cofre de chaves Azure existente para recuperar chaves geridas pelo cliente. As chaves geridas pelo cliente são usadas para criar uma nova instância Cosmos DB para o espaço de trabalho.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> Uma vez criado um espaço de trabalho, não é possível alterar as definições para dados confidenciais, encriptação, ID do cofre de chaves ou identificadores chave. Para alterar estes valores, é necessário criar um novo espaço de trabalho utilizando os novos valores.

Para obter mais informações, consulte [a Encriptação em repouso](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]
> Existem alguns requisitos específicos que a sua subscrição deve satisfazer antes de usar este modelo:
> * Deve ter um Cofre de Chave Azure existente que contenha uma chave de encriptação.
> * O Azure Key Vault deve estar na mesma região onde planeia criar o espaço de trabalho Azure Machine Learning.
> * Tem de especificar o ID do Cofre da Chave Azure e o URI da chave de encriptação.

__Para obter os valores__ para o `cmk_keyvault` (ID do Cofre chave) e os `resource_cmk_uri` parâmetros (chave URI) necessários por este modelo, utilize os seguintes passos:    

1. Para obter o ID do cofre de chaves, utilize o seguinte comando:  

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    Este comando devolve um valor semelhante a `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` .  

1. Para obter o valor do URI para a tecla gerida pelo cliente, utilize o seguinte comando:    

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    Este comando devolve um valor semelhante a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` . 

> [!IMPORTANT]  
> Uma vez criado um espaço de trabalho, não é possível alterar as definições para dados confidenciais, encriptação, ID do cofre de chaves ou identificadores chave. Para alterar estes valores, é necessário criar um novo espaço de trabalho utilizando os novos valores.

Para ativar a utilização das chaves geridas pelo cliente, desa estale os seguintes parâmetros ao implementar o modelo:

* **encryption_status** **a Ativação.**
* **cmk_keyvault** ao valor `cmk_keyvault` obtido em etapas anteriores.
* **resource_cmk_uri** ao `resource_cmk_uri` valor obtido em etapas anteriores.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Ao utilizar uma chave gerida pelo cliente, a Azure Machine Learning cria um grupo de recursos secundários que contém a instância de DB cosmos. Para mais informações, consulte [a encriptação em repouso - Cosmos DB](concept-data-encryption.md#encryption-at-rest).

Uma configuração adicional que pode fornecer para os seus dados é definir o parâmetro **confidential_data** para **verdadeiro**. Ao fazê-lo, faz o seguinte:

* Começa a encriptar o disco de risco local para clusters de cálculo Azure Machine Learning, desde que não tenha criado nenhum cluster anterior na sua subscrição. Se criou previamente um cluster na subscrição, abra um bilhete de suporte para ter a encriptação do disco de risco ativado para os seus clusters de computação.
* Limpa o disco de risco local entre as corridas.
* Passa credenciais de segurança para a conta de armazenamento, registo de contentores e conta SSH da camada de execução para os seus clusters de computação utilizando o cofre de chaves.
* Permite a filtragem IP para garantir que os lotes subjacentes não podem ser chamados por outros serviços externos que não o AzureMachineLearningService.

    > [!IMPORTANT]
    > Uma vez criado um espaço de trabalho, não é possível alterar as definições para dados confidenciais, encriptação, ID do cofre de chaves ou identificadores chave. Para alterar estes valores, é necessário criar um novo espaço de trabalho utilizando os novos valores.

  Para obter mais informações, consulte [a encriptação em repouso.](concept-data-encryption.md#encryption-at-rest)

## <a name="deploy-workspace-behind-a-virtual-network"></a>Implementar espaço de trabalho por trás de uma rede virtual

Ao definir o valor do `vnetOption` parâmetro para qualquer um `new` `existing` ou, é capaz de criar os recursos utilizados por um espaço de trabalho por trás de uma rede virtual.

> [!IMPORTANT]
> Para o registo de contentores, apenas o sku 'Premium' é suportado.

> [!IMPORTANT]
> O Application Insights não suporta a implementação por trás de uma rede virtual.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Apenas implementar espaço de trabalho atrás do ponto final privado

Se os seus recursos associados não estiverem por trás de uma rede virtual, pode definir o parâmetro **PrivateEndpointType** para `AutoAproval` ou para `ManualApproval` implantar o espaço de trabalho por trás de um ponto final privado. Isto pode ser feito tanto para espaços de trabalho novos como existentes. Ao atualizar um espaço de trabalho existente, preencha os parâmetros do modelo com as informações do espaço de trabalho existente.

> [!IMPORTANT]
> A utilização de um espaço de trabalho de aprendizagem automática Azure com ligação privada não está disponível nas regiões do Governo Azure.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Use uma nova rede virtual

Para implementar um recurso por trás de uma nova rede virtual, defina a **vnetOption** para **nova,** juntamente com as definições de rede virtual para o respetivo recurso. A implementação abaixo mostra como implantar um espaço de trabalho com o recurso de conta de armazenamento por trás de uma nova rede virtual.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Em alternativa, pode implementar vários ou todos os recursos dependentes por detrás de uma rede virtual.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Utilize uma rede virtual existente & recursos

Para implantar um espaço de trabalho com os recursos associados existentes, tem de definir o parâmetro **vnetOption** para os parâmetros **existentes** juntamente com os parâmetros da sub-rede. No entanto, é necessário criar pontos finais de serviço na rede virtual para cada um dos recursos **antes** da implementação. Tal como acontece com novas implementações de rede virtual, pode ter um ou todos os seus recursos por trás de uma rede virtual.

> [!IMPORTANT]
> Subnet deve ter `Microsoft.Storage` ponto final de serviço

> [!IMPORTANT]
> As sub-redes não permitem a criação de pontos finais privados. Desative o ponto final privado para ativar a sub-rede.

1. Ativar os pontos finais do serviço para os recursos.

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Implementar o espaço de trabalho

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

1. Siga os passos na [implementação de recursos a partir do modelo personalizado.](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) Quando chegar ao ecrã __do modelo,__ escolha o modelo **avançado de aprendizagem de 201 máquinas** a partir do dropdown.
1. __Selecione o modelo__ para usar o modelo. Forneça as seguintes informações necessárias e quaisquer outros parâmetros dependendo do seu cenário de implantação.

   * Subscrição: Selecione a subscrição Azure para utilizar para estes recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Região: Selecione a região de Azure onde serão criados os recursos.
   * Nome do espaço de trabalho: O nome a usar para o espaço de trabalho Azure Machine Learning que será criado. O nome do espaço de trabalho deve ter entre 3 e 33 caracteres. Só pode conter caracteres alfanuméricos e "-".
   * Localização: Selecione o local onde os recursos serão criados.
1. Selecione __Rever + criar__.
1. No __Review + criar__ ecrã, concordar com os termos e condições listados e selecionar __Criar.__

Para obter mais informações, consulte [implementar recursos a partir do modelo personalizado.](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Política de acesso a Azure Key Vault e modelos de Gestor de Recursos Azure

Quando utilizar um modelo de Gestor de Recursos Azure para criar o espaço de trabalho e os recursos associados (incluindo o Cofre da Chave Azure), várias vezes. Por exemplo, utilizar o modelo várias vezes com os mesmos parâmetros como parte de um gasoduto de integração e implantação contínua.

A maioria das operações de criação de recursos através de modelos são idempotentes, mas o Key Vault limpa as políticas de acesso cada vez que o modelo é usado. Limpar as políticas de acesso quebra o acesso ao Cofre-Chave para qualquer espaço de trabalho existente que o utilize. Por exemplo, parar/criar funcionalidades de Azure Notebooks VM podem falhar.  

Para evitar este problema, recomendamos uma das seguintes abordagens:

* Não desloque o modelo mais de uma vez para os mesmos parâmetros. Ou elimine os recursos existentes antes de usar o modelo para recriá-los.

* Examine as políticas de acesso ao Cofre chave e, em seguida, use estas políticas para definir a `accessPolicies` propriedade do modelo. Para ver as políticas de acesso, utilize o seguinte comando Azure CLI:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Para obter mais informações sobre a `accessPolicies` utilização da secção do modelo, consulte a referência do [objeto AccessPolicyEntry](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

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

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Este comando devolve um valor semelhante ao seguinte texto:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>Rede virtual não ligada à zona privada de DNS

Ao criar um espaço de trabalho com um ponto final privado, o modelo cria uma Zona Privada de DNS chamada __privatelink.api.azureml.ms__. Uma __ligação de rede virtual__ é automaticamente adicionada a esta zona privada de DNS. O link é adicionado apenas para o primeiro espaço de trabalho e ponto final privado que você cria em um grupo de recursos; se criar outra rede virtual e espaço de trabalho com um ponto final privado no mesmo grupo de recursos, a segunda rede virtual pode não ser adicionada à zona privada de DNS.

Para ver as ligações de rede virtual que já existem para a zona privada de DNS, utilize o seguinte comando Azure CLI:

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

Para adicionar a rede virtual que contém outro espaço de trabalho e ponto final privado, utilize os seguintes passos:

1. Para encontrar o ID de rede virtual para a rede que pretende adicionar, utilize o seguinte comando:

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    Este comando devolve um valor semelhante a "/subscrições/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet". Guarde este valor e use-o no próximo passo.

2. Para adicionar uma ligação de rede virtual à zona privatelink.api.azureml.ms DSM, utilize o seguinte comando. Para o `--virtual-network` parâmetro, utilize a saída do comando anterior:

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>Passos seguintes

* [Implementar recursos com modelos de Gestor de Recursos e Gestor de Recursos REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Criar e implantar grupos de recursos Azure através do Visual Studio.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
* [Para outros modelos relacionados com a aprendizagem automática Azure, consulte o repositório de modelos Azure Quickstart](https://github.com/Azure/azure-quickstart-templates)