---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3f910a3d0466153bd60fe23ef2f9f656cac292ee
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919727"
---
# <a name="using-azure-ultra-disks"></a>Usando os ultra discos do Azure

Os ultra discos do Azure oferecem alta taxa de transferência, IOPS alta e armazenamento de disco consistente de baixa latência para VMs (máquinas virtuais) IaaS do Azure. Essa nova oferta fornece a parte superior do desempenho de linha nos mesmos níveis de disponibilidade que nossas ofertas de discos existentes. Um grande benefício dos ultra discos é a capacidade de alterar dinamicamente o desempenho do SSD junto com suas cargas de trabalho sem a necessidade de reiniciar suas VMs. Ultra discos são adequados para cargas de trabalho com uso intensivo de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho com transações pesadas.

## <a name="check-if-your-subscription-has-access"></a>Verifique se sua assinatura tem acesso

Se você já se inscreveu para ultra disks e deseja verificar se sua assinatura está habilitada para ultra discos, use um dos seguintes comandos: 

CLI`az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell: `Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

Se sua assinatura estiver habilitada, a saída deverá ter uma aparência semelhante a esta:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>Determinar sua zona de disponibilidade

Depois de aprovado, você precisa determinar em qual zona de disponibilidade você está, para usar ultra disks. Execute um dos seguintes comandos para determinar em qual zona implantar o ultra Disk, substitua primeiro os valores **Region**, **vmSize**e **Subscription** :

CLI:

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

A resposta será semelhante ao formulário abaixo, em que X é a zona a ser usada para implantação na sua região escolhida. X poderia ser 1, 2 ou 3. Atualmente, apenas três regiões dão suporte a ultra disks, elas são: Leste dos EUA 2, Sudeste Asiático e Europa Setentrional.

Preservar o valor de **zonas** , representa sua zona de disponibilidade e você precisará dela para implantar um ultra Disk.

|ResourceType  |Name  |Location  |Zonas  |Restrição  |Funcionalidade  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|discos     |UltraSSD_LRS         |eualeste2         |X         |         |         |         |

> [!NOTE]
> Se não houver resposta do comando, o registro para o recurso ainda estará pendente ou você estará usando uma versão antiga da CLI ou do PowerShell.

Agora que você sabe em qual zona implantar, siga as etapas de implantação neste artigo para implantar uma VM com um ultra Disk anexado ou anexar um ultra Disk a uma VM existente.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Implantar um ultra Disk usando o Azure Resource Manager

Primeiro, determine o tamanho da VM a ser implantado. Por enquanto, somente as famílias de VM DsV3 e EsV3 dão suporte a ultra disks. Consulte a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter detalhes adicionais sobre esses tamanhos de VM.

Se você quiser criar uma VM com vários ultra discos, consulte o exemplo [criar uma VM com vários ultra discos](https://aka.ms/ultradiskArmTemplate).

Se você pretende usar seu próprio modelo, certifique-se de que apiVersion `Microsoft.Compute/virtualMachines` para `Microsoft.Compute/Disks` e esteja definido `2018-06-01` como (ou posterior).

Defina a SKU do disco como **UltraSSD_LRS**, em seguida, defina a capacidade do disco, o IOPS, a zona de disponibilidade e a taxa de transferência em Mbps para criar um ultra Disk.

Depois que a VM for provisionada, você poderá particionar e formatar os discos de dados e configurá-los para suas cargas de trabalho.

## <a name="deploy-an-ultra-disk-using-cli"></a>Implantar um ultra Disk usando a CLI

Primeiro, determine o tamanho da VM a ser implantado. Por enquanto, somente as famílias de VM DsV3 e EsV3 dão suporte a ultra disks. Consulte a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter detalhes adicionais sobre esses tamanhos de VM.

Você deve criar uma VM que seja capaz de usar ultra disks, a fim de anexar um ultra Disk.

Substitua ou defina as **$vmname**, **$rgname**, **$diskname**, **$Location**, **$password**$user **variáveis com** seus próprios valores. Defina **$Zone** para o valor da zona de disponibilidade obtida do [início deste artigo](#determine-your-availability-zone). Em seguida, execute o seguinte comando da CLI para criar uma VM ultra habilitada:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Criar um ultra Disk usando a CLI

Agora que você tem uma VM capaz de conectar ultra disks, você pode criar e anexar um ultra Disk a ele.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Anexar um ultra Disk a uma VM usando a CLI

Como alternativa, se sua VM existente estiver em uma zona de região/disponibilidade que seja capaz de usar ultra disks, você poderá usar ultra discos sem precisar criar uma nova VM.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>Ajustar o desempenho de um ultra Disk usando a CLI

Ultra disks oferece um recurso exclusivo que permite que você ajuste seu desempenho, o comando a seguir descreve como usar esse recurso:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Implantar um ultra Disk usando o PowerShell

Primeiro, determine o tamanho da VM a ser implantado. Por enquanto, somente as famílias de VM DsV3 e EsV3 dão suporte a ultra disks. Consulte a segunda tabela neste [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) para obter detalhes adicionais sobre esses tamanhos de VM.

Para usar ultra disks, você deve criar uma VM que seja capaz de usar ultra disks. Substitua ou defina as variáveis **$resourcegroup** e **$vmName** com seus próprios valores. Defina **$Zone** para o valor da zona de disponibilidade obtida do [início deste artigo](#determine-your-availability-zone). Em seguida, execute o seguinte comando [New-AzVm](/powershell/module/az.compute/new-azvm) para criar uma VM ultra habilitada:

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

### <a name="create-an-ultra-disk-using-powershell"></a>Criar um ultra Disk usando o PowerShell

Agora que você tem uma VM capaz de usar ultra disks, você pode criar e anexar um ultra Disk a ele:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Anexar um ultra Disk a uma VM usando o PowerShell

Como alternativa, se sua VM existente estiver em uma zona de região/disponibilidade que seja capaz de usar ultra disks, você poderá usar ultra discos sem precisar criar uma nova VM.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Ajustar o desempenho de um ultra Disk usando o PowerShell

Ultra discos têm um recurso exclusivo que permite que você ajuste o desempenho, o comando a seguir é um exemplo que ajusta o desempenho sem precisar desanexar o disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>Passos Seguintes

Se você quiser experimentar o novo tipo de disco, [solicite acesso a essa pesquisa](https://aka.ms/UltraDiskSignup).