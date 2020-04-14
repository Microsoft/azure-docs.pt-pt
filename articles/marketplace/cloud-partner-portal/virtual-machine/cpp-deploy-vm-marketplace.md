---
title: Implementar um VM do Azure Marketplace
description: Explica como implantar uma máquina virtual a partir de uma máquina virtual pré-configurada do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6e39f1c70cd94c14b12e54817941ea9106aacfdd
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273873"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Implementar uma máquina virtual do Azure Marketplace

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções na certificação de [imagem Azure VM](https://aks.ms/CertifyVMimage) para gerir as suas ofertas migratórias.

Este artigo explica como implementar uma máquina virtual pré-configurada (VM) a partir de um Azure Marketplace, utilizando o script Azure PowerShell fornecido.  Este script também expõe os pontos finais WinRM HTTP e HTTPS no VM.  O script requer que já tenha um certificado enviado para o Cofre de Chaves Azure, como descrito nos [certificados Create para Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Modelo de implementação vm

O modelo de implantação quickstart Azure VM, está disponível como o ficheiro online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Contém os seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | Nome da conta de armazenamento                       |
| dnsNameForPublicIP    | Nome DNS para o IP público. Deve ser minúscula.    |
| adminUserName            | Nome de utilizador do administrador                          |
| adminPassword            | Senha do administrador                          |
| imagemEditora        | Editor de imagem                                   |
| imagemOffer            | Oferta de imagem                                       |
| imagensSKU                | Imagem SKU                                         |
| vmSize                | Tamanho do VM                                    |
| vmName                | Nome do VM                                    |
| nome do cofre                | Nome do cofre da chave                             |
| vaultResourceGroup    | Grupo de recursos do cofre chave                   |
| certificadoUrl        | URL para o certificado, incluindo versão no KeyVault, por exemplo`https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Script de implementação

Editar o seguinte script Azure PowerShell e executá-lo para implementar o específico Azure Marketplace VM.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Passos seguintes

Depois de ter implementado um VM pré-configurado, pode configurar e aceder às soluções e serviços que contém, ou usá-lo para posterior desenvolvimento. 
