---
title: Utilizar o armazenamento Premium do Azure com o SQL Server | Documentos da Microsoft
description: Este artigo utiliza recursos criados com o modelo de implementação clássica e fornece orientações sobre como utilizar o armazenamento Premium do Azure com o SQL Server em execução em máquinas de virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: be96aaa69fc1d59bdfa8079eff99c13c1e92c736
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57905125"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizar o Armazenamento Premium do Azure com o SQL Server em Máquinas Virtuais

## <a name="overview"></a>Descrição geral

[Premium do Azure SSDs](../disks-types.md) é a próxima geração de armazenamento que proporciona baixa latência e alto débito e/s. Ele funciona melhor para e/s intensivas cargas de trabalho importantes, como o SQL Server em IaaS [máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/).

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo fornece orientações para migrar uma Máquina Virtual com o SQL Server para utilizar o armazenamento Premium de planejamento e. Isto inclui a infraestrutura do Azure (sistema de rede, armazenamento) e os passos de VM do Windows do convidado. O exemplo a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) mostra uma migração de ponta a ponta completa abrangente de como mover VMs maiores para tirar partido do armazenamento SSD local aperfeiçoados com o PowerShell.

É importante compreender o processo de ponto-a-ponto utilizar Premium do armazenamento do Azure com o SQL Server em VMs de IAAS. Isto inclui:

* Identificação dos pré-requisitos para utilizar o armazenamento Premium.
* Exemplos de implementar o SQL Server em IaaS para o armazenamento Premium para novas implementações.
* Exemplos de migrar as implementações existentes, dois servidores autónomos e implementações com o SQL grupos de Disponibilidade AlwaysOn.
* Abordagens possíveis de migração.
* Exemplo completo ponta a ponta que mostra os passos do Azure, o Windows e o SQL Server para a migração de uma implementação existente do Always On.

Para obter mais informações sobre o SQL Server em máquinas de virtuais do Azure, consulte [SQL Server em máquinas de virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **Technical Reviewers:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para o armazenamento Premium

Existem vários pré-requisitos para utilizar o armazenamento Premium.

### <a name="machine-size"></a>Tamanho da máquina

Para utilizar o armazenamento Premium tem de utilizar máquinas virtuais (VM) da série DS. Se não utilizou as máquinas de série DS no seu serviço cloud antes, tem de eliminar a VM existente, manter os discos anexados e, em seguida, crie um novo serviço cloud antes de recriar a VM como o tamanho de função DS *. Para obter mais informações sobre tamanhos de máquinas virtuais, consulte [Máquina Virtual e tamanhos do serviço Cloud do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Serviços em nuvem

Só pode utilizar DS * VMs com o armazenamento Premium, quando são criados num novo serviço cloud. Se estiver usando o SQL Server Always On no Azure, o sempre no serviço de escuta refere-se para o endereço interno do Azure ou o IP do Balanceador de carga externo que está associado um serviço em nuvem. Este artigo se concentra em como migrar, mantendo a disponibilidade neste cenário.

> [!NOTE]
> Uma série DS * tem de ser a primeira VM for implementada para o novo serviço Cloud.
>
>

### <a name="regional-vnets"></a>Regional VNETS

Para as VMs DS * tem de configurar a rede Virtual (VNET) que aloja as suas VMs sejam regional. Isso "amplia" a VNET é permitir que as VMs maiores ser aprovisionado em outros clusters e permitir a comunicação entre eles. Na captura de ecrã seguinte, a localização realçada mostra VNETs regionais, ao passo que o primeiro resultado mostra uma VNET "estreita".

![RegionalVNET][1]

Pode fazer um pedido de suporte da Microsoft para migrar para uma VNET regional. Microsoft, em seguida, faz uma alteração. Para concluir a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração de rede. Em primeiro lugar, exportar a configuração de rede no PowerShell e, em seguida, substitua a **AffinityGroup** propriedade na **VirtualNetworkSite** elemento com um **localização** propriedade. Especifique `Location = XXXX` onde `XXXX` é uma região do Azure. Em seguida, importe a nova configuração.

Por exemplo, Considerando a seguinte configuração de VNET:

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

Para mover isso para uma VNET regional na Europa Ocidental, altere a configuração para o seguinte:
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

Terá de criar uma nova conta de armazenamento que está configurada para o armazenamento Premium. Tenha em atenção que a utilização do armazenamento Premium está definida na conta de armazenamento, não em VHDs individuais, no entanto, quando utilizar uma VM de série DS * pode anexar do VHD de contas de armazenamento Premium e Standard. Pode considerar isso se não pretender colocar o VHD do SO para a conta de armazenamento Premium.

O seguinte procedimento **New-AzureStorageAccountPowerShell** comando com "Premium_LRS" **tipo** cria uma conta de armazenamento Premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Definições de Cache de VHDs

A principal diferença entre a criação de discos que fazem parte de uma conta de armazenamento Premium é a definição de cache do disco. Para discos de volume de dados do SQL Server, ele é recomendado que utilize '**colocação em cache de leitura**'. Para volumes de registo de transações, a definição de cache do disco deve ser definida como '**None**'. Isso é diferente de recomendações para contas de armazenamento Standard.

Assim que tenham sido vinculados os VHDs, não é possível alterar a definição da cache. Precisaria desanexar e voltar a anexar o VHD com uma definição de cache atualizado.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento do Windows

Pode usar [espaços de armazenamento do Windows](https://technet.microsoft.com/library/hh831739.aspx) como fez com o armazenamento Standard anterior, isto permite-lhe migrar uma VM que já está a utilizar espaços de armazenamento. O exemplo na [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (etapa 9 e encaminhamento) demonstra o código do Powershell para extrair e importar uma VM com vários VHDs ligadas.

Agrupamentos de armazenamento foram utilizados com a conta de armazenamento Standard do Azure para melhorar o débito e reduzir a latência. Pode encontrar o valor em testes de agrupamentos de armazenamento com o armazenamento Premium para novas implementações, mas adicionam complexidade adicional com a configuração de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como encontrar o mapa de discos virtuais do Azure para agrupamentos de armazenamento

Como existem recomendações de definição de cache diferente para VHDs ligadas, pode optar por copiar os VHDs a uma conta de armazenamento Premium. No entanto, quando voltar a ligá-los para a novo VM da série DS, poderá ter de alterar as definições de cache. É mais simples de aplicar o armazenamento Premium recomendado definições de cache quando tiver VHDs separados para os ficheiros de dados SQL e ficheiros de registo (em vez de um único VHD que contém os dois).

> [!NOTE]
> Se tiver ficheiros de registo e de dados do SQL Server no mesmo volume, a opção de colocação em cache que escolher depende dos padrões de acesso de e/s para as cargas de trabalho de base de dados. Apenas o teste pode demonstrar que opção de colocação em cache é mais adequada para este cenário.
>
>

No entanto, se estiver a utilizar espaços de armazenamento do Windows que são formados de vários VHDs tem de examinar seus scripts originais para identificar qual anexado VHDs estão no conjunto que específicos, assim pode, em seguida, definir as definições de cache em conformidade para cada disco.

Se não tiver o script original disponível para mostrar que VHDs mapeiam para o agrupamento de armazenamento, pode utilizar os seguintes passos para determinar o mapeamento de agrupamento de armazenamento do disco.

Para cada disco, utilize os seguintes passos:

1. Obter lista de discos ligados à VM com o **Get-AzureVM** comando:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Tenha em atenção o DiskName e o LUN.

    ![DisknameAndLUN][2]
1. Ambiente de trabalho remoto na VM. Em seguida, aceda a **gestão de computadores** | **Gestor de dispositivos** | **unidades de disco**. Ver as propriedades de cada um dos 'Microsoft discos virtuais'

    ![VirtualDiskProperties][3]
1. O número do LUN aqui é uma referência para o número do LUN que especificar ao anexar o VHD à VM.
1. Para o "Disco Virtual Microsoft", vá para o **detalhes** separador, em seguida, no **propriedade** lista, aceda a **Driver chave**. Na **valor**, tenha em atenção a **deslocamento**, que é 0002 na captura de ecrã seguinte. O 0002 denota a PhysicalDisk2 que referencia o agrupamento de armazenamento.

    ![VirtualDiskPropertyDetails][4]
1. Para cada agrupamento de armazenamento, despeje os discos associados:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Agora, pode utilizar estas informações para associar anexados VHDs de discos físicos nos agrupamentos de armazenamento.

Depois de ter mapeado os VHDs de discos físicos nos agrupamentos de armazenamento, em seguida, pode anular a exposição e copie-os para uma conta de armazenamento Premium, em seguida, anexe-os com a definição de cache correta. Consulte o exemplo na [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), os passos 8 a 12. Estes passos mostram como extrair uma configuração de disco VHD ligados à VM para um ficheiro CSV, copie os VHDs, alterar as definições de cache de configuração de disco e, finalmente, voltar a implementar a VM como uma VM da série DS com todos os discos ligados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Largura de banda de armazenamento VM e o débito de armazenamento do VHD

O nível de desempenho de armazenamento depende do tamanho da VM DS * especificado e os tamanhos VHD. As VMs têm permissões diferentes para o número de VHDs que podem ser anexados e a largura de banda máxima que suportam (MB/s). Para os números da largura de banda específica, consulte [Máquina Virtual e tamanhos do serviço Cloud do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

IOPS maior são alcançados com tamanhos de disco maiores. Isso deve ser considerado quando pensa em seu caminho de migração. Para obter detalhes, [consulte a tabela de IOPS e tipos de disco](../disks-types.md#premium-ssd).

Finalmente, considere que as VMs tenham larguras de banda de outro disco máximo que eles oferecem suporte para todos os discos ligados. Com uma carga elevada, poderia saturar a largura de banda máximo do disco disponível para esse tamanho de função VM. Por exemplo um Standard_DS14 suporta um máximo de 512 MB/s; Por conseguinte, com três discos em P30 pode saturar a largura de banda do disco da VM. Mas, neste exemplo, poderia ser excedeu o limite de taxa de transferência consoante a combinação de leitura e escrita IOs.

## <a name="new-deployments"></a>Novas implementações

As duas secções seguintes demonstram como pode implementar VMs do SQL Server para o armazenamento Premium. Como mencionado anteriormente, não necessariamente precisa colocar o disco do SO para o armazenamento Premium. Pode optar por fazê-lo se pretenda colocar quaisquer cargas de trabalho de e/s intensivas em termos no VHD do SO.

O primeiro exemplo demonstra a utilização de imagens da galeria do Azure existente. O segundo exemplo mostra como utilizar uma imagem VM personalizada que tiver numa conta de armazenamento padrão existente.

> [!NOTE]
> Estes exemplos partem do princípio de que já criou uma VNET Regional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar uma nova VM com o armazenamento Premium com a imagem da Galeria

O exemplo abaixo mostra como colocar o VHD do SO para o armazenamento premium e anexar VHDs de armazenamento Premium. No entanto, pode também colocar o disco do SO na conta de armazenamento Standard e, em seguida, anexe os VHDs que residem numa conta de armazenamento Premium. Ambos os cenários são demonstrados.

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

#### <a name="step-2-create-a-new-cloud-service"></a>Passo 2: Criar um novo serviço Cloud

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passo 3: Reservar um VIP do serviço Cloud (opcional)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Passo 4: Criar um contentor VM

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passo 5: Colocar o VHD do SO no armazenamento Standard ou Premium

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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Criar uma nova VM para utilizar o armazenamento Premium com uma imagem personalizada

Este cenário demonstra em que tem imagens personalizadas existentes que residem numa conta de armazenamento Standard. Conforme mencionado, se pretende colocar o VHD do SO no armazenamento Premium tem de copiar a imagem que existe na conta de armazenamento Standard e transferi-las para um armazenamento Premium, antes de poder ser utilizada. Se tiver uma imagem no local, pode também utilizar este método para copiá-lo diretamente para a conta de armazenamento Premium.

#### <a name="step-1-create-storage-account"></a>Passo 1: Criar Conta de Armazenamento

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>Passo 2-criar serviço Cloud

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Passo 3: Utilizar a imagem existente

Pode utilizar uma imagem existente. Também pode [pegar uma imagem de uma máquina existente](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tenha em atenção a máquina que imagem não tem de ser DS * máquina. Assim que tiver a imagem, os passos seguintes mostram como copiá-lo para a conta de armazenamento Premium com o **Start-AzureStorageBlobCopy** commandlet do PowerShell.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passo 4: Copiar BLOBs entre contas de armazenamento

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Passo 5: Verificar regularmente o estado da cópia:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passo 6: Adicionar disco de imagem ao repositório na subscrição de disco do Azure

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Pode achar que, mesmo que reporta o estado como êxito, ainda pode receber um erro de concessão de disco. Neste caso, aguarde cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Passo 7:  Criar a VM

Aqui estão a criar a VM da sua imagem e anexar dois VHDs de armazenamento Premium:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementações existentes que não utilizam grupos de Disponibilidade AlwaysOn

> [!NOTE]
> Para as implementações existentes, consulte a [pré-requisitos](#prerequisites-for-premium-storage) seção deste artigo.

Existem vários aspectos diferentes para implementações do SQL Server que não utilizam grupos de Disponibilidade AlwaysOn e os que fazer. Se não estiver a utilizar Always On e tiver uma autónoma existente do SQL Server, pode atualizar para o armazenamento Premium com uma conta de serviço e o armazenamento na cloud novas. Considere as seguintes opções:

* **Criar uma nova VM do SQL Server**. Pode criar uma nova VM do SQL Server que utiliza uma conta de armazenamento Premium, conforme documentado nas novas implementações. Em seguida, criar cópias de segurança e restaurar as bases de dados do utilizador e a configuração do SQL Server. A aplicação tem de ser atualizados para referenciar o novo SQL Server, se ele está sendo acessado internamente ou externamente. Terá de copiar todos os objetos "fora de db' como se estava a fazer uma migração do lado a lado (SxS) SQL Server. Isto inclui objetos, tais como inícios de sessão, certificados e servidores vinculados.
* **Migrar uma VM existente do SQL Server**. Isto requer que a VM do SQL Server offline, em seguida, transferi-los para um novo serviço cloud, o que inclui todos os seus VHDs ligadas a copiar para a conta de armazenamento Premium. Quando a VM fica online, o aplicativo referencia o nome de anfitrião do servidor como antes. Lembre-se de que o tamanho do disco existente afeta as características de desempenho. Por exemplo, um disco de 400 GB é arredondado para P20. Se souber que não necessita que o desempenho do disco, em seguida, pode recriar a VM como uma VM de série DS e anexar VHDs de armazenamento Premium da especificação de tamanho/desempenho que necessitar. Em seguida, poderia desanexar e voltar a ligá os ficheiros de BD SQL.

> [!NOTE]
> Quando copiar os discos VHD, deve estar ciente do tamanho, dependendo do tamanho significa que tipo de disco de armazenamento Premium elas enquadram-, isto determina a especificação de desempenho de disco. Arredonda do Azure até ao tamanho de disco mais próximo, portanto, se tiver um disco de 400 GB, isso é arredondado para P20. Dependendo dos requisitos de e/s existentes do VHD do SO, não poderá ter de migrar isso para uma conta de armazenamento Premium.

Se o SQL Server é acessado externamente, em seguida, o VIP do serviço de nuvem é alterado. Também tem pontos finais de atualização, as ACLs e DNS definições.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementações existentes que utilizam grupos de Disponibilidade AlwaysOn

> [!NOTE]
> Para as implementações existentes, consulte a [pré-requisitos](#prerequisites-for-premium-storage) seção deste artigo.

Inicialmente nesta secção vamos ver como Always On interage com redes do Azure. Vamos dividir, em seguida, as migrações em para dois cenários: migrações em que um período de indisponibilidade pela pode tolerar e migrações em que deve ser atingida período de indisponibilidade mínimo.

No local SQL Server grupos de Disponibilidade AlwaysOn utilizar um serviço de escuta no local que registra um nome DNS virtual juntamente com um endereço IP que é partilhado entre um ou mais servidores SQL. Quando os clientes ligam eles são encaminhados através do IP do serviço de escuta para o servidor primário do SQL. Este é o servidor que possui o recurso de sempre IP nesse momento.

![DeploymentsUseAlways no][6]

No Microsoft Azure que pode ter apenas um endereço IP atribuído a uma NIC na VM, por isso, para obter a mesma camada de abstração, como no local, Azure utiliza o endereço IP que está atribuído aos balanceadores de carga externo/interno (ILB/ELB). O recurso IP que é partilhado entre os servidores está definido para o mesmo IP como o ILB/ELB. Isso é publicado no DNS e o tráfego de cliente é transmitido ILB/ELB para a réplica primária do SQL Server. O ILB/ELB sabe que o SQL Server é primário, uma vez que utiliza sondas para o recurso de sempre IP de sonda. No exemplo anterior, sondas de cada nó que tem um ponto final referenciado pelo ELB/ILB, que responde é o principal do SQL Server.

> [!NOTE]
> O ILB e ELB são ambos atribuídos a um determinado Azure serviço em nuvem, por isso qualquer migração para a cloud no Azure significa mais provável que o IP do Balanceador de carga é alterado.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrar Always On as implementações que podem permitir que um período de indisponibilidade

Existem duas estratégias de migração de implementações de Always On que permitem a um período de indisponibilidade:

1. **Adicionar mais réplicas secundárias para sempre num Cluster existente**
2. **Migrar para um novo sempre no Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Adicionar mais réplicas secundárias para sempre num Cluster existente

É uma estratégia adicionar mais bases de dados secundárias para o grupo de Disponibilidade AlwaysOn. Tem de adicioná-las num novo serviço cloud e atualize o serviço de escuta com o novo IP do Balanceador de carga.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade:

* Validação do cluster.
* Testar as ativações pós-falha Always On para bases de dados secundárias de novo.

Se estiver a utilizar agrupamentos de armazenamento do Windows dentro da VM para um maior débito de e/s, em seguida, estas são colocadas offline durante uma validação de Cluster completo. O teste de validação é necessário quando adicionar nós ao cluster. O tempo que demora a executar o teste pode variar, para que deve testar isso no seu ambiente de teste representativo para obter uma hora aproximada de quanto tempo isso leva.

Deve aprovisionar a hora em que pode realizar ativação pós-falha manual e caos testes em nós de recém-adicionado para garantir que as funções de elevada disponibilidade Always On conforme esperado.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Deve parar todas as instâncias do SQL Server onde são utilizados os agrupamentos de armazenamento antes das execuções de validação.
>
> ##### <a name="high-level-steps"></a>Passos de alto nível
>

1. Crie dois novos servidores SQL no novo serviço em nuvem com o armazenamento Premium ligados.
2. Copie através de cópias de segurança completas e restaurar com **NORECOVERY**.
3. Copie 'fora do utilizador DB' objetos dependentes, tais como inícios de sessão etc.
4. Criar um balanceador novo de carga interno (ILB) um novo ou utilizar um balanceador de carga externo (ELB) e, em seguida, configure os pontos finais com balanceamento de carga em ambos os nós de novo.

   > [!NOTE]
   > Certifique-se todos os nós tem a configuração de ponto final correta antes de continuar
   >
   >
5. Pare o acesso de utilizadores/aplicações para o SQL Server (se utilizar agrupamentos de armazenamento).
6. Pare os serviços de motor do SQL Server em todos os nós (se utilizar agrupamentos de armazenamento).
7. Adicione novos nós em cluster e executar testes de validação completa.
8. Assim que a validação for bem sucedida, inicie todos os serviços do SQL Server.
9. Registos de transações de cópia de segurança e restaurar bases de dados do utilizador.
10. Adicionar novos nós para o grupo de Disponibilidade AlwaysOn e colocar os replicação em **síncrono**.
11. Adicionar o recurso de endereço IP do novo Cloud Service ILB/ELB através do PowerShell para Always On com base no exemplo de vários site na [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). No Windows clustering, defina o **possíveis proprietários** da **endereço IP** recursos para os novos nós antigos. Consulte a secção "Adicionar recurso de endereço IP na mesma sub-rede" a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Ativação pós-falha para um dos novos nós.
13. Tornar os novos nós ativações pós-falha de parceiros de ativação pós-falha automática e teste.
14. Remova nós originais do grupo de disponibilidade.

##### <a name="advantages"></a>Vantagens

* Novos servidores SQL pode ser testada (SQL Server e aplicação) antes de estes são adicionados Always On.
* Pode alterar o tamanho da VM e personalizar o armazenamento para suas necessidades exatas. No entanto, seria vantajoso manter todos os caminhos de ficheiro SQL, o mesmo.
* Pode controlar quando é iniciada a transferência das cópias de segurança de DB para as réplicas secundárias. Isso difere da utilização do Azure **Start-AzureStorageBlobCopy** commandlet para copiar VHDs, uma vez que é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens

* Quando utilizar agrupamentos de armazenamento do Windows, há o tempo de inatividade do Cluster durante a validação de Cluster completo para os novos nós adicionais.
* Consoante a versão do SQL Server e o número existente de réplicas secundárias, poderá não conseguir adicionar mais réplicas secundárias sem remover bases de dados secundárias existentes.
* Pode haver muito tempo de transferência de dados SQL ao configurar as bases de dados secundárias.
* Há custos adicionais durante a migração enquanto tiver novas máquinas em execução em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar para um novo sempre no Cluster

Outra estratégia é criar um novo sempre no Cluster connosco totalmente novos no novo serviço em nuvem e, em seguida, redirecionar os clientes para utilizá-lo.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

Há um período de indisponibilidade quando transferir aplicações e os utilizadores para o serviço de escuta de novos Always On. O tempo de inatividade depende o tempo limite:

* O tempo necessário para restaurar backups de log de transação final bancos de dados em novos servidores.
* O tempo necessário para Atualizar aplicativos de cliente para utilizar o novo sempre no serviço de escuta.

##### <a name="advantages"></a>Vantagens

* Pode testar o ambiente de produção real, o SQL Server, e as alterações de compilação do SO.
* Tem a opção de personalizar o armazenamento e potencialmente reduzir o tamanho da VM. Isto pode resultar numa redução de custo.
* Pode atualizar sua compilação do SQL Server ou a versão durante este processo. Também pode atualizar o sistema operativo.
* Sempre no Cluster anterior pode agir como um destino de reversão sólido.

##### <a name="disadvantages"></a>Desvantagens

* Terá de alterar o nome DNS do serviço de escuta se pretender que ambos os clusters Always On em execução em simultâneo. Esta ação adiciona a administração sobrecarga durante a migração, como cadeias de caracteres de aplicação de cliente têm de refletir o novo nome de serviço de escuta.
* Tem de implementar um mecanismo de sincronização entre os dois ambientes para mantê-los o mais próximo possível para minimizar os requisitos de sincronização final antes da migração.
* Lá é adicionada custo durante a migração enquanto tiver o novo ambiente em execução.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrar sempre sobre as implementações para o período de indisponibilidade mínimo

Existem duas estratégias para migrar implementações Always On para o período de indisponibilidade mínimo:

1. **Utilize um secundário existente: Site único**
2. **Utilize as réplicas secundárias existentes: Múltiplos sites**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilize um secundário existente: Site único

Uma estratégia para o período de indisponibilidade mínimo é pegar uma cloud existente secundário e removê-lo a partir do serviço cloud atual. Em seguida, copie os VHDs para a nova conta de armazenamento Premium e criar a VM no novo serviço cloud. Em seguida, atualize o serviço de escuta de clustering e de ativação pós-falha.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

* Há um período de indisponibilidade ao atualizar o nó final com o ponto final com balanceamento de carga.
* O restabelecimento de ligação de cliente pode estar atrasado consoante a configuração de cliente/DNS.
* Há um período de indisponibilidade adicional se optar por colocar o grupo de sempre Cluster offline para alternar os endereços IP. Isso pode ser evitado com uma dependência OR e possíveis proprietários para o recurso de endereço IP foi adicionado. Consulte a secção "Adicionar recurso de endereço IP na mesma sub-rede" a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Quando pretender que o nó foi adicionado para partake na como um sempre no parceiro de ativação pós-falha, terá de adicionar um ponto de final do Azure com uma referência para a carga balanceada definido. Quando executa o **Add-AzureEndpoint** de comando para fazer isso, abra as ligações atuais para permanecer, mas novas ligações ao serviço de escuta não são possível ser estabelecido até que o Balanceador de carga foi atualizado. No teste isso foi visto a última 90-120seconds, isso deve ser testado.

##### <a name="advantages"></a>Vantagens

* Não existem extra custo incorrido durante a migração.
* Uma migração de um para um.
* Complexidade reduzida.
* Permite uma maior IOPS de SKUs de armazenamento Premium. Quando os discos são anular a exposição da VM e copiados para o novo serviço de cloud de terceiros um 3rd ferramenta pode ser utilizada para aumentar o tamanho do VHD, que fornece as taxas de transferência mais elevadas. Para aumentar os tamanhos VHD, consulte esta [debate no fórum](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desvantagens

* Há uma perda temporária de HA e DR durante a migração.
* Como se trata de uma migração de 1:1, terá de utilizar um tamanho mínimo de VM que suporte o número de VHDs, pelo que poderá não conseguir atividade as suas VMs.
* Este cenário iria utilizar do Azure **Start-AzureStorageBlobCopy** commandlet, que é assíncrona. Não existe nenhum SLA na conclusão de cópia. O tempo das cópias varia, embora isso depende em espera na fila também depende da quantidade de dados a transferir. O tempo de cópia aumenta se a transferência para outro Datacenter do Azure que suporta o armazenamento Premium em outra região. Se tiver apenas 2 nós, considere uma medida possível de atenuação no caso da cópia exige mais tempo no teste. Pode incluir as seguintes idéias.
  * Adicione um nó do SQL Server 3º temporário para HA antes da migração com tempo de inatividade acordado.
  * Execute a migração fora do Azure manutenção agendada.
  * Certifique-se de que configurou corretamente o quórum do cluster.  

##### <a name="high-level-steps"></a>Passos de alto nível

Este documento não demonstre um exemplo completo de ponto a ponto, no entanto, a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fornece detalhes que podem ser usados para realizar isso.

![MinimalDowntime][8]

* Recolher configuração do disco e remover o nó (não elimine dos VHD ligados).
* Criar uma conta de armazenamento Premium e copiar VHDs a partir da conta de armazenamento Standard
* Criar novo serviço em nuvem e Reimplementar a VM SQL2 no referido serviço em nuvem. Crie a VM com o VHD do SO original copiado e anexar os VHDs copiados.
* Configurar o ILB / ELB e adicionar pontos de extremidade.
* Atualize o serviço de escuta através de:
  * Colocar offline o grupo Always On e atualizar o sempre no serviço de escuta com ILB novo / endereço IP de ELB.
  * Ou ao adicionar o recurso de endereço IP do novo Cloud Service ILB/ELB através do PowerShell para clustering do Windows. Em seguida, defina os possíveis proprietários do recurso de endereço IP para o nó migrado, SQL2 e isso como a dependência OR no nome da rede. Consulte a secção "Adicionar recurso de endereço IP na mesma sub-rede" a [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Verifique a configuração/propagação de DNS para os clientes.
* Migrar SQL1 VM e executar etapas de 2 a 4.
* Se utilizar passos 5ii, em seguida, adicione SQL1 como um possível proprietário para o recurso de endereço IP foi adicionado
* Ativações pós-falha de teste.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilize as réplicas secundárias existentes: Multilocal

Se tiver nós em mais de um datacenter do Azure (DC) ou se tiver um ambiente híbrido, pode utilizar uma configuração Always On neste ambiente, para minimizar o período de indisponibilidade.

A abordagem é alterar a sincronização Always On para síncrono para no local ou DC secundário do Azure e, em seguida, ativação pós-falha ao longo para que o SQL Server. Em seguida, copie os VHDs a uma conta de armazenamento Premium e voltar a implementar a máquina num novo serviço cloud. Atualizar o serviço de escuta e, em seguida, efetuar a reativação pós-falha.

##### <a name="points-of-downtime"></a>Pontos de tempo de inatividade

O tempo de inatividade é composta pelo tempo de ativação pós-falha para o controlador de domínio e vice-versa alternativo. Ele também depende da sua configuração de cliente/DNS e o restabelecimento de ligação do cliente pode sofrer um atraso.
Considere o seguinte exemplo de configuração híbrida Always On:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

* Pode utilizar a infraestrutura existente.
* Tem a opção de pré-atualização pela primeira vez o armazenamento do Azure no controlador de domínio do Azure de DR.
* O armazenamento de controlador de domínio do Azure de DR pode ser reconfigurado.
* Existe um mínimo de ativações pós-falha de dois durante a migração, excluindo as ativações pós-falha de teste.
* Não é necessário mover dados do SQL Server com cópia de segurança e restaurar.

##### <a name="disadvantages"></a>Desvantagens

* Dependendo de acesso para cliente para o SQL Server, pode haver uma maior latência ao SQL Server está em execução num DC alternativo para a aplicação.
* O tempo de cópia de VHDs para o armazenamento Premium pode ser longo. Poderá afetar sua decisão sobre se é preciso manter o nó no grupo de disponibilidade. Considere o seguinte para quando trabalho intensivo de log cargas estão em execução durante a migração é necessário, uma vez que o nó principal tem de manter as transações unreplicated em seu log de transação. Portanto, isso pode aumentar significativamente.
* Este cenário iria utilizar do Azure **Start-AzureStorageBlobCopy** commandlet, que é assíncrona. Após a conclusão, não existe nenhum SLA. O tempo das cópias varia, embora isso depende em espera na fila, ele também dependem de segurança a quantidade de dados a transferir. Portanto, tenha apenas um nó no seu Datacenter 2nd, que deve tomar passos de mitigação, no caso da cópia exige mais tempo no teste. Estes passos de mitigação incluem as seguintes idéias:
  * Adicione um nó temporário de SQL 2nd para HA antes da migração com tempo de inatividade acordado.
  * Execute a migração fora do Azure manutenção agendada.
  * Certifique-se de que configurou corretamente o quórum do cluster.

Este cenário pressupõe que documentamos a instalação e saber como o armazenamento está mapeado para fazer alterações para as definições de cache do disco ideal.

##### <a name="high-level-steps"></a>Passos de alto nível

![Multisite2][10]

* Tornar no local / alternativo o SQL Server primário do controlador de domínio do Azure e torná-lo a outros automática ativação pós-falha parceiro AFP ().
* Reunir informações de configuração do disco de SQL2 e remover o nó (não elimine dos VHD ligados).
* Criar uma conta de armazenamento Premium e copiar VHDs a partir da conta de armazenamento Standard.
* Criar um novo serviço de nuvem e criar a VM de SQL2 com seus discos de armazenamento de prémios ligados.
* Configurar o ILB / ELB e adicionar pontos de extremidade.
* Atualizar o sempre no serviço de escuta com o ILB novo / ELB IP endereço e o teste de ativação pós-falha.
* Verifique a configuração de DNS.
* Alterar o AFP para SQL2 e, em seguida, migre o SQL1 e passam por etapas 2 e 5.
* Ativações pós-falha de teste.
* Mudar o AFP para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apêndice: Migrar uma implementação multilocal sempre no Cluster para o armazenamento Premium

O restante deste artigo fornece um exemplo detalhado de conversão de um cluster multissite Always On para o armazenamento Premium. Também converte o serviço de escuta de utilizar um balanceador de carga externo (ELB) para um balanceador de carga interno (ILB).

### <a name="environment"></a>Ambiente

* Windows 2k12 / SQL 2k12
* Ficheiros de 1 DB no SP
* 2 x agrupamentos de armazenamento por nó

![Appendix1][11]

### <a name="vm"></a>VM:

Neste exemplo, vamos demonstrar movendo de uma ELB para o ILB. ELB estava disponível antes de ILB, então isso mostra como mudar para o ILB durante a migração.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Passos de pré-instalação: Ligar à subscrição

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Passo 1: Criar nova conta de armazenamento e serviço em nuvem

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passo 2: Aumentar as falhas permitidas nos recursos <Optional>

Em certos recursos que pertencem ao seu grupo de Disponibilidade AlwaysOn existem limites sobre quantos falhas que podem ocorrer num período, onde o serviço de cluster tenta reiniciar o grupo de recursos. Recomenda-se que aumenta esse, lidando simultaneamente e estiver percorrendo neste procedimento, desde se não manualmente as ativações pós-falha de ativação pós-falha e o acionador por encerrar as máquinas que pode chegar perto este limite.

Seria prudente necessário duplicar o número permitido de falha, para fazer isso no Gestor de clusters de ativação pós-falha, vá para as propriedades do grupo de recursos Always On:

![Appendix3][13]

Altere o máximo de falhas para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passo 3: Recurso de endereço IP de adição para o grupo de Cluster <Optional>

Se tiver apenas um endereço IP para o grupo de Cluster e isso é alinhado à sub-rede na cloud, saiba, se acidentalmente colocar offline todos os nós de cluster na cloud na rede, em seguida, o recurso de IP do Cluster e o nome de rede de Cluster não são conseguir fique online. Nesta situação, impede que as atualizações para outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Passo 4: Configuração do DNS

Implementar uma transição tranquila depende de como DNS está a ser utilizado e atualizado.
Quando o Always On está instalado, ele cria um grupo de recursos de Cluster do Windows, se abrir o Gestor de clusters de ativação pós-falha, verá que no mínimo, ele tem três recursos, são os dois que o documento refere-se a:

* Nome de rede virtual (VNN) – o nome DNS que os clientes ligam ao quando que pretendem ligar ao SQL Servers via Always On.
* Recurso de endereço IP – o endereço IP que associada a VNN, pode ter mais de um e, numa configuração de múltiplos sites tiver um endereço IP por sub-rede/site.

Quando ligar ao SQL Server, o SQL Server Client driver obtém os registos DNS associados com o serviço de escuta e tenta estabelecer ligação com cada Always On associado o endereço IP. Em seguida, vamos abordar alguns fatores que podem influenciar isso.

O número de registos DNS em simultâneo que estão associados com o nome do serviço de escuta depende não apenas o número de endereços IP associados, mas o "RegisterAllIpProviders'setting no Clustering de ativação pós-falha para o recurso de sempre ON VNN.

Ao implementar Always On no Azure existem passos diferentes para criar o serviço de escuta e endereços IP, precisa configurar manualmente o RegisterAllIpProviders como 1, isso é diferente para uma implementação do Always On no local em que já está definido como 1.

Se 'RegisterAllIpProviders' for 0, em seguida, apenas verá um registo DNS no DNS associado com o serviço de escuta:

![Appendix4][14]

Se "RegisterAllIpProviders" for 1:

![Appendix5][15]

O código a seguir despeja as definições de VNN e define-o por si. Para que a alteração tenha efeito, precisa colocar offline o VNN e ativá-lo novamente online. Isso leva o serviço de escuta offline que interrupção de conectividade de cliente.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Num passo posterior para a migração, tem de atualizar o serviço de escuta Always On com um endereço IP atualizado que faz referência a um balanceador de carga, isso envolve uma remoção de recurso de endereço IP e a adição. Após a atualização IP, terá de garantir que o novo endereço IP foi atualizado na zona DNS e que os clientes estão a atualizar seu cache DNS local.

Se seus clientes residem num segmento de rede diferentes e fazer referência a um servidor DNS diferente, precisa considerar o que acontece com a transferência de zona DNS durante a migração, como voltar a ligar a aplicação de tempo é restrita por, pelo menos, o tempo de transferência de zona de qualquer IP novo endereços para o serviço de escuta. Se está sob a restrição de tempo aqui, deve discutir e testar a forçar uma transferência de zona incremental com suas equipes de Windows, e também colocar o registo de anfitrião DNS para um menor tempo para Live (TTL), portanto, os clientes de atualização. Para obter mais informações, consulte [Incremental transferências de zona](https://technet.microsoft.com/library/cc958973.aspx) e [início DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Por predefinição, o valor de TTL para o registo de DNS que estão associados com o serviço de escuta no Always On no Azure é 1200 segundos. Pode pretender reduzi-lo se estiver em tempo de restrição durante a sua migração para garantir que os clientes de atualizar o seu DNS com o endereço IP atualizado para o serviço de escuta. Pode ver e modificar a configuração, despejar a configuração da VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Quanto mais baixo for 'HostRecordTTL uma maior quantidade de tráfego do DNS ocorre.

##### <a name="client-application-settings"></a>Definições da aplicação de cliente

Se a aplicação de cliente SQL suporta o .NET 4.5 SQLClient, em seguida, pode utilizar "MULTISUBNETFAILOVER = TRUE" palavra-chave. Deve ser aplicado essa palavra-chave, porque permite a ligação mais rápidas no grupo de disponibilidade Always durante a ativação pós-falha. Ele enumera todos os endereços IP associados a escuta Always On em paralelo e efetua uma velocidade de tentativas de ligação de TCP mais agressiva durante uma ativação pós-falha.

Para obter mais informações sobre as definições anteriores, consulte [palavra-chave de MultiSubnetFailover e recursos associados](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [SqlClient suporte para elevada disponibilidade, recuperação após desastre](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Passo 5: Definições de quórum do cluster

Como vai ser peguem pelo menos um servidor de SQL para baixo ao mesmo tempo, deve modificar a configuração de quórum do cluster, se utilizar o testemunho de partilha de ficheiros (FSW) com dois nós, deve definir o quórum para permitir que a maioria de nós e utilizar o voto dinâmico , permitindo a um único nó permaneça permanente.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Para obter mais informações sobre como gerir e configurar o quórum do cluster, consulte [configurar e gerir o quórum num Cluster de ativação pós-falha do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Passo 6: Extrair os pontos finais existentes e ACLs

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Guarde este texto num ficheiro.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passo 7: Parceiros de ativação pós-falha de alteração e modos de replicação

Se tiver mais de dois servidores do SQL Server, deve alterar a ativação pós-falha da outra secundária em outro controlador de domínio ou no local para 'Síncrono' e torná-lo um parceiro de ativação pós-falha automática (AFP), isto é, para manter HA, lidando simultaneamente e estiver a efetuar alterações. Pode fazê-lo por meio de TSQL de modificar, no entanto, SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passo 8: Remover VM secundária do serviço em nuvem

Deve planear a migração de um nó secundário da cloud em primeiro lugar. Se este nó está atualmente primário, deve iniciar uma ativação pós-falha manual.

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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 9: Alterar as definições no ficheiro CSV da colocação em cache e guardar

Para volumes de dados, estes devem ser definidos como só de leitura.

Para volumes TLOG, isso devem ser definidos como NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Passo 10: VHDS de cópia

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


Pode verificar o estado da cópia dos VHDs para a conta de armazenamento Premium:

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

Aguarde até que todos esses são registados como êxito.

Para obter informações para blobs individuais:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Passo 11: Registe-se o disco do SO

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Passo 12: Importar secundário para o novo serviço em nuvem

O código abaixo também utiliza a opção adicional aqui pode importar a máquina e utilizar o VIP retainable.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Passo 13: Criar o ILB no novo Cloud Svc, adicionar carga balanceada pontos de extremidade e ACLs

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

#### <a name="step-14-update-always-on"></a>Passo 14: Atualização Always On

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

Agora, remova o serviço em nuvem antigo endereço IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Passo 15: Verificação de atualizações de DNS

Agora, deve verificar servidores DNS nas suas redes de cliente do SQL Server e certifique-se de que a clustering adicionou o registo de anfitrião extra para o endereço IP foi adicionado. Se esses servidores DNS não tem atualizado, considere forçar uma transferência de zona DNS e certifique-se de que os clientes na lá sub-rede são capazes de resolver para os dois sempre em endereços IP, isto é, de modo não é necessário aguardar que a replicação automática de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Passo 16: Reconfigure Always On

Neste momento, aguarde pela secundário nesse nó que foi migrado para ressincronizar totalmente com o nó no local e mude para o nó de replicação síncrona e torná-lo a AFP.  

#### <a name="step-17-migrate-second-node"></a>Passo 17: Migrar o segundo nó

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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 18: Alterar as definições no ficheiro CSV da colocação em cache e guardar

Para volumes de dados, as definições de cache devem ser definidas como READONLY.

Para volumes TLOG, as definições de cache devem ser definidas como NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Passo 19: Criar nova conta de armazenamento independentes para o nó secundário

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

#### <a name="step-20-copy-vhds"></a>Passo 20: VHDS de cópia

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

Pode verificar o estado de cópia do VHD para todos os VHDs:

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

Aguarde até que todos esses são registados como êxito.

Para obter informações para blobs individuais:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Passo 21: Registe-se o disco do SO

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Passo 22: Adicionar carga balanceada pontos de extremidade e ACLs

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

#### <a name="step-23-test-failover"></a>Passo 23: Ativação pós-falha de teste

Aguarde até o nó migrado sincronizar com o nó de Always On no local. Coloque-o no modo de replicação síncrona e aguarde até que os dados foram sincronizados. Em seguida, ativação pós-falha no local para o primeiro nó migradas, que é o AFP. Uma vez que já trabalhou, altere o último nó migrado para o AFP.

Deve ativações pós-falha entre todos os nós de teste e execute embora chaos testes para garantir que as ativações pós-falha funcionam como esperado e, num manor oportuna.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Passo 24: Colocar novamente as definições de quórum de cluster / TTL de DNS / ativação pós-falha Pntrs / definições de sincronização

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionar o recurso de endereço IP na mesma sub-rede

Se tem apenas dois servidores de SQL e quiser migrá-los para um novo serviço cloud, mas queremos mantê-los na mesma sub-rede, pode evitar a colocar offline o serviço de escuta para eliminar o original sempre no endereço IP e adicione o novo endereço IP. Se estiver a migrar as VMs para outra sub-rede, não é necessário fazer isso porque não existe uma rede de cluster adicional que faz referência a essa sub-rede.

Depois de recuperadas secundário migrado e adicionado no novo recurso de endereço IP para o novo serviço cloud antes da ativação pós-falha primário existente, deve levar estes passos dentro do Gestor de ativação pós-falha do Cluster:

Para adicionar endereço IP, consulte o apêndice, passo 14.

1. Para o recurso de endereço IP atual, altere o proprietário possível para "Principal SQL Server existente", no exemplo, "dansqlams4":

    ![Appendix13][23]
2. Para o novo recurso de endereço IP, altere o proprietário possível para 'migrado secundário do SQL Server", no exemplo,"dansqlams5":

    ![Appendix14][24]
3. Depois de esta definição estiver definida, pode de ativação pós-falha e, quando o último nó é migrado os possíveis proprietários deve ser editados, para que esse nó é adicionado como um possível proprietário:

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
