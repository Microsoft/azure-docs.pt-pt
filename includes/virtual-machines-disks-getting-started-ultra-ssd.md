---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008650"
---
Os discos ultra azure oferecem alta entrada, iOPS elevados e armazenamento consistente de disco de baixa latência para máquinas virtuais Azure IaaS (VMs). Esta nova oferta proporciona um desempenho topo de linha nos mesmos níveis de disponibilidade que as nossas ofertas de discos existentes. Um dos principais benefícios dos discos ultra é a capacidade de alterar dinamicamente o desempenho do SSD juntamente com as suas cargas de trabalho sem a necessidade de reiniciar os seus VMs. Os discos Ultra são indicados para cargas de trabalho com utilização intensa de dados, como o SAP HANA, base de dados de escalão superior e cargas de trabalho com bastantes transações.

## <a name="ga-scope-and-limitations"></a>Âmbito e limitações ga

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinar o tamanho do VM e a disponibilidade da região

### <a name="vms-using-availability-zones"></a>VMs usando zonas de disponibilidade

Para alavancar os discos ultra, precisa determinar em que zona de disponibilidade está. Nem todas as regiões suportam todas as dimensões vm com discos ultra. Para determinar se a sua região, zona e tamanho VM suportam discos ultra discos, execute qualquer um dos seguintes comandos, certifique-se de substituir a **região**, **vmSize**, e valores de **subscrição** primeiro:

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

A resposta será semelhante à forma abaixo, onde X é a zona a utilizar para implantação na sua região escolhida. X pode ser 1, 2 ou 3.

Preservar o valor **das Zonas,** representa a sua zona de disponibilidade e vai precisar dela para implantar um disco Ultra.

|ResourceType  |Nome  |Localização  |Zonas  |Restrição  |Capacidade  |Valor  |
|---------|---------|---------|---------|---------|---------|---------|
|discos     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se não houve resposta do comando, então o tamanho de VM selecionado não é suportado com discos ultra na região selecionada.

Agora que sabe para que zona se implantar, siga os passos de implementação deste artigo para implantar um VM com um disco ultra ligado ou anexar um disco ultra a um VM existente.

### <a name="vms-with-no-redundancy-options"></a>VMs sem opções de despedimento

Os discos ultra implantados nos EUA Ocidentais devem ser implantados sem opções de despedimento, por enquanto. No entanto, nem todos os tamanhos do disco que suportam discos ultra podem estar nesta região. Para determinar quais nos eua ocidentais suportam discos ultra, pode utilizar qualquer um dos seguintes códigos. Certifique-se de `vmSize` `subscription` substituir primeiro os valores e os valores:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

A resposta será semelhante à `UltraSSDAvailable   True` seguinte forma, indicando se o tamanho vm suporta discos ultra nesta região.

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

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Implementar um disco ultra utilizando o Gestor de Recursos Azure

Primeiro, determine o tamanho vm para implantar. Para obter uma lista de tamanhos VM suportados, consulte a secção de [possibilidades e limitações da GA.](#ga-scope-and-limitations)

Se quiser criar um VM com vários discos ultra, consulte a amostra [Criar um VM com vários discos ultra.](https://aka.ms/ultradiskArmTemplate)

Se pretender utilizar o seu próprio modelo, certifique-se de `2018-06-01` que a **apiVersão** para `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` está definida como (ou mais tarde).

Desloque o sku do disco para **UltraSSD_LRS,** em seguida, definir a capacidade do disco, IOPS, zona de disponibilidade e entrada em MBps para criar um disco ultra.

Uma vez que o VM é provisionado, pode dividir e formatar os discos de dados e configurá-los para as suas cargas de trabalho.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Implementar um disco ultra utilizando o portal Azure

Esta secção cobre a implementação de uma máquina virtual equipada com um disco ultra como disco de dados. Assume que tem familiaridade com a implementação de uma máquina virtual, caso não o faça, veja o nosso [Quickstart: Crie uma máquina virtual Windows no portal Azure](../articles/virtual-machines/windows/quick-create-portal.md).

- Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para implantar uma máquina virtual (VM).
- Certifique-se de escolher um [tamanho e região vm suportados.](#ga-scope-and-limitations)
- Selecione **zona de disponibilidade** nas **opções de Disponibilidade.**
- Preencha as restantes entradas com seleções à sua escolha.
- Selecione os **Discos**.

![criar-ultra-disco-habilitado-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Na lâmina dos Discos, selecione **Sim** para **enable ultra disk compatibilidade**.
- Selecione **Criar e fixe um novo disco** para fixar um disco ultra agora.

![activa-e-anexar-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Na **Create a new disk** blade, introduza um nome e, em seguida, selecione **Tamanho De mudança**.
- Alterar o tipo de **conta** para **Ultra Disk**.
- Altere os valores do tamanho do **disco personalizado (GiB),** **IOPS do disco**e a entrada do **disco** para os da sua escolha.
- Selecione **OK** em ambas as lâminas.
- Continue com a implantação de VM, será o mesmo que utilizarqualquer outro VM.

![criar-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Fixe um disco ultra utilizando o portal Azure

Alternativamente, se o seu VM existente estiver numa zona de região/disponibilidade capaz de utilizar discos ultra, pode utilizar discos ultra sem ter de criar um novo VM. Ao ativar discos ultra no VM existente, em seguida, fixando-os como discos de dados.

- Navegue para o seu VM e selecione **Discos**.
- Selecione **Editar**.

![opções-selector-ultra-discos.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- **Selecione Sim** para ativar a compatibilidade ultra **disco**.

![ultra-opções-sim-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selecione **Guardar**.
- **Selecione Adicionar disco de dados** e, em seguida, no dropdown para **Nome** selecione **Criar disco**.

![criar e anexar-novo-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Preencha um nome para o seu novo disco e, em seguida, selecione alterar o **tamanho**.
- Alterar o tipo de **conta** para **Ultra Disk**.
- Altere os valores do tamanho do **disco personalizado (GiB),** **IOPS do disco**e a entrada do **disco** para os da sua escolha.
- Selecione **OK** e selecione **Criar**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Depois de ser devolvido à lâmina do disco, selecione **Save**.

![poupança e anexação-novo-ultra-disco.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Ajuste o desempenho de um disco ultra utilizando o portal Azure

Os discos Ultra oferecem uma capacidade única que lhe permite ajustar o seu desempenho. Pode efazer estes ajustes a partir do portal Azure, nos próprios discos.

- Navegue para o seu VM e selecione **Discos**.
- Selecione o disco ultra que pretende modificar o desempenho de.

![selecionando-ultra-disco-to-modificação.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- **Selecione Configuração** e, em seguida, faça as suas modificações.
- Selecione **Guardar**.

![configuração-ultra-disco-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Implementar um disco ultra utilizando o CLI

Primeiro, determine o tamanho vm para implantar. Consulte a secção de [possibilidades e limitações da GA](#ga-scope-and-limitations) para obter uma lista de tamanhos vm suportados.

É necessário criar um VM capaz de utilizar discos ultra, de modo a fixar um disco ultra.

Substitua ou coloque as **variáveis** **$vmname,** **$rgname,** **$diskname,** **$location,** **$password,**$user com os seus próprios valores. **Dete$zone** ao valor da sua zona de disponibilidade que obteve desde o [início deste artigo.](#determine-vm-size-and-region-availability) Em seguida, executar o seguinte comando CLI para criar um VM ultra habilitado:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Ativar a compatibilidade do disco ultra num VM existente

Se o seu VM cumprir os requisitos descritos no [âmbito e limitações da GA](#ga-scope-and-limitations) e estiver na zona apropriada para a sua [conta,](#determine-vm-size-and-region-availability)então pode ativar a compatibilidade ultra discona sua VM.

Para permitir a compatibilidade do disco ultra, tem de parar o VM. Depois de parar o VM, pode ativar a compatibilidade, anexar um disco ultra e, em seguida, reiniciar o VM:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Criar um disco ultra usando o CLI

Agora que tem um VM capaz de anexar discos ultra, pode criar e anexar um disco ultra.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Fixe um disco ultra a um VM utilizando cli

Alternativamente, se o seu VM existente estiver numa zona de região/disponibilidade capaz de utilizar discos ultra, pode utilizar discos ultra sem ter de criar um novo VM.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ajuste o desempenho de um disco ultra utilizando CLI

Os discos Ultra oferecem uma capacidade única que lhe permite ajustar o seu desempenho, o seguinte comando mostra como utilizar esta funcionalidade:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Implementar um disco ultra usando powerShell

Primeiro, determine o tamanho vm para implantar. Consulte a secção de [possibilidades e limitações da GA](#ga-scope-and-limitations) para obter uma lista de tamanhos vm suportados.

Para utilizar discos ultra, é necessário criar um VM capaz de utilizar discos ultra. Substitua ou coloque as variáveis **$resourcegroup** e **$vmName** com os seus próprios valores. **Dete$zone** ao valor da sua zona de disponibilidade que obteve desde o [início deste artigo.](#determine-vm-size-and-region-availability) Em seguida, executar o seguinte comando [New-AzVm](/powershell/module/az.compute/new-azvm) para criar um VM ultra habilitado:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Ativar a compatibilidade do disco ultra num VM existente

Se o seu VM cumprir os requisitos descritos no [âmbito e limitações da GA](#ga-scope-and-limitations) e estiver na zona apropriada para a sua [conta,](#determine-vm-size-and-region-availability)então pode ativar a compatibilidade ultra discona sua VM.

Para permitir a compatibilidade do disco ultra, tem de parar o VM. Depois de parar o VM, pode ativar a compatibilidade, anexar um disco ultra e, em seguida, reiniciar o VM:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Crie um disco ultra usando powerShell

Agora que tem um VM capaz de usar discos ultra, pode criar e anexar um disco ultra:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Fixe um disco ultra a um VM utilizando powerShell

Alternativamente, se o seu VM existente estiver numa zona de região/disponibilidade capaz de utilizar discos ultra, pode utilizar discos ultra sem ter de criar um novo VM.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajuste o desempenho de um disco ultra utilizando powerShell

Os discos ultra têm uma capacidade única que lhe permite ajustar o seu desempenho, o seguinte comando é um exemplo que ajusta o desempenho sem ter de desprender o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```