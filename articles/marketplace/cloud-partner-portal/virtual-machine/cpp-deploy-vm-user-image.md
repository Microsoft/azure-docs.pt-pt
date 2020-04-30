---
title: Implementar um VM Azure a partir de um VHD utilizador / Mercado Azure
description: Explica como implementar uma imagem VHD do utilizador para criar uma instância De VM Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: e3bad2dc63f6a75f52c537aabfa6e85d1846ef15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147923"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Implementar um VM Azure a partir de um VHD de utilizador

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções na certificação de [imagem Azure VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) para gerir as suas ofertas migratórias.

Este artigo explica como implementar uma imagem VHD generalizada para criar um novo recurso Azure VM, utilizando o modelo de Gestor de Recursos Azure fornecido e script Azure PowerShell.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Modelo de implementação VHD

Copie o modelo do Gestor de Recursos Azure `VHDtoImage.json`para a implantação de [VHD](cpp-deploy-json-template.md) para um ficheiro local chamado .  Editar este ficheiro para fornecer valores para os seguintes parâmetros. 

|  **Parâmetro**             |   **Descrição**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Nome de grupo de recursos Azure existente.  Normalmente use o mesmo RG associado ao seu cofre chave  |
| ModeloFile               | Nome completo do caminho para o ficheiro`VHDtoImage.json`                                    |
| userStorageAccountName     | Nome da conta de armazenamento                                                    |
| sNameForPublicIP           | Nome dNS para o IP público. Deve ser minúscula                                  |
| subscriptionId             | Identificador de assinatura Azure                                                  |
| Localização                   | Localização geográfica Standard Azure do grupo de recursos                       |
| vmName                     | Nome da máquina virtual                                                    |
| nome do cofre                  | Nome do cofre da chave                                                          |
| vaultResourceGroup         | Grupo de recursos do cofre chave
| certificadoUrl             | Url do certificado, incluindo versão armazenada no cofre chave, por exemplo:`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL do disco rígido virtual                                                   |
| vmSize                     | Tamanho da instância da máquina virtual                                           |
| nome de endereço ipaddress público        | Nome do endereço IP público                                                  |
| virtualNetworkName         | Nome da rede virtual                                                    |
| nicName                    | Nome do cartão de interface de rede para a rede virtual                     |
| adminUserName              | Nome de utilizador da conta de administrador                                          |
| adminPassword              | Senha de administrador                                                          |
|  |  |


## <a name="powershell-script"></a>Script Powershell

Copiar e editar o seguinte script `$storageaccount` `$vhdUrl` para fornecer valores para as e variáveis.  Execute-o para criar um recurso Azure VM a partir do vHD generalizado existente.

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

Depois de implantado o VM, está pronto para certificar a [sua imagem VM](./cpp-certify-vm.md).
