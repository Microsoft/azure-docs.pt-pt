---
title: Ativar discos partilhados para discos geridos aZure
description: Configurar um disco gerido a Azure com discos partilhados para que possa partilhá-lo em vários VMs
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 661d4ba575eafa4e261a1c92c1112a259b95eac7
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683597"
---
# <a name="enable-shared-disk"></a>Ativar disco partilhado

Este artigo abrange como ativar a funcionalidade de discos partilhados para discos geridos Azure. Os discos partilhados Azure são uma nova funcionalidade para discos geridos aZure que lhe permite anexar um disco gerido a várias máquinas virtuais (VMs) simultaneamente. A anexação de um disco gerido a vários VMs permite-lhe implementar novas ou migrar aplicações agrupadas existentes para o Azure. 

Se procura informações conceptuais sobre discos geridos que tenham discos partilhados ativados, consulte [discos partilhados do Azure.](disks-shared.md)

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Os discos partilhados suportam vários sistemas operativos. Consulte as secções [Windows](windows/disks-shared.md#windows) e [Linux](linux/disks-shared.md#linux) do artigo conceptual para os sistemas operativos suportados.

## <a name="disk-sizes"></a>Tamanhos de disco

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Implementar discos partilhados

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Implementar um SSD premium como disco partilhado

Para implementar um disco gerido com a funcionalidade de disco partilhado ativada, utilize a nova propriedade e defina um valor superior a `maxShares` 1. Isto torna o disco partilhável em vários VMs.

> [!IMPORTANT]
> O valor só `maxShares` pode ser definido ou alterado quando um disco é desmontado de todos os VMs. Consulte os [tamanhos](#disk-sizes) do disco para obter os valores permitidos para `maxShares` .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

Antes de utilizar o seguinte modelo, `[parameters('dataDiskName')]` substitua, , , e pelos seus `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` próprios valores.

[Modelo de disco compartilhado Premium SSD](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Implementar um disco ultra como um disco partilhado

Para implementar um disco gerido com a função de disco partilhado ativada, altere o `maxShares` parâmetro para um valor superior a 1. Isto torna o disco partilhável em vários VMs.

> [!IMPORTANT]
> O valor só `maxShares` pode ser definido ou alterado quando um disco é desmontado de todos os VMs. Consulte os [tamanhos](#disk-sizes) do disco para obter os valores permitidos para `maxShares` .


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Exemplo de disco regional

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Exemplo de disco zonal

Este exemplo é quase o mesmo que o anterior, exceto que cria um disco na zona de disponibilidade 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Exemplo de disco regional

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Exemplo de disco zonal

Este exemplo é quase o mesmo que o anterior, exceto que cria um disco na zona de disponibilidade 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Exemplo de disco regional

Antes de utilizar o seguinte modelo, `[parameters('dataDiskName')]` substitua, , , , , , , , `[resourceGroup().location]` e com os seus `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` próprios valores.

[Modelo de ultra discos compartilhados regionais](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Exemplo de disco zonal

Antes de utilizar o seguinte modelo, `[parameters('dataDiskName')]` substitua, , , , , , , , `[resourceGroup().location]` e com os seus `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` próprios valores.

[Modelo de discos ultra compartilhados zonal](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Usando discos compartilhados Azure com os seus VMs

Depois de ter implantado um disco partilhado, `maxShares>1` pode montar o disco num ou mais dos seus VMs.

> [!NOTE]
> Se estiver a utilizar um disco ultra, certifique-se de que corresponde aos requisitos necessários. Consulte [a utilização de discos ultra Azure](disks-enable-ultra-ssd.md) para mais detalhes.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

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

Se preferir utilizar modelos do Gestor de Recursos Azure para implementar o seu disco, estão disponíveis os seguintes modelos de amostra:
- [SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Discos ultra regionais](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Discos ultra zonais](https://aka.ms/SharedUltraDiskARMtemplateZonal)
