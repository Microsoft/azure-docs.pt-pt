---
title: Crie um cluster de tecido de serviço usando o modelo de gestor de recursos Azure
description: Neste arranque rápido, irá criar um cluster de testes de tecido de serviço Azure utilizando o modelo Azure Resource Manager.
author: erikadoyle
ms.author: edoyle
ms.date: 07/29/2020
ms.topic: quickstart
ms.service: service-fabric
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: cb3758ae837dc03789371d00f768b257a9ef295d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535311"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Quickstart: Criar um cluster de tecido de serviço usando o modelo ARM

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços e contentores dimensionáveis e fiáveis. Um *cluster* de Tecido de Serviço é um conjunto de máquinas virtuais ligadas à rede em que os seus microserviços são implantados e geridos. Este artigo descreve como implantar um cluster de teste de tecido de serviço em Azure usando um modelo de Gestor de Recursos Azure (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Este cluster windows de cinco nó é protegido com um certificado auto-assinado e, portanto, apenas destinado a fins instrutivos (em vez de cargas de trabalho de produção). Usaremos a Azure PowerShell para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Instalar módulos SDK e PowerShell de tecido de serviço

Para completar este arranque rápido, terá de:

* Instale o [módulo SDK e PowerShell do tecido de serviço](service-fabric-get-started.md).

* Instalar [a Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Descarregue o modelo de amostra e o script do ajudante de certificado

Clone ou descarregue o [quickstart Templates do Azure Resource](https://github.com/Azure/azure-quickstart-templates) Manager. Alternativamente, copie localmente os seguintes ficheiros que usaremos a partir da pasta *de tipo de nó-cluster-5-nó-1 de serviço* de serviço:

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.js](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no Azure e designe a subscrição para a criação do seu cluster de Tecido de Serviço.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Criar um certificado auto-assinado armazenado em Key Vault

A Service Fabric utiliza certificados X.509 para [garantir um cluster](./service-fabric-cluster-security.md) e fornecer funcionalidades de segurança de aplicações, e o Key [Vault](../key-vault/general/overview.md) para gerir esses certificados. A criação bem sucedida do cluster requer um certificado de cluster para permitir a comunicação nó-a-nó. Com o propósito de criar este cluster de teste de arranque rápido, vamos criar um certificado auto-assinado para autenticação de cluster. As cargas de trabalho de produção requerem certificados criados utilizando um serviço de certificados do Windows Server corretamente configurado ou um de uma autoridade de certificados aprovada (CA).

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\scripts\New-ServiceFabricClusterCertificate.ps1
```

O script irá instruí-lo para o seguinte (certifique-se de modificar *CertDNSName* e *KeyVaultName* a partir dos valores de exemplo abaixo):

* **Senha:** Senha!1
* **CertDNSName:** *sfquickstart*.southcentralus.cloudapp.azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Após a conclusão, o script fornecerá os valores de parâmetro necessários para a implementação do modelo. Certifique-se de que as armazena nas seguintes variáveis, pois serão necessárias para implementar o seu modelo de cluster:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). O modelo para este artigo é muito longo para mostrar aqui. Para ver o modelo, consulte a [azuredeploy.jsno](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json) ficheiro.

Vários recursos Azure foram definidos no modelo:

* [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Para encontrar mais modelos relacionados com o Tecido de Serviço Azure, consulte [os modelos de arranque rápido do Azure.](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric)

### <a name="customize-the-parameters-file"></a>Personalize o ficheiro de parâmetros

Abra *azuredeploy.parameters.js* e edite os valores dos parâmetros de modo a que:

* **clusterName** corresponde ao valor que forneceu para *o CertDNSName* ao criar o seu certificado de cluster
* **adminUserName** é algum valor que não seja o token *GEN-UNIQUE* padrão
* **adminPassword** é algum valor que não seja o token *GEN-PASSWORD* padrão
* **certificateThumbprint**, **sourceVaultResourceId,** e **certificateUrlValue** são todos cordas vazias ( `""` )

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

Guarde os caminhos do seu modelo ARM e dos ficheiros de parâmetros em variáveis e, em seguida, desloque o modelo.

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

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Uma vez concluída a implementação, encontre o `managementEndpoint` valor na saída e abra o endereço num navegador web para ver o seu cluster no Service Fabric [Explorer](./service-fabric-visualizing-your-cluster.md).

![Explorador de tecido de serviço mostrando novo cluster](./media/quickstart-cluster-template/service-fabric-explorer.png)

Também pode encontrar o ponto final do Service Fabric Explorer a partir da sua lâmina de recursos Do Explorador de Serviço no portal Azure.

![Lâmina de recurso de tecido de serviço mostrando ponto final do Explorador de tecido de serviço](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Em seguida, retire o certificado de cluster da sua loja local. Lista certificados instalados para encontrar a impressão digital para o seu cluster:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Em seguida, retire o certificado:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre a criação de um modelo de cluster de tecido de serviço Azure personalizado, consulte:

> [!div class="nextstepaction"]
> [Crie um modelo de gestor de recursos de cluster de tecido de serviço](service-fabric-cluster-creation-create-template.md)
