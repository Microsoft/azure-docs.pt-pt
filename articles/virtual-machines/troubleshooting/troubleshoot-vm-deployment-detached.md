---
title: Implantação de máquina virtual de resolução de problemas devido a discos separados / Microsoft Docs
description: Implantação de máquina virtual de resolução de problemas devido a discos separados
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75486823"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>Implantação de máquina virtual de resolução de problemas devido a discos separados

## <a name="symptom"></a>Sintoma

Quando estiver a tentar atualizar uma máquina virtual cujo descolamento de disco de dados anterior falhou, pode encontrar este código de erro.

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>Causa

Este erro ocorre quando se tenta recolocar um disco de dados cuja última operação de desacopte falhou. A melhor maneira de sair deste estado é separar o disco falhado.

## <a name="solution-1-powershell"></a>Solução 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>Passo 1: Obtenha os detalhes da máquina virtual e do disco

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Passo 2: Coloque a bandeira para os discos falhados como "verdadeiros".

Obtenha o índice de matriz do disco em falha e coloque a bandeira **toBeDetached** para o disco em falha (para o qual ocorreu o erro **attachDiskWhileBeingDetached)** para "verdadeiro". Esta definição implica separar o disco da máquina virtual. O nome do disco em falha pode ser encontrado no **erroMensagem**.

> ! Nota: A versão API especificada para chamadas Get and Put tem de ser 2019-03-01 ou superior.

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

Em alternativa, também pode desmontar este disco utilizando o comando abaixo, o que será útil para os utilizadores que utilizem versões API antes de 01 de março de 2019.

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>Passo 3: Atualizar a máquina virtual

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>Solução 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>Passo 1: Obtenha a carga útil da máquina virtual.

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>Passo 2: Coloque a bandeira para os discos falhados como "verdadeiros".

Detete a bandeira **toBeDetached** para falhar o disco verdadeiro na carga útil devolvida no passo 1. Nota: A versão API especificada para chamadas `2019-03-01` Get and Put tem de ser ou maior.

**Corpo de Pedido de Amostra**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Alternadamente também pode remover o disco de dados em falha da carga útil acima, o que é útil para os utilizadores que usam versões API antes de 01 de março de 2019.

### <a name="step-3-update-the-virtual-machine"></a>Passo 3: Atualizar a máquina virtual

Utilize a carga útil do corpo de pedido definida no passo 2 e atualize a máquina virtual da seguinte forma:

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**Resposta da amostra:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>Passos Seguintes

Se tiver problemas de ligação ao seu VM, consulte [ligações RDP de Troubleshoot a um VM Azure](troubleshoot-rdp-connection.md).

Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade da [aplicação Troubleshoot num VM windows](troubleshoot-app-connection.md).
