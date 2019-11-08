---
title: Implantar uma VM do Azure de um VHD de usuário | Azure Marketplace
description: Explica como implantar uma imagem de VHD de usuário para criar uma instância de VM do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 8421e9b7b7e2b7d13054e977da83be044b4e6af7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816632"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Implantar uma VM do Azure de um VHD de usuário

Este artigo explica como implantar uma imagem de VHD generalizada para criar um novo recurso de VM do Azure, usando o modelo de Azure Resource Manager fornecido e Azure PowerShell script.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Modelo de implantação VHD

Copie o modelo de Azure Resource Manager para [implantação de VHD](cpp-deploy-json-template.md) em um arquivo local chamado `VHDtoImage.json`.  Edite esse arquivo para fornecer valores para os parâmetros a seguir. 

|  **Meter**             |   **Descrição**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Nome do grupo de recursos do Azure existente.  Normalmente, use o mesmo RG associado ao seu cofre de chaves  |
| TemplateFile               | Nome de caminho completo para o arquivo `VHDtoImage.json`                                    |
| userStorageAccountName     | Nome da conta de armazenamento                                                    |
| sNameForPublicIP           | Nome DNS para o IP público. Deve estar em minúsculas                                  |
| subscriptionId             | Identificador de assinatura do Azure                                                  |
| Localização                   | Localização geográfica padrão do Azure do grupo de recursos                       |
| vmName                     | Nome da máquina virtual                                                    |
| vaultName                  | Nome do cofre de chaves                                                          |
| vaultResourceGroup         | Grupo de recursos do cofre de chaves
| certificateUrl             | URL do certificado, incluindo a versão armazenada no cofre de chaves, por exemplo: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL do disco rígido virtual                                                   |
| vmSize                     | Tamanho da instância de máquina virtual                                           |
| publicIPAddressName        | Nome do endereço IP público                                                  |
| virtualNetworkName         | Nome da rede virtual                                                    |
| nicName                    | Nome da placa de interface de rede para a rede virtual                     |
| adminUserName              | Nome de usuário da conta de administrador                                          |
| adminPassword              | Senha do administrador                                                          |
|  |  |


## <a name="powershell-script"></a>Script do PowerShell

Copie e edite o script a seguir para fornecer valores para as variáveis `$storageaccount` e `$vhdUrl`.  Execute-o para criar um recurso de VM do Azure de seu VHD generalizado existente.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Passos seguintes

Depois que a VM for implantada, você estará pronto para [certificar sua imagem de VM](./cpp-certify-vm.md).
