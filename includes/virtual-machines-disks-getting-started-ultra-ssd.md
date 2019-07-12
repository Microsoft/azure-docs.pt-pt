---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 742e0028b1f92beb8300cc97f09d8292259fbc0a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712525"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>Ativar e implementar o SSDs ultra do Azure (pré-visualização)

Estado do Azure de sólido ultra unidades de débito elevado de oferta (SSD) (pré-visualização), o IOPS elevado e o armazenamento de disco de baixa latência consistente para máquinas virtuais (VMs) IaaS do Azure. Esta nova oferta fornece a parte superior do desempenho de linha nos níveis de disponibilidade mesmo como nossas ofertas de discos existentes. Um dos principais benefícios do ultra SSDs é a capacidade de alterar dinamicamente o desempenho de SSD, juntamente com as suas cargas de trabalho sem a necessidade de reiniciar as suas VMs. Ultra SSDs são adequados para cargas de trabalho com uso intensivo de dados, como SAP HANA, bases de dados de escalão superior e cargas de trabalho pesado de transação.

Atualmente, o ultra SSDs estão em pré-visualização e deverá [inscrever](https://aka.ms/UltraSSDPreviewSignUp) na pré-visualização para poder aceder aos mesmos.

## <a name="determine-your-availability-zone"></a>Determinar a sua zona de disponibilidade

Após a aprovação, precisa determinar qual está, para poder utilizar ultra SSDs de zona de disponibilidade. Execute um dos seguintes comandos para determinar exatamente em qual zona na região E.U.A. Leste 2 para implementar o seu disco ultra para:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query "[?name=='UltraSSD_LRS'].locationInfo"`

A resposta será semelhante ao formulário abaixo, onde X é a zona a utilizar para implementar na região E.U.A. Leste 2. X pode ser 1, 2 ou 3.

Preservar a **zonas** valor, ele representa a sua zona de disponibilidade e irá precisar dele para poder implementar um SSD ultra.

|ResourceType  |Nome  |Location  |Zonas  |Restrição  |Funcionalidade  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|Discos     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se não houve resposta do comando, em seguida, o registo para a funcionalidade é ainda pendente ou não aprovado ainda.

Agora que sabe exatamente em qual zona para implementar, siga os passos de implementação neste artigo para obter a sua primeira VMs implementadas com o ultra SSD.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Implementar um SSD ultra com o Azure Resource Manager

Primeiro, determine o tamanho da VM para implementar. Como parte desta pré-visualização, apenas as famílias DsV3 e EsV3 VM são suportadas. Consulte a segunda tabela sobre isso [blogue](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre estes tamanhos VM.

Se gostaria de criar uma VM com vários SSDs ultra, consulte o exemplo [criar uma VM com vários SSD ultra](https://aka.ms/UltraSSDTemplate).

Se pretende usar seu próprio modelo, certifique-se de que **apiVersion** para `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` está definida como `2018-06-01` (ou posterior).

Defina o sku de disco como **UltraSSD_LRS**, em seguida, defina a capacidade do disco, o IOPS, a zona de disponibilidade e o débito em MBps para criar um disco ultra.

Assim que a VM está aprovisionada, pode particionar e formatar os discos de dados e configurá-los para cargas de trabalho.

## <a name="deploy-an-ultra-ssd-using-cli"></a>Implementar um SSD ultra com a CLI

Primeiro, determine o tamanho da VM para implementar. Como parte desta pré-visualização, apenas as famílias DsV3 e EsV3 VM são suportadas. Consulte a segunda tabela sobre isso [blogue](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre estes tamanhos VM.

Utilizar ultra SSDs, tem de criar uma VM que é capaz de usar o ultra SSDs.

Substituir ou definir o **$vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** variáveis com seus próprios valores. Definir **$zone** para o valor da sua zona de disponibilidade que recebeu do [início deste artigo](#determine-your-availability-zone). Em seguida, execute o seguinte comando da CLI para criar uma VM ativada ultra:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>Criar um SSD ultra com a CLI

Agora que tem uma VM que é capaz de usar o ultra SSDs, pode criar e anexar um SSD ultra a ele.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>Ajustar o desempenho de um SSD ultra com a CLI

Ultra SSDs oferecem uma capacidade exclusiva que permite-lhe ajustar o desempenho, o comando seguinte ilustra como utilizar esta funcionalidade:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>Implementar um SSD ultra com o PowerShell

Primeiro, determine o tamanho da VM para implementar. Como parte desta pré-visualização, apenas as famílias DsV3 e EsV3 VM são suportadas. Consulte a segunda tabela sobre isso [blogue](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter mais detalhes sobre estes tamanhos VM.

Utilizar ultra SSDs, tem de criar uma VM que é capaz de usar o ultra SSDs. Substituir ou definir o **$resourcegroup** e **$vmName** variáveis com seus próprios valores. Definir **$zone** para o valor da sua zona de disponibilidade que recebeu do [início deste artigo](#determine-your-availability-zone). Em seguida, execute o seguinte [New-AzVm](/powershell/module/az.compute/new-azvm) comando para criar um ultra da VM ativada:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-ssd-using-powershell"></a>Criar um SSD ultra com o PowerShell

Agora que tem uma VM que é capaz de usar o ultra SSDs, pode criar e anexar um SSD ultra a ele:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>Ajustar o desempenho de um SSD ultra com o PowerShell

Ultra SSDs tem uma capacidade exclusiva que permite-lhe ajustar o desempenho, o comando seguinte é um exemplo que se ajuste o desempenho sem ter de desanexar o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Passos seguintes

Se quiser experimentar o novo tipo de disco [pedir acesso para a pré-visualização com este inquérito](https://aka.ms/UltraSSDPreviewSignUp).