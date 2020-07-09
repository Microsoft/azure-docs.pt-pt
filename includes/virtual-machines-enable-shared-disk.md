---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0df74b82c847c9738d97d2001573666714c17672
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81008360"
---
## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos de disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Implementar discos partilhados

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Implementar um SSD premium como disco partilhado

Para implementar um disco gerido com a funcionalidade de disco partilhado ativada, utilize a nova propriedade e defina um valor superior a `maxShares` 1. Isto torna o disco partilhável em vários VMs.

> [!IMPORTANT]
> O valor só `maxShares` pode ser definido ou alterado quando um disco é desmontado de todos os VMs. Consulte os [tamanhos](#disk-sizes) do disco para obter os valores permitidos para `maxShares` .

Antes de utilizar o seguinte modelo, `[parameters('dataDiskName')]` substitua, , , e pelos seus `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` próprios valores.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Implementar um disco ultra como um disco partilhado

#### <a name="cli"></a>CLI

Para implementar um disco gerido com a função de disco partilhado ativada, altere o `maxShares` parâmetro para um valor superior a 1. Isto torna o disco partilhável em vários VMs.

> [!IMPORTANT]
> O valor só `maxShares` pode ser definido ou alterado quando um disco é desmontado de todos os VMs. Consulte os [tamanhos](#disk-sizes) do disco para obter os valores permitidos para `maxShares` .

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Para implementar um disco gerido com a funcionalidade de disco partilhado ativada, utilize a propriedade e defina um valor superior a `maxShares` 1. Isto torna o disco partilhável em vários VMs.

> [!IMPORTANT]
> O valor só `maxShares` pode ser definido ou alterado quando um disco é desmontado de todos os VMs. Consulte os [tamanhos](#disk-sizes) do disco para obter os valores permitidos para `maxShares` .

Antes de utilizar o seguinte modelo, `[parameters('dataDiskName')]` substitua, , , , , , , , `[resourceGroup().location]` e com os seus `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` próprios valores.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Usando discos compartilhados Azure com os seus VMs

Depois de ter implantado um disco partilhado, `maxShares>1` pode montar o disco num ou mais dos seus VMs.

> [!IMPORTANT]
> Todos os VM que partilham um disco devem ser implantados no mesmo [grupo de colocação de proximidade](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Comandos SCSI PR apoiados

Uma vez montado o disco partilhado nos seus VMs no seu cluster, pode estabelecer quórum e ler/escrever para o disco utilizando o SCSI PR. Os seguintes comandos de relações públicas estão disponíveis quando utilizar discos partilhados Azure:

Para interagir com o disco, comece com a lista de ação de reserva persistente:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Ao utilizar PR_RESERVE, PR_PREEMPT_RESERVATION ou PR_RELEASE_RESERVATION, forneça um dos seguintes tipos de reserva persistentes:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Você também precisa fornecer uma chave de reserva persistente ao usar PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION ou PR_RELEASE-RESERVA.


## <a name="next-steps"></a>Próximos passos

Se está interessado em experimentar discos partilhados, [inscreva-se para a nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).