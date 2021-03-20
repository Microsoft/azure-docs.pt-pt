---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed2b13077ff5809899097254dde0fa8406e1db3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85601394"
---
Pode desativar a encriptação utilizando o Azure PowerShell, o Azure CLI ou com um modelo de Gestor de Recursos. 

>[!IMPORTANT]
>A encriptação incapacitante com encriptação de disco Azure em VMs Linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de SO se o volume de SO tiver sido encriptado.  

- **Desative a encriptação do disco com a Azure PowerShell:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Desative a encriptação com um modelo de Gestor de Recursos:** Utilize a [encriptação de desativação num modelo de VM de Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) para desativar a encriptação.
     1. Clique em **Implementar no Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, termos legais e acordo.
