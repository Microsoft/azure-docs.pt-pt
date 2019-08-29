---
title: Usar o armazenamento Premium do Azure com o SQL Server | Microsoft Docs
description: Este artigo usa recursos criados com o modelo de implantação clássico e fornece orientação sobre como usar o armazenamento Premium do Azure com SQL Server em execução em máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ebcfa9da8fc8760fa4c13cec1a8921c4ecef5691
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101942"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizar o Armazenamento Premium do Azure com o SQL Server em Máquinas Virtuais

## <a name="overview"></a>Descrição geral

O [SSDs Premium do Azure](../disks-types.md) é a próxima geração de armazenamento que fornece baixa latência e e/s de alta taxa de transferência. Ele funciona melhor para cargas de trabalho com uso intensivo de e/s de chave, como SQL Server em [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/)IaaS.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo fornece planejamento e orientação para a migração de uma máquina virtual que executa o SQL Server para usar o armazenamento Premium. Isso inclui a infraestrutura do Azure (rede, armazenamento) e as etapas de VM do Windows convidadas. O exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) mostra uma migração completa abrangente de ponta a ponta de como mover VMs maiores para tirar proveito do armazenamento SSD local aprimorado com o PowerShell.

É importante entender o processo de ponta a ponta de utilização do armazenamento Premium do Azure com SQL Server em VMs IAAS. Isto inclui:

* Identificação dos pré-requisitos para usar o armazenamento Premium.
* Exemplos de implantação de SQL Server em IaaS para armazenamento Premium para novas implantações.
* Exemplos de migração de implantações existentes, servidores autônomos e implantações usando grupos de disponibilidade do SQL Always On.
* Possíveis abordagens de migração.
* Exemplo completo de ponta a ponta mostrando as etapas do Azure, do Windows e do SQL Server para a migração de uma implementação de Always On existente.

Para obter mais informações básicas sobre SQL Server em máquinas virtuais do Azure, consulte [SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autorização** Daniel de **revisores técnicos** de sol: Luis Carlos Vargas Herring, Sanjay Mishra, Pravin MITAL, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para o armazenamento Premium

Há vários pré-requisitos para usar o armazenamento Premium.

### <a name="machine-size"></a>Tamanho da máquina

Para usar o armazenamento Premium, você precisa usar VMS (máquinas virtuais) da série DS. Se você não usou máquinas da série DS em seu serviço de nuvem antes, você deve excluir a VM existente, manter os discos anexados e, em seguida, criar um novo serviço de nuvem antes de recriar a VM como o tamanho da função DS *. Para obter mais informações sobre tamanhos de máquina virtual, consulte [tamanhos de máquina virtual e serviço de nuvem para o Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Serviços em nuvem

Você só pode usar VMs DS * com o armazenamento Premium quando elas são criadas em um novo serviço de nuvem. Se você estiver usando SQL Server Always On no Azure, o ouvinte Always On refere-se ao endereço IP de Load Balancer interno ou externo do Azure que está associado a um serviço de nuvem. Este artigo se concentra em como migrar e manter a disponibilidade nesse cenário.

> [!NOTE]
> Uma série DS * deve ser a primeira VM que é implantada no novo serviço de nuvem.
>
>

### <a name="regional-vnets"></a>VNETS regionais

Para VMs DS *, você deve configurar a rede virtual (VNET) hospedando suas VMs para serem regionais. Isso "amplia" a VNET é permitir que as VMs maiores sejam provisionadas em outros clusters e permitir a comunicação entre elas. Na captura de tela a seguir, o local realçado mostra VNETs regionais, enquanto o primeiro resultado mostra uma VNET "estreita".

![RegionalVNET][1]

Você pode gerar um tíquete de suporte da Microsoft para migrar para uma VNET regional. Em seguida, a Microsoft faz uma alteração. Para concluir a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração de rede. Primeiro, exporte a configuração de rede no PowerShell e, em seguida, substitua a propriedade **AffinityGroup** no elemento **VirtualNetworkSite** por uma propriedade **Location** . Especifique `Location = XXXX` onde`XXXX` é uma região do Azure. Em seguida, importe a nova configuração.

Por exemplo, considerando a seguinte configuração de VNET:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Para mover isso para uma VNET regional no Europa Ocidental, altere a configuração para o seguinte:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Contas de armazenamento

Você precisa criar uma nova conta de armazenamento configurada para o armazenamento Premium. Observe que o uso do armazenamento Premium é definido na conta de armazenamento, não em VHDs individuais, no entanto, ao usar uma VM da série DS *, você pode anexar VHD das contas de armazenamento Premium e Standard. Você pode considerar isso se não quiser posicionar o VHD do sistema operacional na conta de armazenamento Premium.

O comando **New-AzureStorageAccountPowerShell** a seguir com o **tipo** "Premium_LRS" cria uma conta de armazenamento Premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Configurações de cache de VHDs

A principal diferença entre a criação de discos que fazem parte de uma conta de armazenamento Premium é a configuração de cache de disco. Para discos de volume de dados SQL Server, é recomendável que você use '**cache de leitura**'. Para volumes de log de transações, a configuração de cache de disco deve ser definida como '**None**'. Isso é diferente das recomendações para contas de armazenamento padrão.

Depois que os VHDs forem anexados, a configuração de cache não poderá ser alterada. Você precisa desanexar e anexar novamente o VHD com uma configuração de cache atualizada.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Você pode usar [espaços de armazenamento do Windows](https://technet.microsoft.com/library/hh831739.aspx) como fez com o armazenamento Standard anterior, isso permite que você MIGRE uma VM que já está utilizando espaços de armazenamento. O exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (etapa 9 e avançar) demonstra o código do PowerShell para extrair e importar uma VM com vários VHDs anexados.

Os pools de armazenamento foram usados com a conta de armazenamento do Azure padrão para melhorar a taxa de transferência e reduzir a latência. Você pode encontrar valor no teste de pools de armazenamento com o armazenamento Premium para novas implantações, mas elas adicionam complexidade adicional com a configuração de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como descobrir quais discos virtuais do Azure são mapeados para pools de armazenamento

Como há diferentes recomendações de configuração de cache para VHDs anexados, você pode optar por copiar os VHDs para uma conta de armazenamento Premium. No entanto, ao reanexá-los à nova VM da série DS, talvez seja necessário alterar as configurações de cache. É mais simples aplicar as configurações de cache recomendadas do armazenamento Premium quando você tem VHDs separados para os arquivos de dados do SQL e arquivos de log (em vez de um único VHD que contém ambos).

> [!NOTE]
> Se você tiver SQL Server arquivos de dados e de log no mesmo volume, a opção de cache que você escolher dependerá dos padrões de acesso de e/s para suas cargas de trabalho de banco de dados. Somente o teste pode demonstrar qual opção de cache é melhor para esse cenário.
>
>

No entanto, se você estiver usando espaços de armazenamento do Windows compostos por vários VHDs, precisará examinar seus scripts originais para identificar quais VHDs anexados estão em qual pool específico, para que você possa definir as configurações de cache adequadamente para cada disco.

Se você não tiver o script original disponível para mostrar quais VHDs são mapeados para o pool de armazenamento, você pode usar as etapas a seguir para determinar o mapeamento de disco/pool de armazenamento.

Para cada disco, use as seguintes etapas:

1. Obtenha a lista de discos anexados à VM com o comando **Get-AzureVM** :

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Anote o diskname e o LUN.

    ![DisknameAndLUN][2]
1. Área de trabalho remota na VM. Em seguida, vá para **Gerenciamento** | do computador**Device Manager** | **unidades de disco**. Examine as propriedades de cada um dos "discos virtuais da Microsoft"

    ![VirtualDiskProperties][3]
1. O número de LUN aqui é uma referência ao número de LUN que você especifica ao anexar o VHD à VM.
1. Para o ' disco virtual da Microsoft ', vá para a guia **detalhes** e, em seguida, na lista **Propriedade** , vá para **chave do driver**. No **valor**, observe o **deslocamento**, que é 0002 na captura de tela a seguir. O 0002 denota o PhysicalDisk2 que o pool de armazenamento faz referência.

    ![VirtualDiskPropertyDetails][4]
1. Para cada pool de armazenamento, despeje os discos associados:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Agora você pode usar essas informações para associar VHDs anexados a discos físicos em pools de armazenamento.

Depois de mapear VHDs para discos físicos em pools de armazenamento, você pode desanexá-los e copiá-los para uma conta de armazenamento Premium e, em seguida, anexá-los com a configuração de cache correta. Consulte o exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), as etapas 8 a 12. Estas etapas mostram como extrair uma configuração de disco VHD anexada à VM para um arquivo CSV, copiar os VHDs, alterar as configurações de cache de configuração de disco e, por fim, reimplantar a VM como uma VM da série DS com todos os discos anexados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Largura de banda de armazenamento de VM e taxa de transferência de armazenamento VHD

A quantidade de desempenho de armazenamento depende do tamanho de VM DS * especificado e dos tamanhos de VHD. As VMs têm diferentes concessões para o número de VHDs que podem ser anexados e a largura de banda máxima com suporte (MB/s). Para obter os números de largura de banda específicos, consulte [tamanhos de máquina virtual e serviço de nuvem para o Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

IOPS aumentadas são obtidos com tamanhos de disco maiores. Você deve considerar isso quando pensar em seu caminho de migração. Para obter detalhes, [consulte a tabela para IOPS e tipos de disco](../disks-types.md#premium-ssd).

Por fim, considere que as VMs têm larguras de banda máxima de disco diferentes que dão suporte a todos os discos anexados. Em alta carga, você poderia saturar a largura de banda de disco máxima disponível para o tamanho da função VM. Por exemplo, um Standard_DS14 dá suporte a até 512MB/s; Portanto, com três discos p30, você poderia saturar a largura de banda do disco da VM. Mas, neste exemplo, o limite de taxa de transferência pode ser excedido dependendo da combinação de IOs de leitura e gravação.

## <a name="new-deployments"></a>Novas implantações

As próximas duas seções demonstram como você pode implantar SQL Server VMs no armazenamento Premium. Conforme mencionado anteriormente, você não precisa necessariamente posicionar o disco do sistema operacional no armazenamento Premium. Você pode optar por fazer isso se estiver pretendendo posicionar cargas de trabalho de e/s intensivas no VHD do sistema operacional.

O primeiro exemplo demonstra o uso de imagens existentes da galeria do Azure. O segundo exemplo mostra como usar uma imagem de VM personalizada que você tem em uma conta de armazenamento padrão existente.

> [!NOTE]
> Esses exemplos pressupõem que você já criou uma VNET regional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar uma nova VM com o armazenamento Premium com imagem da Galeria

O exemplo a seguir mostra como posicionar o VHD do sistema operacional no armazenamento Premium e anexar VHDs de armazenamento Premium. No entanto, você também pode posicionar o disco do sistema operacional em uma conta de armazenamento padrão e anexar os VHDs que residem em uma conta de armazenamento Premium. Ambos os cenários são demonstrados.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Passo 1: Criar uma conta de armazenamento Premium

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>Passo 2: Criar um novo serviço de nuvem

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passo 3: Reservar um VIP de serviço de nuvem (opcional)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Passo 4: Criar um contêiner de VM

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passo 5: Colocando o VHD do sistema operacional no armazenamento Standard ou Premium

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>Passo 6: Criar VM

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Criar uma nova VM para usar o armazenamento Premium com uma imagem personalizada

Este cenário demonstra onde você tem imagens personalizadas existentes que residem em uma conta de armazenamento padrão. Conforme mencionado, se você quiser posicionar o VHD do sistema operacional no armazenamento Premium, precisará copiar a imagem que existe na conta de armazenamento padrão e transferi-la para um armazenamento Premium antes que ela possa ser usada. Se você tiver uma imagem local, também poderá usar esse método para copiá-la diretamente para a conta de armazenamento Premium.

#### <a name="step-1-create-storage-account"></a>Passo 1: Criar conta de armazenamento

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Etapa 2 criar serviço de nuvem

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Passo 3: Usar imagem existente

Você pode usar uma imagem existente. Ou, você pode [tirar uma imagem de uma máquina existente](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Observe que a máquina em que a imagem não precisa ser um computador DS *. Quando você tiver a imagem, as etapas a seguir mostram como copiá-la para a conta de armazenamento Premium com o commandlet do PowerShell **Start-AzureStorageBlobCopy** .

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passo 4: Copiar blob entre contas de armazenamento

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Passo 5: Verificar regularmente o status da cópia:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passo 6: Adicionar disco de imagem ao repositório de disco do Azure na assinatura

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Você pode descobrir que, mesmo que o status relate como êxito, ainda poderá obter um erro de concessão de disco. Nesse caso, aguarde cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Passo 7:  Criar a VM

Aqui você está criando a VM a partir de sua imagem e anexando dois VHDs de armazenamento Premium:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implantações existentes que não usam Always On grupos de disponibilidade

> [!NOTE]
> Para implantações existentes, primeiro consulte a seção [pré-requisitos](#prerequisites-for-premium-storage) deste artigo.

Há diferentes considerações para implantações de SQL Server que não usam Always On grupos de disponibilidade e as que fazem. Se você não estiver usando Always On e tiver um SQL Server autônomo existente, poderá atualizar para o armazenamento Premium usando um novo serviço de nuvem e conta de armazenamento. Considere as seguintes opções:

* **Crie uma nova VM SQL Server**. Você pode criar um novo SQL Server VM que usa uma conta de armazenamento Premium, conforme documentado em novas implantações. Em seguida, faça backup e restaure seus bancos de dados de usuário e configuração de SQL Server. O aplicativo precisa ser atualizado para referenciar o novo SQL Server se ele estiver sendo acessado internamente ou externamente. Você precisaria copiar todos os objetos ' fora do banco de de BD ' como se estivesse fazendo uma migração de SQL Server SxS (lado a lado). Isso inclui objetos como logons, certificados e servidores vinculados.
* **Migre uma VM SQL Server existente**. Isso requer que o SQL Server VM fique offline e, em seguida, transferi-lo para um novo serviço de nuvem, o que inclui copiar todos os seus VHDs anexados para a conta de armazenamento Premium. Quando a VM fica online, o aplicativo faz referência ao nome de host do servidor como antes. Lembre-se de que o tamanho do disco existente afeta as características de desempenho. Por exemplo, um disco de 400 GB é arredondado para um P20. Se você souber que não requer esse desempenho de disco, poderá recriar a VM como uma VM da série DS e anexar VHDs de armazenamento Premium da especificação de tamanho/desempenho que você precisa. Em seguida, você poderia desanexar e anexar novamente os arquivos do banco de BD SQL.

> [!NOTE]
> Ao copiar os discos VHD, você deve estar ciente do tamanho, dependendo do tamanho que o tipo de disco de armazenamento Premium que eles enquadram, isso determina a especificação de desempenho de disco. O Azure arredonda até o tamanho de disco mais próximo, portanto, se você tiver um disco de 400 GB, isso será arredondado para um P20. Dependendo dos requisitos de e/s existentes do VHD do sistema operacional, talvez não seja necessário migrá-lo para uma conta de armazenamento Premium.

Se seu SQL Server for acessado externamente, o VIP do serviço de nuvem será alterado. Você também precisa atualizar pontos de extremidade, ACLs e configurações de DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implantações existentes que usam Always On grupos de disponibilidade

> [!NOTE]
> Para implantações existentes, primeiro consulte a seção [pré-requisitos](#prerequisites-for-premium-storage) deste artigo.

Inicialmente nesta seção, veremos como Always On interage com a rede do Azure. Em seguida, dividimos as migrações em dois cenários: migrações em que algum tempo de inatividade pode ser tolerado e migrações em que você deve atingir o tempo de inatividade mínimo.

O SQL Server local Always On grupos de disponibilidade usam um ouvinte local que registra um nome DNS virtual junto com um endereço IP que é compartilhado entre um ou mais SQL Servers. Quando os clientes se conectam, eles são roteados pelo IP do ouvinte para o SQL Server primário. Esse é o servidor que possui a Always On recurso IP nesse momento.

![DeploymentsUseAlways em][6]

No Microsoft Azure você pode ter apenas um endereço IP atribuído a uma NIC na VM, portanto, para obter a mesma camada de abstração que o local, o Azure utiliza o endereço IP atribuído aos balanceadores de carga internos/externos (ILB/ELB). O recurso de IP compartilhado entre os servidores é definido para o mesmo IP que o ILB/ELB. Isso é publicado no DNS e o tráfego do cliente é transmitido por meio de ILB/ELB para a réplica de SQL Server primária. O ILB/ELB sabe qual SQL Server é o primário, pois usa investigações para investigar o recurso de IP Always On. No exemplo anterior, ele investiga cada nó que tem um ponto de extremidade referenciado pelo ELB/ILB, o que responde é o SQL Server primário.

> [!NOTE]
> Os ILB e ELB são atribuídos a um serviço de nuvem do Azure específico, portanto, qualquer migração na nuvem no Azure provavelmente significa que o Load Balancer IP é alterado.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrando implantações Always On que podem permitir algum tempo de inatividade

Há duas estratégias para migrar Always On implantações que permitem algum tempo de inatividade:

1. **Adicionar mais réplicas secundárias a um cluster de Always On existente**
2. **Migrar para um novo cluster Always On**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Adicionar mais réplicas secundárias a um cluster de Always On existente

Uma estratégia é adicionar mais secundários ao grupo de disponibilidade Always On. Você precisa adicioná-los em um novo serviço de nuvem e atualizar o ouvinte com o novo IP do balanceador de carga.

##### <a name="points-of-downtime"></a>Pontos de inatividade:

* Validação de cluster.
* Testando failovers de Always On para novos secundários.

Se você estiver usando pools de armazenamento do Windows na VM para maior taxa de transferência de e/s, eles serão colocados offline durante uma validação de cluster completa. O teste de validação é necessário quando você adiciona nós ao cluster. O tempo necessário para executar o teste pode variar, de modo que você deve testá-lo em seu ambiente de teste representativo para obter um tempo aproximado de quanto isso leva.

Você deve provisionar o tempo em que você pode executar o failover manual e o teste de caos nos nós recém-adicionados para garantir Always On funções de alta disponibilidade conforme o esperado.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Você deve interromper todas as instâncias de SQL Server em que os pools de armazenamento são usados antes da execução da validação.
>
> ##### <a name="high-level-steps"></a>Etapas de alto nível
>

1. Crie dois novos SQL Servers no novo serviço de nuvem com o armazenamento Premium anexado.
2. Copie sobre backups completos e restaure with **NORECOVERY**.
3. Copiar sobre objetos dependentes de ' banco de usuários fora do usuário ', como logons etc.
4. Crie um novo Load Balancer interno (ILB) ou use um Load Balancer externo (ELB) e, em seguida, configure os pontos de extremidade com balanceamento de carga em ambos os nós novos.

   > [!NOTE]
   > Verifique se todos os nós têm a configuração de ponto de extremidade correta antes de continuar
   >
   >
5. Interrompa o acesso de usuário/aplicativo ao SQL Server (se estiver usando pools de armazenamento).
6. Pare SQL Server serviços de mecanismo em todos os nós (se estiver usando pools de armazenamento).
7. Adicione novos nós ao cluster e execute a validação completa.
8. Depois que a validação for bem-sucedida, inicie todos os serviços de SQL Server.
9. Fazer backup de logs de transações e restaurar bancos de dados de usuário.
10. Adicione novos nós ao grupo de disponibilidade Always On e coloque a replicação em **sincronia**.
11. Adicione o recurso de endereço IP do novo serviço de nuvem ILB/ELB por meio do PowerShell para Always On com base no exemplo de vários sites no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). No clustering do Windows, defina os **possíveis proprietários** do recurso de **endereço IP** para os novos nós antigos. Consulte a seção "adicionando recurso de endereço IP na mesma sub-rede" do [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Failover para um dos novos nós.
13. Faça os novos nós parceiros de failover automático e failovers de teste.
14. Remova os nós originais do grupo de disponibilidade.

##### <a name="advantages"></a>Vantagens

* Novos SQL Servers podem ser testados (SQL Server e aplicativo) antes de serem adicionados ao Always On.
* Você pode alterar o tamanho da VM e personalizar o armazenamento para seus requisitos exatos. No entanto, seria benéfico manter todos os caminhos de arquivo SQL iguais.
* Você pode controlar quando a transferência dos backups de BD para as réplicas secundárias é iniciada. Isso é diferente de usar o commandlet **Start-AzureStorageBlobCopy** do Azure para copiar VHDs, pois essa é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens

* Ao usar pools de armazenamento do Windows, há tempo de inatividade do cluster durante a validação completa do cluster para os novos nós adicionais.
* Dependendo da versão do SQL Server e do número existente de réplicas secundárias, talvez você não consiga adicionar mais réplicas secundárias sem remover os secundários existentes.
* Pode haver tempo de transferência de dados SQL longo durante a configuração dos secundários.
* Há custo adicional durante a migração, enquanto você tem novos computadores em execução em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar para um novo cluster Always On

Outra estratégia é criar um cluster de Always On totalmente novo com nós totalmente novos no novo serviço de nuvem e, em seguida, redirecionar os clientes para usá-lo.

##### <a name="points-of-downtime"></a>Pontos de inatividade

Há um tempo de inatividade quando você transfere aplicativos e usuários para o novo ouvinte de Always On. O tempo de inatividade depende de:

* O tempo necessário para restaurar os backups de log de transações finais para bancos de dados em novos servidores.
* O tempo necessário para atualizar os aplicativos cliente para usar o novo ouvinte de Always On.

##### <a name="advantages"></a>Vantagens

* Você pode testar o ambiente de produção real, SQL Server e alterações de Build do sistema operacional.
* Você tem a opção de personalizar o armazenamento e reduzir potencialmente o tamanho da VM. Isso pode resultar em redução de custos.
* Você pode atualizar seu SQL Server Build ou versão durante esse processo. Você também pode atualizar o sistema operacional.
* O cluster de Always On anterior pode atuar como um destino de reversão sólido.

##### <a name="disadvantages"></a>Desvantagens

* Você precisa alterar o nome DNS do ouvinte se desejar que ambos os clusters Always On executados simultaneamente. Isso adiciona sobrecarga de administração durante a migração, pois as cadeias de caracteres do aplicativo cliente devem refletir o novo nome do ouvinte.
* Você deve implementar um mecanismo de sincronização entre os dois ambientes para mantê-los o mais próximo possível para minimizar os requisitos de sincronização final antes da migração.
* Há um custo adicional durante a migração enquanto você tem o novo ambiente em execução.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrando implantações Always On para tempo de inatividade mínimo

Há duas estratégias para migrar implantações Always On para tempo de inatividade mínimo:

1. **Utilizar um secundário existente: Site único**
2. **Utilizar réplicas secundárias existentes: Multissite**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilizar um secundário existente: Site único

Uma estratégia para tempo de inatividade mínimo é pegar um secundário de nuvem existente e removê-lo do serviço de nuvem atual. Em seguida, copie os VHDs para a nova conta de armazenamento Premium e crie a VM no novo serviço de nuvem. Em seguida, atualize o ouvinte em clustering e failover.

##### <a name="points-of-downtime"></a>Pontos de inatividade

* Há um tempo de inatividade quando você atualiza o nó final com o ponto de extremidade com balanceamento de carga.
* A reconexão do cliente pode ser adiada dependendo da configuração do cliente/DNS.
* Haverá tempo de inatividade adicional se você optar por colocar o Always On grupo de clusters offline para trocar os endereços IP. Você pode evitar isso usando uma dependência OR e possíveis proprietários para o recurso de endereço IP adicionado. Consulte a seção "adicionando recurso de endereço IP na mesma sub-rede" do [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Quando desejar que o nó adicionado adicional participe como um parceiro de failover Always On, você precisará adicionar um Ponto de Extremidade do Azure com uma referência ao conjunto de balanceamento de carga. Quando você executa o comando **Add-AzureEndpoint** para fazer isso, as conexões atuais permanecem abertas, mas não é possível estabelecer novas conexões com o ouvinte até que o balanceador de carga seja atualizado. No teste, isso foi visto para os últimos 90 120seconds, isso deve ser testado.

##### <a name="advantages"></a>Vantagens

* Nenhum custo adicional incorrido durante a migração.
* Uma migração de um para um.
* Complexidade reduzida.
* Permite um aumento de IOPS de SKUs de armazenamento Premium. Quando os discos são desanexados da VM e copiados para o novo serviço de nuvem, uma ferramenta de terceiros pode ser usada para aumentar o tamanho do VHD, que fornece mais taxas de transferência. Para aumentar os tamanhos de VHD, consulte esta [discussão sobre o fórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desvantagens

* Há uma perda temporária de HA e DR durante a migração.
* Como essa é uma migração de 1:1, você precisa usar um tamanho mínimo de VM que dá suporte ao seu número de VHDs, portanto, talvez você não consiga diminuir suas VMs.
* Esse cenário usaria o commandlet **Start-AzureStorageBlobCopy** do Azure, que é assíncrono. Não há SLA na conclusão da cópia. A hora das cópias varia, enquanto isso depende da espera na fila, e também depende da quantidade de dados a serem transferidos. O tempo de cópia aumenta se a transferência for para outra data center do Azure que dá suporte ao armazenamento Premium em outra região. Se você tiver apenas dois nós, considere uma possível mitigação caso a cópia demore mais do que no teste. Isso pode incluir as ideias a seguir.
  * Adicione um nó temporário de 3º SQL Server para HA antes da migração com tempo de inatividade acordado.
  * Execute a migração fora da manutenção agendada do Azure.
  * Verifique se você configurou o quorum de cluster corretamente.  

##### <a name="high-level-steps"></a>Etapas de alto nível

Este documento não demonstra um exemplo completo de ponta a ponta, no entanto, o [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fornece detalhes que podem ser aproveitados para fazer isso.

![MinimalDowntime][8]

* Reúna a configuração de disco e remova o nó (não exclua VHDs anexados).
* Criar uma conta de armazenamento Premium e copiar VHDs da conta de armazenamento padrão
* Crie um novo serviço de nuvem e reimplante a VM SQL2 nesse serviço de nuvem. Crie a VM usando o VHD do sistema operacional original copiado e anexando os VHDs copiados.
* Configure ILB/ELB e adicione pontos de extremidade.
* Atualize o ouvinte:
  * Colocar o grupo de Always On offline e atualizar o ouvinte de Always On com o novo endereço IP ILB/ELB.
  * Ou adicione o recurso de endereço IP do novo serviço de nuvem ILB/ELB por meio do PowerShell no clustering do Windows. Em seguida, defina os possíveis proprietários do recurso de endereço IP para o nó migrado, SQL2, e defina isso como ou dependência no nome da rede. Consulte a seção "adicionando recurso de endereço IP na mesma sub-rede" do [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Verifique a configuração/propagação de DNS para os clientes.
* Migre a VM SQL1 e percorra as etapas 2 a 4.
* Se estiver usando as etapas 5II, adicione SQL1 como um possível proprietário para o recurso de endereço IP adicionado
* Failovers de teste.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilizar réplicas secundárias existentes: Multilocal

Se você tiver nós em mais de um datacenter (DC) do Azure ou se tiver um ambiente híbrido, poderá usar uma configuração de Always On nesse ambiente para minimizar o tempo de inatividade.

A abordagem é alterar a sincronização de Always On para síncrona para o controlador de domínio do Azure local ou secundário e, em seguida, fazer failover para esse SQL Server. Em seguida, copie os VHDs para uma conta de armazenamento Premium e reimplante o computador em um novo serviço de nuvem. Atualize o ouvinte e faça failback.

##### <a name="points-of-downtime"></a>Pontos de inatividade

O tempo de inatividade consiste no momento de realizar o failover para o controlador de domínio alternativo e vice-versa. Ele também depende de sua configuração de cliente/DNS e a reconexão do cliente pode ser atrasada.
Considere o seguinte exemplo de uma configuração de Always On híbrida:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

* Você pode utilizar a infraestrutura existente.
* Você tem a opção de atualizar previamente o armazenamento do Azure no controlador de domínio de DR do Azure primeiro.
* O armazenamento do DC do Azure do DR pode ser reconfigurado.
* Há um mínimo de dois failovers durante a migração, excluindo failovers de teste.
* Você não precisa mover SQL Server dados com backup e restauração.

##### <a name="disadvantages"></a>Desvantagens

* Dependendo do acesso do cliente ao SQL Server, pode haver maior latência quando SQL Server está sendo executado em um controlador de domínio alternativo para o aplicativo.
* O tempo de cópia dos VHDs para o armazenamento Premium poderia ser longo. Isso pode afetar sua decisão sobre se deseja manter o nó no grupo de disponibilidade. Considere isso para quando houver necessidade de cargas de trabalho com uso intensivo de log durante a migração, pois o nó primário precisa manter as transações não replicadas em seu log de transações. Portanto, isso pode crescer significativamente.
* Esse cenário usaria o commandlet **Start-AzureStorageBlobCopy** do Azure, que é assíncrono. Não há nenhum SLA na conclusão. A hora das cópias varia, enquanto isso depende da espera na fila, mas também depende da quantidade de dados a serem transferidos. Portanto, você tem apenas um nó em seu 2º data center, deve tomar etapas de mitigação caso a cópia demore mais do que no teste. Essas etapas de mitigação incluem as seguintes ideias:
  * Adicione um segundo nó do SQL temporário para HA antes da migração com tempo de inatividade acordado.
  * Execute a migração fora da manutenção agendada do Azure.
  * Verifique se você configurou o quorum de cluster corretamente.

Este cenário pressupõe que você tenha documentado sua instalação e saiba como o armazenamento é mapeado para fazer alterações nas configurações de cache de disco ideais.

##### <a name="high-level-steps"></a>Etapas de alto nível

![Multisite2][10]

* Torne o controlador de domínio do Azure local/alternativo o SQL Server primário e torne-o o outro do AFP (parceiro de failover automático).
* Colete informações de configuração de disco do SQL2 e remova o nó (não exclua VHDs anexados).
* Crie uma conta de armazenamento Premium e copie os VHDs da conta de armazenamento padrão.
* Crie um novo serviço de nuvem e crie a VM SQL2 com seus discos de armazenamento Premium anexados.
* Configure ILB/ELB e adicione pontos de extremidade.
* Atualize o ouvinte de Always On com o novo endereço IP ILB/ELB e o failover de teste.
* Verifique a configuração de DNS.
* Altere o AFP para SQL2 e, em seguida, migre SQL1 e passe pelas etapas 2 a 5.
* Failovers de teste.
* Alterne o AFP de volta para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Anexo Migrando um cluster de Always On multissite para o armazenamento Premium

O restante deste artigo fornece um exemplo detalhado de como converter um cluster de Always On multissite para o armazenamento Premium. Ele também converte o ouvinte de usar um ELB (balanceador de carga externo) para um ILB (balanceador de carga interno).

### <a name="environment"></a>Ambiente

* 2K12 2K12/SQL do Windows
* 1 arquivos de BD no SP
* 2 x pools de armazenamento por nó

![Appendix1][11]

### <a name="vm"></a>VM

Neste exemplo, vamos demonstrar a mudança de um ELB para ILB. ELB estava disponível antes de ILB, portanto, isso mostra como mudar para ILB durante a migração.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pré-etapas: Conectar à assinatura

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Passo 1: Criar nova conta de armazenamento e serviço de nuvem

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passo 2: Aumentar as falhas permitidas em \<recursos opcionais >

Em determinados recursos que pertencem ao seu grupo de disponibilidade Always On, há limites de quantas falhas podem ocorrer em um período, em que o serviço de cluster tenta reiniciar o grupo de recursos. É recomendável que você aumente isso enquanto estiver percorrendo esse procedimento, já que, se você não fizer failover manualmente e disparar failovers desligando os computadores, poderá ficar próximo desse limite.

Seria prudente dobrar a permissão de falha, fazer isso em Gerenciador de Cluster de Failover, ir para as propriedades do grupo de recursos Always On:

![Appendix3][13]

Altere o máximo de falhas para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passo 3: O recurso de endereço IP de adição \<para o grupo de cluster opcional >

Se você tiver apenas um endereço IP para o grupo de clusters e isso estiver alinhado à sub-rede na nuvem, tenha cuidado se você acidentalmente colocar offline todos os nós de cluster na nuvem nessa rede, o recurso de IP do cluster e o nome da rede do cluster não poderão ficar online. Nessa situação, ele impede atualizações em outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Passo 4: Configuração do DNS

A implementação de uma transição suave depende de como o DNS está sendo utilizado e atualizado.
Quando o Always On for instalado, ele criará um grupo de recursos de cluster do Windows, se você abrir Gerenciador de Cluster de Failover, verá que, no mínimo, ele tem três recursos, os dois aos quais o documento se refere são:

* VNN (nome da rede virtual) – o nome DNS ao qual os clientes se conectam quando deseja se conectar a servidores SQL por meio de Always On.
* Recurso de endereço IP – o endereço IP associado ao VNN, você pode ter mais de um e, em uma configuração multissite, você tem um endereço IP por site/sub-rede.

Ao se conectar ao SQL Server, o driver de cliente do SQL Server recupera os registros DNS associados ao ouvinte e tenta se conectar a cada Always On endereço IP associado. Em seguida, discutiremos alguns fatores que podem influenciar isso.

O número de registros DNS simultâneos associados ao nome do ouvinte depende não apenas do número de endereços IP associados, mas ' Registerallipproviders ' no clustering de failover para o recurso VNN Always ON.

Quando você implanta Always On no Azure, há diferentes etapas para criar o ouvinte e os endereços IP. você precisa configurar manualmente o ' RegisterAllIpProviders ' como 1, isso é diferente para uma implantação de Always On local onde ele já está definido como 1.

Se ' RegisterAllIpProviders ' for 0, você verá apenas um registro DNS no DNS associado ao ouvinte:

![Appendix4][14]

Se ' RegisterAllIpProviders ' for 1:

![Appendix5][15]

O código a seguir despeja as configurações de VNN e as define para você. Para que a alteração entre em vigor, você precisa colocar o VNN offline e ativá-lo novamente. Isso coloca o ouvinte offline causando interrupção de conectividade do cliente.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Em uma etapa de migração posterior, você precisa atualizar o ouvinte de Always On com um endereço IP atualizado que faz referência a um balanceador de carga, isso envolve uma remoção e adição de recursos de endereço IP. Após a atualização de IP, você precisa garantir que o novo endereço IP tenha sido atualizado na zona DNS e que os clientes estejam atualizando seu cache DNS local.

Se os clientes residirem em um segmento de rede diferente e fizerem referência a um servidor DNS diferente, você precisará considerar o que acontece com a transferência de zona DNS durante a migração, pois o tempo de reconexão do aplicativo é restrito pelo menos pelo tempo de transferência de zona de qualquer novo IP endereços para o ouvinte. Se você estiver sob a restrição de tempo aqui, você deve discutir e testar a força de uma transferência de zona incremental com suas equipes do Windows e também colocar o registro do host DNS em uma TTL (vida útil) menor, para que os clientes atualizem. Para obter mais informações, consulte [transferências de zona incrementais](https://technet.microsoft.com/library/cc958973.aspx) e [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Por padrão, o TTL do registro DNS que está associado ao ouvinte no Always On no Azure é de 1200 segundos. Talvez você queira reduzir isso se estiver sob restrição de tempo durante a migração para garantir que os clientes atualizem seu DNS com o endereço IP atualizado para o ouvinte. Você pode ver e modificar a configuração despejando a configuração do VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Quanto menor for o ' HostRecordTTL ', ocorrerá uma maior quantidade de tráfego DNS.

##### <a name="client-application-settings"></a>Configurações do aplicativo cliente

Se o seu aplicativo cliente SQL der suporte ao .NET 4,5 SqlClient, você poderá usar a palavra-chave ' MULTISUBNETFAILOVER = TRUE '. Essa palavra-chave deve ser aplicada, pois permite uma conexão mais rápida com o grupo de disponibilidade do SQL Always On durante o failover. Ele enumera todos os endereços IP associados ao ouvinte de Always On em paralelo e executa uma velocidade de repetição de conexão TCP mais agressiva durante um failover.

Para obter mais informações sobre as configurações anteriores, consulte [palavra-chave MultiSubnetFailover e recursos associados](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [suporte a SqlClient para alta disponibilidade e recuperação de desastre](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Passo 5: Configurações de quorum do cluster

Como você vai retirar pelo menos um SQL Server por vez, você deve modificar a configuração de quorum do cluster, se estiver usando a FSW (testemunha de compartilhamento de arquivos) com dois nós, você deve definir o quorum para permitir a maioria do nó e utilizar a votação dinâmica , permitindo que um único nó permaneça em pé.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Para obter mais informações sobre como gerenciar e configurar o quorum do cluster, consulte [configurar e gerenciar o quorum em um cluster de failover do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Passo 6: Extrair pontos de extremidade e ACLs existentes

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Salve este texto em um arquivo.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passo 7: Alterar parceiros de failover e modos de replicação

Se você tiver mais de dois SQL Servers, deverá alterar o failover de outro secundário em outro controlador de domínio ou local para "síncrono" e torná-lo um parceiro de failover automático (AFP), isto é, para manter a HA enquanto você estiver fazendo alterações. Você pode fazer isso por meio de TSQL de modificar por meio do SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passo 8: Remover VM secundária do serviço de nuvem

Você deve estar planejando migrar primeiro um nó secundário da nuvem. Se esse nó for primário no momento, você deverá iniciar um failover manual.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 9: Alterar as configurações de cache de disco no arquivo CSV e salvar

Para volumes de dados, eles devem ser definidos como READONLY.

Para volumes TLOG, eles devem ser definidos como nenhum.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Etapa 10: Copiar VHDS

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Você pode verificar o status da cópia dos VHDs para a conta de armazenamento Premium:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

Aguarde até que todos eles sejam registrados como êxito.

Para obter informações para BLOBs individuais:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Etapa 11: Registrar disco do sistema operacional

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Etapa 12: Importar secundário para o novo serviço de nuvem

O código a seguir também usa a opção adicionada aqui, você pode importar o computador e usar o VIP que possa ser mantido.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Etapa 13: Criar ILB em Nova nuvem svc, adicionar pontos de extremidade com balanceamento de carga e ACLs

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>Etapa 14: Atualizar Always On

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

Agora, remova o endereço IP do serviço de nuvem antigo.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Etapa 15: Verificação de atualização de DNS

Agora você deve verificar os servidores DNS em suas redes SQL Server Client e certificar-se de que o clustering adicionou o registro de host extra para o endereço IP adicionado. Se esses servidores DNS não tiverem sido atualizados, considere forçar uma transferência de zona DNS e garantir que os clientes nessa sub-rede sejam capazes de resolver para ambos os endereços IP Always On, isso é para que você não precise esperar pela replicação automática de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Etapa 16: Reconfigurar Always On

Neste ponto, você aguarda o secundário que o nó foi migrado para ressincronizar totalmente com o nó local e alternar para o nó de replicação síncrona e torná-lo o AFP.  

#### <a name="step-17-migrate-second-node"></a>Etapa 17: Migrar segundo nó

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Etapa 18: Alterar as configurações de cache de disco no arquivo CSV e salvar

Para volumes de dados, as configurações de cache devem ser definidas como READONLY.

Para volumes TLOG, as configurações de cache devem ser definidas como NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Etapa 19: Criar nova conta de armazenamento independente para o nó secundário

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>Etapa 20: Copiar VHDS

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Você pode verificar o status da cópia do VHD para todos os VHDs:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

Aguarde até que todos eles sejam registrados como êxito.

Para obter informações para BLOBs individuais:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Etapa 21: Registrar disco do sistema operacional

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Etapa 22: Adicionar pontos de extremidade com balanceamento de carga e ACLs

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>Etapa 23: Ativação pós-falha de teste

Aguarde até que o nó migrado seja sincronizado com o nó de Always On local. Coloque-o no modo de replicação síncrona e aguarde até que ele seja sincronizado. Em seguida, faça failover do local para o primeiro nó migrado, que é o AFP. Depois que isso funcionou, altere o último nó migrado para o AFP.

Você deve testar failovers entre todos os nós e executar os testes de caos para garantir que os failovers funcionem conforme o esperado e em uma oportuno oportuna.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Etapa 24: Colocar novamente as configurações de quorum do cluster/TTL do DNS/failover impressoras/configurações de sincronização

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionando recurso de endereço IP na mesma sub-rede

Se você tiver apenas dois SQL Servers e quiser migrá-los para um novo serviço de nuvem, mas quiser mantê-los na mesma sub-rede, poderá evitar colocar o ouvinte offline para excluir o endereço IP Always On original e adicionar o novo endereço IP. Se você estiver migrando as VMs para outra sub-rede, não será necessário fazer isso, pois há uma rede de cluster adicional que faz referência a essa sub-rede.

Depois de colocar o secundário migrado e adicionado ao novo recurso de endereço IP para o novo serviço de nuvem antes de realizar o failover do primário existente, você deve executar estas etapas dentro do cluster Gerenciador de Failover:

Para adicionar o endereço IP, consulte o apêndice, etapa 14.

1. Para o recurso de endereço IP atual, altere o proprietário possível para ' SQL Server primário existentes ', no exemplo, ' dansqlams4 ':

    ![Appendix13][23]
2. Para o novo recurso de endereço IP, altere o proprietário possível para ' SQL Server secundários migrados ', no exemplo, ' dansqlams5 ':

    ![Appendix14][24]
3. Depois que isso for definido, você poderá fazer failover e, quando o último nó for migrado, os possíveis proprietários deverão ser editados para que o nó seja adicionado como um possível proprietário:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionais

* [Armazenamento Premium do Azure](../disks-types.md)
* [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
