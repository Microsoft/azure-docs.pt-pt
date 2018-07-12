---
title: Tutorial – Criar e utilizar discos para conjuntos de dimensionamento com o Azure PowerShell | Microsoft Docs
description: Saiba como utilizar o Azure PowerShell para criar e utilizar Managed Disks com conjuntos de dimensionamento de máquinas virtuais, incluindo como adicionar, preparar, listar e desanexar discos.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 17f20ac2313ed4abd4f86bf10b65848a9870a688
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606676"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Criar e utilizar discos com um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell
Os conjuntos de dimensionamento de máquinas virtuais utilizam discos para armazenar o sistema operativo, as aplicações e os dados da instância de VM. Ao criar e gerir um conjunto de dimensionamento, é importante escolher um tamanho de disco e a configuração adequados para a carga de trabalho esperada. Este tutorial abrange como criar e gerir discos de VM. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial precisará da versão 6.0.0 ou posterior do módulo do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzureRmAccount` para criar uma ligação com o Azure. 


## <a name="default-azure-disks"></a>Discos do Azure predefinidos
Quando um conjunto de dimensionamento é criado ou dimensionado, são anexados automaticamente dois discos a cada instância de VM. 

**Disco do sistema operativo** – os discos do sistema operativo podem ter o tamanho máximo de 2 TB e alojam o sistema operativo da instância de VM. O disco do SO está identificado como */dev/sda* por predefinição. A configuração da colocação em cache do disco do SO está otimizada para desempenho do SO. Devido a esta configuração, o disco do SO **não deve** alojar aplicações nem dados. Para aplicações e dados, utilize discos de dados, que são descritos posteriormente neste artigo. 

**Disco temporário** – os discos temporários utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião do Azure da instância de VM. São discos de elevado desempenho e podem ser utilizados para operações como o processamento de dados temporários. No entanto, se a instância de VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo tamanho da instância de VM. Os discos temporários estão identificados como */dev/sdb* e têm um ponto de montagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos dos discos temporários
| Tipo | Tamanhos comuns | Tamanho máximo de disco temporário (GiB) |
|----|----|----|
| [Fins gerais](../virtual-machines/windows/sizes-general.md) | Séries A, B e D | 1600 |
| [Com otimização de computação](../virtual-machines/windows/sizes-compute.md) | Série F | 576 |
| [Com otimização de memória](../virtual-machines/windows/sizes-memory.md) | Séries D, E, G e M | 6144 |
| [Com otimização de armazenamento](../virtual-machines/windows/sizes-storage.md) | Série L | 5630 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Série N | 1440 |
| [Elevado desempenho](../virtual-machines/windows/sizes-hpc.md) | Séries A e H | 2000 |


## <a name="azure-data-disks"></a>Discos de dados do Azure
Podem ser adicionados mais discos de dados se precisar de instalar aplicações e armazenar dados. Os discos de dados devem ser utilizados em qualquer situação em que se pretenda armazenamento de dados duradouro e reativo. Cada disco de dados tem a capacidade máxima de 4 TB. O tamanho da instância de VM determina quantos discos de dados podem ser anexados. Para cada vCPU de VM, podem ser expostos dois discos de dados.

### <a name="max-data-disks-per-vm"></a>Discos de dados máximos por VM
| Tipo | Tamanhos comuns | Discos de dados máximos por VM |
|----|----|----|
| [Fins gerais](../virtual-machines/windows/sizes-general.md) | Séries A, B e D | 64 |
| [Com otimização de computação](../virtual-machines/windows/sizes-compute.md) | Série F | 64 |
| [Com otimização de memória](../virtual-machines/windows/sizes-memory.md) | Séries D, E, G e M | 64 |
| [Com otimização de armazenamento](../virtual-machines/windows/sizes-storage.md) | Série L | 64 |
| [GPU](../virtual-machines/windows/sizes-gpu.md) | Série N | 64 |
| [Elevado desempenho](../virtual-machines/windows/sizes-hpc.md) | Séries A e H | 64 |


## <a name="vm-disk-types"></a>Tipos de disco de VM
O Azure oferece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard
O Armazenamento Standard está protegido por HDDs e fornece armazenamento e desempenho económicos. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.

### <a name="premium-disk"></a>Disco Premium
Os discos Premium são apoiados por discos de elevado desempenho baseado em SSD e de baixa latência. Estes discos são recomendados para VMs que executam cargas de trabalho de produção. O Armazenamento Premium suporta VMs da série DS, série DSv2, série GS e série FS. Quando selecionar um tamanho de disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for inferior a 128 GB, o tipo de disco é P10. Se o tamanho do disco estiver entre 129 GB e 512 GB, o tamanho é P20. Acima de 512 GB, o tamanho é P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium
|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GB | 64 GB | 128 GB | 512 GB | 1.024 GB (1 TB) | 2.048 GB (2 TB) | 4.095 GB (4 TB) |
| IOPs Máx por disco | 120 | 240 | 500 | 2.300 | 5.000 | 7.500 | 7.500 |
Débito por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Enquanto a tabela acima identifica o IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao repartir vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80 000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, veja [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md).


## <a name="create-and-attach-disks"></a>Criar e anexar discos
Pode criar e anexar discos quando criar um conjunto de dimensionamento ou com um conjunto de dimensionamento existente.

### <a name="attach-disks-at-scale-set-creation"></a>Anexar discos durante a criação do conjunto de dimensionamento
Crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Quando lhe for pedido, forneça um nome de utilizador e palavra-passe para as instâncias de VM. Para distribuir o tráfego para instâncias de VM individuais, é também criado um balanceador de carga. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, bem como permitir o tráfego de ambiente de trabalho remoto na porta TCP 3389 e a comunicação remota do PowerShell na porta TCP 5985.

São criados dois discos com o parâmetro `-DataDiskSizeGb`. O primeiro disco tem *64* GB de tamanho e o segundo disco tem *128* GB. Quando solicitado, forneça as suas próprias credenciais administrativas pretendidas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

A criação e configuração de todas as instâncias de VM e recursos do conjunto de dimensionamento demora alguns minutos.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anexar um disco a um conjunto de dimensionamento existente
Também pode anexar discos a um conjunto de dimensionamento existente. Utilize o conjunto de dimensionamento criado no passo anterior para adicionar outro disco com [Add-AzureRmVmssDataDisk](/powershell/module/azurerm.compute/add-azurermvmssdatadisk). O exemplo seguinte anexa um disco de *128* GB adicional a um conjunto de dimensionamento existente:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzureRmVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Preparar os discos de dados
Os discos criados e anexados às instâncias de VM do conjunto de dimensionamento são discos não processados. Antes de poder utilizá-los com os seus dados e aplicações, os discos têm de ser preparados. Para preparar os discos, crie uma partição e um sistema de ficheiros, e monte-os.

Para automatizar o processo em múltiplas instâncias de VM num conjunto de dimensionamento, pode utilizar a Extensão de Script Personalizado do Azure. Esta extensão pode executar scripts localmente em cada instância de VM, como preparar discos de dados anexados. Para obter mais informações, veja a [Descrição geral da Extensão de Script Personalizado](../virtual-machines/windows/extensions-customscript.md).

O exemplo seguinte executa um script a partir de um repositório de exemplo do GitHub em cada instância de VM com [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension) que prepara todos os discos raw de dados anexados:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Para confirmar que os discos foram preparados corretamente, execute o protocolo RDP numa das instâncias de VM. 

Primeiro, obtenha o objeto de balanceador de carga com [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Em seguida, veja as regras NAT de entrada com [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig). As regras NAT listam a *FrontendPort* para cada instância de VM escutada por RDP. Por fim, obtenha o endereço IP público do balanceador de carga com [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

Para ligar à VM, especifique o seu próprio endereço IP público e número de porta da instância de VM necessária, conforme mostrado nos comandos anteriores. Quando lhe for pedido, introduza as credenciais utilizadas quando criou o conjunto de dimensionamento. Se utiliza o Azure Cloud Shell, efetue este passo a partir de uma linha de comandos do PowerShell ou do Cliente de Ambiente de Trabalho Remoto. O exemplo seguinte liga à instância de VM *1*:

```powershell
mstsc /v 52.168.121.216:50001
```

Abra uma sessão do PowerShell local na instância de VM e observe os discos ligados com [Get-Disk](/powershell/module/storage/get-disk):

```powershell
Get-Disk
```

O resultado de exemplo seguinte mostra que os três discos de dados estão anexados à instância de VM.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Examine os sistemas de ficheiros e os pontos de montagem na instância de VM da seguinte forma:

```powershell
Get-Partition
```

O resultado de exemplo seguinte mostra que foram atribuídas letras de unidade aos três discos de dados:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

Os discos em cada instância de VM no seu dimensionamento são preparados automaticamente da mesma forma. À medida que o conjunto de dimensionamento aumenta verticalmente, são anexados os discos de dados necessários às novas instâncias de VM. A Extensão de Script Personalizado também é executada para preparar automaticamente os discos.

Feche a sessão da ligação de ambiente de trabalho remoto à instância de VM.


## <a name="list-attached-disks"></a>Listar os discos anexados
Para ver informações sobre os discos anexados a um conjunto de dimensionamento, utilize [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) da seguinte forma:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

Na propriedade *VirtualMachineProfile.StorageProfile*, é apresentada a lista de *DataDisks*. São apresentadas informações sobre o tamanho do disco, a camada de armazenamento e o LUN (Número de Unidade Lógica). O resultado de exemplo seguinte detalha os três discos de dados anexados ao conjunto de dimensionamento:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Desligar um disco
Quando já não precisar de um determinado disco, pode desanexá-lo do conjunto de dimensionamento. O disco é removido de todas as instâncias de VM no conjunto de dimensionamento. Para desanexar um disco de um conjunto de dimensionamento, utilize [Remove-AzureRmVmssDataDisk](/powershell/module/azurerm.compute/remove-azurermvmssdatadisk) e especifique o LUN do disco. Os LUNs são apresentados no resultado de [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) na secção anterior. O exemplo seguinte desanexa o LUN *3* do conjunto de dimensionamento:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzureRmVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o conjunto de dimensionamento e os discos, elimine o grupo de recursos e todos os respetivos recursos com [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). O parâmetro `-Force` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo. O parâmetro `-AsJob` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar e utilizar discos com conjuntos de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Avance para o próximo tutorial para saber como utilizar uma imagem personalizada para as instâncias de VM do conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Utilizar uma imagem personalizada para as instâncias de VM do conjunto de dimensionamento](tutorial-use-custom-image-powershell.md)
