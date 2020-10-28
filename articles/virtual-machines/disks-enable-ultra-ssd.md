---
title: Discos ultra para VMs - Discos geridos Azure
description: Saiba mais sobre discos ultra para VMs Azure
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: d7718ebbbf4f9dec3519ce46e5d0d1cdbb5a7460
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745972"
---
# <a name="using-azure-ultra-disks"></a>Usando discos ultra Azure

Este artigo explica como implementar e utilizar um disco ultra, para informações conceptuais sobre discos ultra, consulte [quais os tipos de disco disponíveis no Azure?](disks-types.md#ultra-disk)

Os discos Azure ultra oferecem alta produção, iops elevados e armazenamento consistente de disco de baixa latência para máquinas virtuais Azure IaaS (VMs). Esta nova oferta fornece o desempenho topo de gama nos mesmos níveis de disponibilidade que as nossas ofertas de discos existentes. Um dos principais benefícios dos discos ultra é a capacidade de alterar dinamicamente o desempenho do SSD juntamente com as suas cargas de trabalho sem a necessidade de reiniciar os seus VMs. Os discos Ultra são indicados para cargas de trabalho com utilização intensa de dados, como o SAP HANA, base de dados de escalão superior e cargas de trabalho com bastantes transações.

## <a name="ga-scope-and-limitations"></a>Âmbito e limitações do GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinar o tamanho do VM e a disponibilidade da região

### <a name="vms-using-availability-zones"></a>VMs usando zonas de disponibilidade

Para alavancar discos ultra, precisa determinar em que zona de disponibilidade está. Nem todas as regiões suportam cada tamanho VM com discos ultra. Para determinar se a sua região, zona e suporte de tamanho VM ultra discos, executar qualquer um dos seguintes comandos, certifique-se de substituir a **região** , **vmSize** , e valores **de subscrição** primeiro:

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

A resposta será semelhante ao formulário abaixo, onde X é a zona a utilizar para implantação na sua região escolhida. X pode ser 1, 2 ou 3.

Preserve o valor **zones,** representa a sua zona de disponibilidade e vai precisar dela para implementar um disco Ultra.

|ResourceType  |Nome  |Localização  |Zonas  |Restrição  |Capacidade  |Valor  |
|---------|---------|---------|---------|---------|---------|---------|
|discos     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se não houve resposta do comando, então o tamanho VM selecionado não é suportado com discos ultra na região selecionada.

Agora que sabe para que zona se implantar, siga as etapas de implantação deste artigo para implantar um VM com um disco ultra ligado ou anexar um disco ultra a um VM existente.

### <a name="vms-with-no-redundancy-options"></a>VMs sem opções de redundância

Os discos ultra implantados em regiões selecionadas devem ser implantados sem opções de redundância, por enquanto. No entanto, nem todos os tamanhos de disco que suportam discos ultra podem estar nesta região. Para determinar quais os tamanhos do disco que suportam discos ultra, pode utilizar qualquer um dos seguintes cortes de código. Certifique-se de substituir primeiro os `vmSize` `subscription` valores e valores:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

A resposta será semelhante à seguinte forma, `UltraSSDAvailable   True` indicando se o tamanho VM suporta discos ultra nesta região.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Implementar um disco ultra usando o Gestor de Recursos Azure

Primeiro, determine o tamanho VM para implantar. Para obter uma lista de tamanhos VM suportados, consulte a secção [de âmbito e limitações de GA.](#ga-scope-and-limitations)

Se quiser criar um VM com vários discos ultra, consulte a amostra [Criar um VM com vários discos ultra](https://aka.ms/ultradiskArmTemplate).

Se pretender utilizar o seu próprio modelo, certifique-se de que **a apiVersão** `Microsoft.Compute/virtualMachines` para e está definida como `Microsoft.Compute/Disks` `2018-06-01` (ou mais tarde).

Descreva o sku do disco para **UltraSSD_LRS,** em seguida, descreva a capacidade do disco, o IOPS, a zona de disponibilidade e a produção em MBps para criar um disco ultra.

Uma vez que o VM é a provisionado, pode dividir e formatar os discos de dados e configurá-los para as suas cargas de trabalho.


## <a name="deploy-an-ultra-disk"></a>Implementar um disco ultra

# <a name="portal"></a>[Portal](#tab/azure-portal)

Esta secção abrange a implantação de uma máquina virtual equipada com um disco ultra como disco de dados. Assume que tem familiaridade com a implantação de uma máquina virtual, caso não o faça, consulte o nosso [Quickstart: Criar uma máquina virtual Windows no portal Azure](./windows/quick-create-portal.md).

- Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para implantar uma máquina virtual (VM).
- Certifique-se de escolher um [tamanho e região de VM suportados.](#ga-scope-and-limitations)
- Selecione **zona de disponibilidade** nas **opções de disponibilidade.**
- Preencha as restantes entradas com as seleções à sua escolha.
- Selecione os **Discos** .

![Screenshot do fluxo de criação vm, lâmina básica.](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Na lâmina Discos, selecione **Sim** para **Ativar a compatibilidade do Disco Ultra** .
- Selecione **Criar e prenda um disco novo** para anexar agora um disco ultra.

![A screenshot do fluxo de criação vm, lâmina de disco, ultra está ativada e cria e anexa um novo disco é realçado.](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Na nova lâmina de **disco,** introduza um nome e, em seguida, selecione **Alterar o tamanho** .

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-create-new-disk-flow.png" alt-text="Screenshot de criar uma nova lâmina de disco, alterar tamanho realçado.":::


- Altere o **tipo de armazenamento** para Ultra **Disk** .
- Altere os valores do tamanho do **disco personalizado (GiB),** **do Disco IOPS** e da **produção** do disco para os que escolherem.
- Selecione **OK** em ambas as lâminas.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Screenshot de criar uma nova lâmina de disco, alterar tamanho realçado.":::

- Continue com a implantação de VM, será o mesmo que implementaria qualquer outro VM.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, determine o tamanho VM para implantar. Consulte a secção [de âmbito e limitações de GA](#ga-scope-and-limitations) para obter uma lista de tamanhos VM suportados.

Tem de criar um VM capaz de utilizar discos ultra, de modo a fixar um disco ultra.

Substitua ou desemalte as **variáveis $vmname** , **$rgname,** **$diskname,** **$password $location,** **$user, $user** variáveis pelos seus próprios valores. **$location** Desaça **$zone** ao valor da sua zona de disponibilidade que obteve desde o [início deste artigo.](#determine-vm-size-and-region-availability) Em seguida, executar o seguinte comando CLI para criar um VM ultra habilitado:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, determine o tamanho VM para implantar. Consulte a secção [de âmbito e limitações de GA](#ga-scope-and-limitations) para obter uma lista de tamanhos VM suportados.

Para utilizar discos ultra, tem de criar um VM capaz de utilizar discos ultra. Substitua ou desaprote as variáveis **$resourcegroup** e **$vmName** pelos seus próprios valores. Desaça **$zone** ao valor da sua zona de disponibilidade que obteve desde o [início deste artigo.](#determine-vm-size-and-region-availability) Em seguida, executar o seguinte comando [New-AzVm](/powershell/module/az.compute/new-azvm) para criar um VM ultra habilitado:

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

### <a name="create-and-attach-the-disk"></a>Criar e fixar o disco

Uma vez implementado o seu VM, pode criar e anexar um disco ultra, utilizar o seguinte script:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
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
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="attach-an-ultra-disk"></a>Anexar um disco ultra

# <a name="portal"></a>[Portal](#tab/azure-portal)

Em alternativa, se o seu VM existente estiver numa zona de região/disponibilidade capaz de utilizar discos ultra, pode utilizar discos ultra sem ter de criar um novo VM. Ao ativar discos ultra no seu VM existente, em seguida, anexando-os como discos de dados. Para ativar a compatibilidade ultra disco, tem de parar o VM. Depois de parar o VM, poderá ativar a compatibilidade e, em seguida, reiniciar o VM. Uma vez ativada a compatibilidade, pode anexar um disco ultra:

- Navegue para o seu VM e pare- o, espere que se transloja.
- Uma vez que o seu VM tenha sido transatado, selecione **Discos** .
- Selecione **Editar** .

![Screenshot de uma lâmina de disco Vm existente, a edição é realçada.](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selecione **Sim** para **Ativar a compatibilidade do Disco Ultra** .

![Screenshot de ativar a compatibilidade ultra disco.](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selecione **Guardar** .
- **Selecione Adicionar o disco de dados** em seguida, no dropdown para selecionar **Nome** **Criar disco** .

![Screenshot da lâmina do disco, adicionando um novo disco.](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Preencha um nome para o seu novo disco e, em seguida, selecione **Alterar o tamanho** .
- Altere o **tipo de Conta** para Ultra **Disco** .
- Altere os valores do tamanho do **disco personalizado (GiB),** **do Disco IOPS** e da **produção** do disco para os que escolherem.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="Screenshot de criar uma nova lâmina de disco, alterar tamanho realçado.":::

- Selecione **OK** e, em seguida, selecione **Criar** .
- Depois de ser devolvido à lâmina do disco, **selecione Save** .
- Comece o seu VM de novo.

![Screenshot da lâmina dos discos no seu vm.](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em alternativa, se o seu VM existente estiver numa zona de região/disponibilidade capaz de utilizar discos ultra, pode utilizar discos ultra sem ter de criar um novo VM.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Permitir a compatibilidade ultra disco num VM existente - CLI

Se o seu VM cumprir os requisitos descritos no [âmbito e limitações de GA](#ga-scope-and-limitations) e estiver na zona apropriada para a sua [conta,](#determine-vm-size-and-region-availability)então pode ativar a compatibilidade ultra disco no seu VM.

Para ativar a compatibilidade ultra disco, tem de parar o VM. Depois de parar o VM, poderá ativar a compatibilidade e, em seguida, reiniciar o VM. Uma vez ativada a compatibilidade, pode anexar um disco ultra:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Criar um disco ultra - CLI

Agora que tem um VM capaz de ligar discos ultra, pode criar e anexar um disco ultra.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
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

### <a name="attach-the-disk---cli"></a>Anexar o disco - CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Em alternativa, se o seu VM existente estiver numa zona de região/disponibilidade capaz de utilizar discos ultra, pode utilizar discos ultra sem ter de criar um novo VM.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Permitir a compatibilidade ultra disco num VM existente - PowerShell

Se o seu VM cumprir os requisitos descritos no [âmbito e limitações de GA](#ga-scope-and-limitations) e estiver na zona apropriada para a sua [conta,](#determine-vm-size-and-region-availability)então pode ativar a compatibilidade ultra disco no seu VM.

Para ativar a compatibilidade ultra disco, tem de parar o VM. Depois de parar o VM, poderá ativar a compatibilidade e, em seguida, reiniciar o VM. Uma vez ativada a compatibilidade, pode anexar um disco ultra:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Criar e anexar um disco ultra - PowerShell

Agora que tem um VM capaz de usar discos ultra, pode criar e anexar um disco ultra a ele:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
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
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Ajuste o desempenho de um disco ultra

# <a name="portal"></a>[Portal](#tab/azure-portal)

Os discos ultra oferecem uma capacidade única que lhe permite ajustar o seu desempenho. Pode fazer estes ajustes a partir do portal Azure, nos próprios discos.

- Navegue para o seu VM e selecione **Discos** .
- Selecione o disco ultra que pretende modificar o desempenho de.

![A imagem da lâmina dos discos no seu vm, ultra disco é realçada.](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selecione **Configuração** e, em seguida, faça as suas modificações.
- Selecione **Guardar** .

![A screenshot da lâmina de configuração no seu disco ultra, tamanho do disco, iops e saída são realçados, o save é realçado.](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Os discos ultra oferecem uma capacidade única que lhe permite ajustar o seu desempenho, o seguinte comando mostra como usar esta funcionalidade:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajuste o desempenho de um disco ultra usando o PowerShell

Os discos ultra têm uma capacidade única que lhe permite ajustar o seu desempenho, o seguinte comando é um exemplo que ajusta o desempenho sem ter de desmontar o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Passos seguintes

- [Utilize discos ultra Azure no Serviço Azure Kubernetes (pré-visualização)](../aks/use-ultra-disks.md).
- [Migrar o disco de log para um disco ultra.](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md)
