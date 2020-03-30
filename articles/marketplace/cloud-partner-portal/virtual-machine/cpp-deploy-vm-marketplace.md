---
title: Implementar um VM do Azure Marketplace
description: Explica como implantar uma máquina virtual a partir de uma máquina virtual pré-configurada do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 7d5269cf8865faeb65356bc8fd3eea087cb7653c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277978"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Implementar uma máquina virtual do Azure Marketplace

Este artigo explica como implementar uma máquina virtual pré-configurada (VM) a partir de um Azure Marketplace, utilizando o script Azure PowerShell fornecido.  Este script também expõe os pontos finais WinRM HTTP e HTTPS no VM.  O script requer que já tenha um certificado enviado para o Cofre de Chaves Azure, como descrito nos [certificados Create para Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Modelo de implementação vm

O modelo de implantação quickstart Azure VM, está disponível como o ficheiro online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Contém os seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Nome da conta de armazenamento                       |
| dnsNameForPublicIP    | Nome DNS para o IP público. Deve ser minúscula.    |
| adminUserName         | Nome de utilizador do administrador                          |
| adminPassword         | Senha do administrador                          |
| imagemEditora        | Editor de imagem                                   |
| imagemOffer            | Oferta de imagem                                       |
| imagensSKU              | Imagem SKU                                         |
| vmSize                | Tamanho do VM                                    |
| vmName                | Nome do VM                                    |
| nome do cofre             | Nome do cofre da chave                             |
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
