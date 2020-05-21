---
title: Criar um cluster de tecido de serviço usando o modelo de Gestor de Recursos Azure
description: Neste arranque rápido, você vai criar um cluster de teste de Tecido de Serviço Azure utilizando o modelo De Gestor de Recursos Azure.
author: erikadoyle
ms.service: service-fabric
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: edoyle
ms.date: 04/24/2020
ms.openlocfilehash: 2db3dffbbf0f6d98fe6da7a0cec5400f7f2c03da
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722461"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-resource-manager-template"></a>Quickstart: Criar um cluster de tecido de serviço usando o modelo de Gestor de Recursos

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis. Um *cluster* de tecido de serviço é um conjunto de máquinas virtuais ligadas à rede nas quais os seus microserviços são implantados e geridos.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Este artigo descreve como implementar um cluster de teste de Tecido de Serviço em Azure usando o Gestor de Recursos. Este cluster Windows de cinco nós é protegido com um certificado auto-assinado e, portanto, destina-se apenas a fins instrutivos (em vez de cargas de trabalho de produção).

Usaremos o Azure PowerShell para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure, O ClI Azur e a Rest API. Para aprender outros métodos de implementação, consulte [os modelos de implantação](../azure-resource-manager/templates/deploy-portal.md).

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Instale módulos SDK e PowerShell de tecido de serviço

Para completar este arranque rápido, terá de:

* Instale o [módulo SDK e PowerShell](service-fabric-get-started.md)de tecido de serviço.

* Instale [o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Descarregue o modelo de amostra e o script de ajudante de certificado

Clone ou baixe o [quickstart Templates do Gestor de Recursos Azure.](https://github.com/Azure/azure-quickstart-templates) Alternativamente, copie localmente os seguintes ficheiros que iremos utilizar a partir da pasta de *cluster-5-nó-nó-de-tecido* de serviço:

* [Novo ServiçoFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure e designe a subscrição a utilizar para criar o seu cluster Service Fabric.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Crie um certificado auto-assinado armazenado no Cofre chave

Service Fabric utiliza certificados X.509 para [garantir um cluster](./service-fabric-cluster-security.md) e fornecer funcionalidades de segurança de aplicações, e Key [Vault](../key-vault/general/overview.md) para gerir esses certificados. A criação bem sucedida do cluster requer um certificado de cluster para permitir a comunicação nó-ao-nó. Com o propósito de criar este cluster de teste de arranque rápido, criaremos um certificado auto-assinado para autenticação de cluster. As cargas de trabalho de produção requerem certificados criados utilizando um serviço de certificado supor o Windows Server corretamente configurado ou um de uma autoridade de certificados aprovado (CA).

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\New-ServiceFabricClusterCertificate.ps1
```

O script irá instruí-lo para o seguinte (certifique-se de modificar *CertDNSName* e *KeyVaultName* dos valores de exemplo abaixo):

* **Senha:** Senha!1
* **CertDNSName:** *sfquickstart*.southcentralus.cloudapp.azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Após a conclusão, o script fornecerá os valores parâmetros necessários para a implementação do modelo. Certifique-se de guardá-las nas seguintes variáveis, pois serão necessárias para implementar o seu modelo de cluster:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). O modelo para este artigo é muito longo para mostrar aqui. Para ver o modelo, consulte o ficheiro [azuredeploy.json.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)

Vários recursos Azure foram definidos no modelo:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresss](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Para encontrar mais modelos relacionados com tecido de serviço Azure, consulte [os modelos de arranque rápido do Azure](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric).

### <a name="customize-the-parameters-file"></a>Personalize o ficheiro de parâmetros

Abra *azuredeploy.parameters.json* e edite os valores do parâmetro de modo a:

* **clusterName** corresponde ao valor fornecido para *CertDNSName* ao criar o seu certificado de cluster
* **adminUserName** é um valor diferente do *token GEN-UNIQUE* padrão
* **adminPassword** é um valor diferente do token *GEN-PASSWORD* padrão
* **certificadoImpressãoPolegar,** **fonteVaultResourceId,** e **certificadoUrlValue** são todos de cadeia vazia ( `""` )

Por exemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>Implementar o modelo

Guarde os caminhos do seu modelo de Gestor de Recursos e ficheiros de parâmetros em variáveis e, em seguida, implemente o modelo.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Assim que a implementação estiver concluída, encontre o `managementEndpoint` valor na saída e abra o endereço num navegador web para ver o seu cluster no Service Fabric [Explorer](./service-fabric-visualizing-your-cluster.md).

![Service Fabric Explorer mostrando novo cluster](./media/quickstart-cluster-template/service-fabric-explorer.png)

Também pode encontrar o ponto final do Service Fabric Explorer a partir da sua lâmina de recurso Service Explorer no portal Azure.

![Lâmina de recurso de tecido de serviço mostrando ponto final do Explorador de Tecido de Serviço](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Próximos passos

Para aprender sobre a criação de um modelo personalizado de cluster azure service fabric, consulte:

> [!div class="nextstepaction"]
> [Criar um modelo de gestor de recursos de cluster de tecido de serviço](service-fabric-cluster-creation-create-template.md)
