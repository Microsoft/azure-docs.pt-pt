---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d081a8cec088f4743bd0dc7d3cc37a9fade61d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117005"
---
Os discos ultra azure oferecem alta entrada, iOPS elevados e armazenamento consistente de disco de baixa latência para máquinas virtuais Azure IaaS (VMs). Esta nova oferta proporciona um desempenho topo de linha nos mesmos níveis de disponibilidade que as nossas ofertas de discos existentes. Um dos principais benefícios dos discos ultra é a capacidade de alterar dinamicamente o desempenho do SSD juntamente com as suas cargas de trabalho sem a necessidade de reiniciar os seus VMs. Os discos Ultra são indicados para cargas de trabalho com utilização intensa de dados, como o SAP HANA, base de dados de escalão superior e cargas de trabalho com bastantes transações.

## <a name="ga-scope-and-limitations"></a>Âmbito e limitações ga

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinar o tamanho do VM e a disponibilidade da região

Para alavancar os discos ultra, precisa determinar em que zona de disponibilidade está. Nem todas as regiões suportam todas as dimensões vm com discos ultra. Para determinar se a sua região, zona e tamanho VM suportam discos ultra discos, execute qualquer um dos seguintes comandos, certifique-se de substituir a **região**, **vmSize**, e valores de **subscrição** primeiro:

CLI:

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

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

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Implementar um disco ultra utilizando o Gestor de Recursos Azure

Primeiro, determine o tamanho vm para implantar. Para obter uma lista de tamanhos VM suportados, consulte a secção [de possibilidades e limitações da GA.](#ga-scope-and-limitations)

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
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
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

## <a name="next-steps"></a>Passos seguintes

Se quiser experimentar o novo tipo de disco [pedir acesso com este inquérito](https://aka.ms/UltraDiskSignup).
