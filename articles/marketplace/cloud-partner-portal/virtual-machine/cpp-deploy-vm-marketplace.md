---
title: Implementar uma VM no Azure Marketplace
description: Explica como implementar uma máquina virtual a partir de uma máquina virtual pré-configurada do Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 482840f5e611bc2d8092add2822a0ed5b2c8f883
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938702"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Implementar uma máquina virtual do Azure Marketplace

Este artigo explica como implementar uma máquina de virtual (VM) pré-configurada de um Azure Marketplace, com o script do Azure PowerShell fornecido.  Este script também expõe os pontos de extremidade do WinRM HTTP e HTTPS na VM.  O script exige que já tenha um certificado carregado para o Azure Key Vault, conforme descrito em [criar certificados para o Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Modelo de implementação de VM

O modelo de implementação de VM do Azure de início rápido, está disponível como o ficheiro online [azuredeploy. JSON](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Contém os seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Nome da conta de armazenamento                       |
| dnsNameForPublicIP    | Nome de DNS para o IP público. Tem de estar em minúsculas.    |
| adminUserName         | Nome de utilizador do administrador                          |
| adminPassword         | Palavra-passe do administrador                          |
| imagePublisher        | Publicador de imagens                                   |
| imageOffer            | Oferta de imagem                                       |
| imageSKU              | SKU de imagem                                         |
| vmSize                | Tamanho da VM                                    |
| vmName                | Nome da VM                                    |
| vaultName             | Nome do Cofre de chaves                             |
| vaultResourceGroup    | Grupo de recursos do Cofre de chaves                   |
| certificateUrl        | URL para o certificado, incluindo a versão no Cofre de chaves, por exemplo  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Script de implementação

Edite o seguinte script do PowerShell do Azure e executá-lo para implementar a VM do Azure Marketplace especificado.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Passos Seguintes

Assim que tiver implementado uma VM pré-configurada, pode configurar e acessar as soluções e serviços que contém ou utilizá-lo para o desenvolvimento ainda mais. 
