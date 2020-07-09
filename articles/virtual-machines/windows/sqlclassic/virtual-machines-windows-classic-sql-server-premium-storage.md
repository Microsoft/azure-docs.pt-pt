---
title: Use o Azure Premium Storage com SQL Server / Microsoft Docs
description: Este artigo utiliza recursos criados com o modelo de implementação clássico, e dá orientações sobre a utilização do Azure Premium Storage com o SQL Server em execução em Azure Virtual Machines.
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
ms.openlocfilehash: ca11fce252192cbf8e5f0bc2cfb5fcd38f5d4443
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84020885"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizar o Armazenamento Premium do Azure com o SQL Server em Máquinas Virtuais

## <a name="overview"></a>Descrição geral

[Azure premium SSDs](../disks-types.md) é a próxima geração de armazenamento que fornece baixa latência e IO de alta produção. Funciona melhor para cargas de trabalho intensivas de IO, como o SQL Server em [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)IaaS.

> [!IMPORTANT]
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo fornece planeamento e orientação para migrar uma máquina virtual que executa o SQL Server para utilizar o Armazenamento Premium. Isto inclui a infraestrutura Azure (networking, armazenamento) e os passos VM do Windows. O exemplo do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) mostra um fim abrangente completo para acabar com a migração de como mover VMs maiores para aproveitar o armazenamento SSD local melhorado com o PowerShell.

É importante compreender o processo de ponta a ponta de utilização do Azure Premium Storage com SQL Server em IAAS VMs. O que está incluído:

* Identificação dos pré-requisitos para a utilização de Armazenamento Premium.
* Exemplos de implantação do SQL Server no IaaS para o Armazenamento Premium para novas implementações.
* Exemplos de implementações existentes em migração, servidores autónomos e implementações utilizando SQL Always On Availability Groups.
* Possíveis aproximações de migração.
* Exemplo completo de ponta a ponta mostrando passos do Azure, Windows e SQL Server para a migração de uma implementação Always On existente.

Para obter mais informações sobre o SQL Server em Azure Virtual Machines, consulte [o SQL Server em Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Autor:** Daniel Sol **Comentadores Técnicos:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para armazenamento premium

Existem vários pré-requisitos para a utilização de Armazenamento Premium.

### <a name="machine-size"></a>Tamanho da máquina

Para utilizar o Armazenamento Premium, é necessário utilizar máquinas virtuais da série DS (VM). Se não tiver usado máquinas DS Series no seu serviço de nuvem antes, deve eliminar o VM existente, manter os discos anexados e, em seguida, criar um novo serviço de nuvem antes de recriar o tamanho da função VM como DS*. Para obter mais informações sobre tamanhos de máquina virtual, consulte [tamanhos de serviço de máquina virtual e cloud para Azure.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cloud-services"></a>Serviços em nuvem

Só pode utilizar VMs DS* com Armazenamento Premium quando são criados num novo serviço de nuvem. Se estiver a utilizar o SQL Server Always On in Azure, o Sempre No Ouvido refere-se ao endereço IP do balançador de carga interno ou externo Azure que está associado a um serviço de cloud. Este artigo centra-se na forma de migrar, mantendo a disponibilidade neste cenário.

> [!NOTE]
> Uma Série DS* deve ser o primeiro VM que é implantado no novo Cloud Service.
>
>

### <a name="regional-vnets"></a>VNETS Regionais

Para DS* VMs deve configurar a Rede Virtual (VNET) que hospeda os seus VMs para serem regionais. Isto "alarga" o VNET é para permitir que os VM maiores sejam a provisionados em outros clusters e permitir a comunicação entre eles. Na imagem seguinte, a localização destacada mostra VNETs regionais, enquanto o primeiro resultado mostra um VNET "estreito".

![RegionalVNET][1]

Você pode levantar um bilhete de suporte da Microsoft para migrar para um VNET regional. A Microsoft faz então uma alteração. Para completar a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração da rede. Primeiro exporte a Configuração de Rede em PowerShell e, em seguida, substitua a propriedade **AffinityGroup** no elemento **VirtualNetworkSite** por uma propriedade **localização.** Especificar `Location = XXXX` onde `XXXX` fica uma região de Azure. Em seguida, importe a nova configuração.

Por exemplo, tendo em conta a seguinte configuração VNET:

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

Para mover isto para um VNET regional na Europa Ocidental, altere a configuração para o seguinte:
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

Precisa de criar uma nova conta de armazenamento configurada para Armazenamento Premium. Note que a utilização de Armazenamento Premium é definida na conta de armazenamento, não em VHDs individuais, no entanto, ao utilizar um DS* Série VM, pode anexar VHD's a partir de contas premium e standard de armazenamento. Pode considerar isto se não quiser colocar o OS VHD na conta De Armazenamento Premium.

O seguinte comando **New-AzureStorageAccountPowerShell** com o **tipo** "Premium_LRS" cria uma conta de armazenamento premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Configurações de cache VHDs

A principal diferença entre a criação de discos que fazem parte de uma conta de Armazenamento Premium é a definição de cache de disco. Para discos de volume de dados do servidor SQL é recomendável que utilize '**Ler Caching**'. Para os volumes de registo de transações, a definição de cache do disco deve ser definida para '**Nenhum**'. Isto é diferente das recomendações para as contas standard de armazenamento.

Uma vez fixados os VHDs, a regulação da cache não pode ser alterada. É necessário separar e voltar a colocar o VHD com uma definição de cache atualizada.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento de janelas

Pode utilizar [os Espaços de Armazenamento do Windows,](https://technet.microsoft.com/library/hh831739.aspx) como fez com o Armazenamento Standard anterior, o que lhe permite migrar um VM que já está a utilizar espaços de armazenamento. O exemplo no [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (passo 9 e para a frente) demonstra o código Powershell para extrair e importar um VM com vários VHDs anexados.

As piscinas de armazenamento foram utilizadas com a conta de armazenamento Standard Azure para aumentar a produção e reduzir a latência. Você pode encontrar valor no teste de Pools de Armazenamento com Armazenamento Premium para novas implementações, mas eles adicionam complexidade adicional com a configuração de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como descobrir qual o mapa de Discos Virtuais Azure para piscinas de armazenamento

Uma vez que existem diferentes recomendações de definição de cache para VHDs anexados, pode decidir copiar os VHDs para uma conta de Armazenamento Premium. No entanto, quando os voltar a ligar à nova série DS VM, poderá ter de alterar as definições de cache. É mais simples aplicar as definições de cache recomendadas para o Armazenamento Premium quando tiver VHDs separados para os ficheiros de dados SQL e ficheiros de registo (em vez de um único VHD que contém ambos).

> [!NOTE]
> Se tiver dados do SQL Server e registar ficheiros no mesmo volume, a opção de caching que escolher depende dos padrões de acesso ao IO para as cargas de trabalho da sua base de dados. Apenas os testes podem demonstrar qual a melhor opção para este cenário.
>
>

No entanto, se estiver a utilizar espaços de armazenamento do Windows que são compostos por vários VHDs, precisa de olhar para os seus scripts originais para identificar quais vHDs anexados estão em que piscina específica, para que possa definir as definições de cache em conformidade para cada disco.

Se não tiver o script original disponível para mostrar quais os VHDs mapear para a piscina de armazenamento, pode usar os seguintes passos para determinar o mapeamento do disco/piscina de armazenamento.

Para cada disco, utilize os seguintes passos:

1. Obtenha a lista de discos anexados ao VM com o comando **Get-AzureVM:**

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Note o Nome do Disco e LUN.

    ![DisknameAndLUN][2]
1. Ambiente de trabalho remoto no VM. Em seguida, aceda a Unidades de Discos do Gestor de Dispositivos de **Gestão de**  |  **Device Manager**  |  **Computadores**. Veja as propriedades de cada um dos 'Discos Virtuais da Microsoft'

    ![VirtualDiskProperties][3]
1. O número LUN aqui é uma referência ao número LUN que especifica ao anexar o VHD ao VM.
1. Para o separador 'Microsoft Virtual Disk' aceda ao separador **Detalhes** e, em seguida, na lista **de propriedades,** vá para **a Chave de Condutor**. No **Valor**, note-se o **Offset**, que é 0002 na seguinte imagem. O 0002 denota o PhysicalDisk2 que o armazém refere.

    ![VirtualDiskPropertyDetails][4]
1. Para cada piscina de armazenamento, despeje os discos associados:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Agora pode utilizar esta informação para associar VHDs anexados a Discos Físicos em Piscinas de Armazenamento.

Depois de ter mapeado VHDs para Discos Físicos em Conjuntos de Armazenamento, pode então desprender-los e copiá-los numa conta de Armazenamento Premium e, em seguida, anexá-los com a definição de cache correta. Veja o exemplo no [apêndice,](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)passos 8 a 12. Estes passos mostram como extrair uma configuração de disco VHD anexada a VM para um ficheiro CSV, copiar os VHDs, alterar as definições de cache de configuração do disco e, finalmente, recolocar o VM como um VM série DS com todos os discos anexos.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Largura de banda de armazenamento VM e saída de armazenamento VHD

A quantidade de desempenho de armazenamento depende do tamanho DS* VM especificado e dos tamanhos VHD. Os VMs têm licenças diferentes para o número de VHDs que podem ser anexados e a largura de banda máxima que suportam (MB/s). Para obter os números específicos de largura de banda, consulte [os tamanhos de serviço de máquina virtual e cloud para Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

O aumento do IOPS é alcançado com tamanhos de disco maiores. Devia considerar isto quando pensa no seu caminho de migração. Para mais detalhes, [consulte a tabela para os tipos de IOPS e Discos](../disks-types.md#premium-ssd).

Por último, considere que os VM têm diferentes larguras de banda máximas de disco que suportam para todos os discos ligados. Sob alta carga, pode saturar a largura de banda máxima disponível para esse tamanho de função VM. Por exemplo, um Standard_DS14 suporta até 512MB/s; portanto, com três discos P30 pode saturar a largura de banda do disco do VM. Mas neste exemplo, o limite de produção poderia ser ultrapassado dependendo da mistura de IOs lidos e escrever.

## <a name="new-deployments"></a>Novas implementações

As próximas duas secções demonstram como pode implementar VMs do SQL Server para o Armazenamento Premium. Como mencionado anteriormente, não precisa necessariamente de colocar o disco DE no armazenamento Premium. Você pode optar por fazê-lo se pretender colocar quaisquer cargas de trabalho intensivas de IO no OS VHD.

O primeiro exemplo demonstra a utilização de imagens existentes da Galeria Azure. O segundo exemplo mostra como usar uma imagem VM personalizada que você tem numa conta de armazenamento Standard existente.

> [!NOTE]
> Estes exemplos pressupõem que já criou um VNET Regional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Criar um novo VM com Armazenamento Premium com Imagem de Galeria

O exemplo abaixo mostra como colocar o OS VHD no armazenamento premium e anexar VHDs de armazenamento premium. No entanto, também pode colocar o disco DE numa conta standard de Armazenamento e, em seguida, anexar VHDs que residem numa conta de Armazenamento Premium. Ambos os cenários são demonstrados.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>Passo 1: Criar uma conta de armazenamento premium

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

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passo 3: Reserve um Serviço de Nuvem VIP (Opcional)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>Passo 4: Criar um recipiente VM

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passo 5: Colocação do OS VHD no armazenamento standard ou premium

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
#Note the size specified '-DiskSizeInGB 1023', this attaches 2 x P30 Premium Storage Disk Type
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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Crie um novo VM para utilizar o Premium Storage com uma imagem personalizada

Este cenário demonstra onde tem imagens personalizadas existentes que residem numa conta Standard Storage. Como mencionado se pretende colocar o OS VHD no Armazenamento Premium, precisa copiar a imagem que existe na conta standard de Armazenamento e transferi-la para um Armazenamento Premium antes de poder ser utilizada. Se tiver uma imagem no local, também pode utilizar este método para copiar diretamente para a conta De Armazenamento Premium.

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

#### <a name="step-2-create-cloud-service"></a>Passo 2 Criar serviço de nuvem

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>Passo 3: Utilizar a imagem existente

Pode usar uma imagem existente. Ou pode [tirar uma imagem de uma máquina existente.](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) Note que a máquina que vê não tem de ser a máquina DS*. Uma vez que tenha a imagem, os passos seguintes mostram como copiá-la na conta de Armazenamento Premium com o comando **Start-AzureStorageBlobCopy** PowerShell.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passo 4: Bolha de cópia entre contas de armazenamento

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>Passo 5: Verifique regularmente o estado da cópia:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passo 6: Adicionar disco de imagem ao repositório de disco Azure em Subscrição

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Pode descobrir que, mesmo que o estado reporte como sucesso, ainda pode obter um erro de aluguer de disco. Neste caso, espere cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Passo 7: Construir o VM

Aqui está a construção do VM a partir da sua imagem e a anexar dois VHDs de armazenamento premium:

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
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementações existentes que não utilizam sempre grupos de disponibilidade

> [!NOTE]
> Para as implementações existentes, consulte primeiro a secção [Pré-Requisitos](#prerequisites-for-premium-storage) deste artigo.

Existem diferentes considerações para implementações do SQL Server que não utilizam sempre os Grupos de Disponibilidade e os que o fazem. Se não estiver a utilizar o Always On e tiver um SqL Server autónomo existente, pode fazer upgrade para o Premium Storage utilizando um novo serviço de cloud e uma conta de armazenamento. Considere as seguintes opções:

* **Criar um novo SQL Server VM**. Pode criar um novo SQL Server VM que utiliza uma conta de Armazenamento Premium, como documentado em Novas Implementações. Em seguida, volte atrás e restaure a configuração do SEU SQL Server e as bases de dados do utilizador. A aplicação precisa de ser atualizada para fazer referência ao novo SQL Server se estiver a ser acedida interna ou externamente. Você precisaria copiar todos os objetos 'out of db' como se estivesse a fazer uma migração side by side (SxS) SQL Server. Isto inclui objetos como logins, certificados e servidores ligados.
* **Migrar um VM do servidor SQL existente**. Isto requer a desativação do SQL Server VM e, em seguida, transferi-lo para um novo serviço de nuvem, que inclui copiar todos os seus VHDs anexados para a conta de Armazenamento Premium. Quando o VM entra on-line, a aplicação refere o nome do anfitrião do servidor como antes. Tenha em atenção que o tamanho do disco existente afeta as características de desempenho. Por exemplo, um disco de 400 GB é arredondado até um P20. Se sabe que não necessita do desempenho do disco, então pode recriar o VM como um VM série DS e anexar VHDs de armazenamento premium da especificação de tamanho/desempenho que necessita. Em seguida, pode separar-se e voltar a ligar os ficheiros DB SQL.

> [!NOTE]
> Ao copiar os discos VHD deve estar ciente do tamanho, dependendo do tamanho significa em que tipo de disco de armazenamento premium eles caem, isto determina a especificação de desempenho do disco. O Azure arredonda até ao tamanho do disco mais próximo, por isso, se tiver um disco de 400GB, este é arredondado até um P20. Dependendo dos requisitos de IO existentes do OS VHD, poderá não precisar de migrar para uma conta de Armazenamento Premium.

Se o seu SQL Server for acedido externamente, o serviço de nuvem VIP muda. Também tem de atualizar pontos finais, ACLs e configurações DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementações existentes que utilizam sempre grupos de disponibilidade

> [!NOTE]
> Para as implementações existentes, consulte primeiro a secção [Pré-Requisitos](#prerequisites-for-premium-storage) deste artigo.

Inicialmente nesta secção vemos como a Always On interage com o Azure Networking. Depois, decompomos as migrações em dois cenários: migrações onde algum tempo de inatividade pode ser tolerado e migrações onde é preciso atingir o mínimo tempo de inatividade.

No local, os Grupos SQL Server Always On Availability utilizam um ouvinte no local que regista um nome DNS virtual juntamente com um endereço IP que é partilhado entre um ou mais Servidores SQL. Quando os clientes se ligam, são encaminhados através do IP do ouvinte para o Servidor SQL Primário. Este é o servidor que detém o recurso Always On IP naquela altura.

![ImplementaçõesUseAlways On][6]

No Microsoft Azure pode ter apenas um endereço IP atribuído a um NIC no VM, de modo a obter a mesma camada de abstração que no local, a Azure utiliza o endereço IP que é atribuído aos Balanceadores de Carga Interno/Externo (ILB/ELB). O recurso IP que é partilhado entre os servidores está definido para o mesmo IP que o ILB/ELB. Isto é publicado no DNS, e o tráfego do cliente é passado através do ILB/ELB para a réplica do Servidor SQL Primário. O ILB/ELB sabe qual o SqL Server primário, uma vez que utiliza sondas para sondar o recurso Always On IP. No exemplo anterior, sonda cada nó que tem um ponto final referenciado pelo ELB/ILB, o que responder é o Servidor SQL Primário.

> [!NOTE]
> O ILB e o ELB estão ambos atribuídos a um determinado serviço de nuvem Azure, pelo que qualquer migração em nuvem em Azure significa, muito provavelmente, que o Ip do Balanceador de Carga muda.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migração Sempre Em implementações que podem permitir algum tempo de inatividade

Existem duas estratégias para migrar sempre em implementações que permitem algum tempo de inatividade:

1. **Adicione mais réplicas secundárias a um Sempre No Cluster existente**
2. **Migrar para um novo Always On Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Adicione mais réplicas secundárias a um sempre existente no cluster

Uma estratégia é adicionar mais secundários ao Grupo Always On Availability. É necessário adicionar estes num novo serviço de nuvem e atualizar o ouvinte com o novo IP do balanceador de carga.

##### <a name="points-of-downtime"></a>Pontos de inatividade:

* Validação do Cluster.
* Testes sempre em falhas para novos secundários.

Se estiver a utilizar piscinas de armazenamento do Windows dentro do VM para uma produção de IO mais elevada, então estas são desligadas durante uma validação completa do cluster. O teste de validação é necessário quando se adicionam nós ao cluster. O tempo que leva para executar o teste pode variar, por isso deve testá-lo no seu ambiente de teste representativo para obter um tempo aproximado do tempo que isto demora.

Deverá prever o tempo em que pode efetuar testes manuais de failover e caos nos nós recém-adicionados para garantir as funções "Sempre em Alta Disponibilidade", como esperado.

![ImplementaçãoUseAlways On2][7]

> [!NOTE]
> Deve parar todas as instâncias do SQL Server onde as Piscinas de Armazenamento são utilizadas antes da validação ser executado.
>
> ##### <a name="high-level-steps"></a>Passos gerais
>

1. Crie dois novos Servidores SQL em novo serviço de nuvem com armazenamento Premium anexado.
2. Copie as cópias full e restaure com **NORECOVERY**.
3. Copie sobre objetos dependentes 'out of user DB', tais como logins, etc.
4. Crie um novo balanceador de carga interno (ILB) ou utilize um Balanceador de Carga Externo (ELB) e, em seguida, instale pontos de terminação equilibrados de carga em ambos os novos nós.

   > [!NOTE]
   > Verifique todos os Nós que têm a configuração correta do Ponto de Final antes de continuar
   >
   >
5. Parar o acesso ao utilizador/aplicação ao servidor SQL (se utilizar pools de armazenamento).
6. Pare os serviços de motor do servidor SQL em todos os nós (se utilizar pools de armazenamento).
7. Adicione novos nós ao cluster e execute a validação completa.
8. Assim que a Validação for bem sucedida, inicie todos os Serviços de Servidor SQL.
9. Registos de transações de backup e restaurar as bases de dados dos utilizadores.
10. Adicione novos nós no Grupo Always On Availability e coloque a replicação em **Synchronous**.
11. Adicione o recurso de endereço IP do novo Serviço cloud ILB/ELB através do PowerShell for Always On com base no exemplo multi-site no [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). No agrupamento do Windows, desaprote os **possíveis proprietários** do recurso **IP Address** para os novos nós antigos. Consulte a secção 'Adicionar recurso de endereço IP na mesma sub-rede' do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Falha num dos novos nós.
13. Faça os novos nós Auto Failover Partners e teste failovers.
14. Remova os nós originais do Grupo Disponibilidade.

##### <a name="advantages"></a>Vantagens

* Os novos Servidores SQL podem ser testados (SQL Server e Application) antes de serem adicionados ao Always On.
* Pode alterar o tamanho VM e personalizar o armazenamento de acordo com os seus requisitos exatos. No entanto, seria benéfico manter todos os caminhos de ficheiro SQL iguais.
* Pode controlar quando a transferência das cópias de segurança DB para as Réplicas Secundárias é iniciada. Isto difere da utilização do comando **Azure Start-AzureStorageBlobCopy** para copiar VHDs, porque é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens

* Ao utilizar piscinas de armazenamento do Windows, há tempo de inatividade do Cluster durante a Validação completa do Cluster para os novos nós adicionais.
* Dependendo da versão sql server e do número existente de réplicas secundárias, poderá não ser capaz de adicionar mais réplicas secundárias sem remover os secundários existentes.
* Pode haver um longo tempo de transferência de dados SQL enquanto configura os secundários.
* Há custos adicionais durante a migração enquanto você tem novas máquinas em execução em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar para um novo Always On Cluster

Outra estratégia é criar um novo Always On Cluster com novos nós no novo serviço de nuvem e, em seguida, redirecionar os clientes para usá-lo.

##### <a name="points-of-downtime"></a>Pontos de inatividade

Há tempo de inatividade quando transfere aplicações e utilizadores para o novo ouvinte Always On. O tempo de inatividade depende de:

* O tempo necessário para restaurar as cópias de segurança finais do registo de transações para bases de dados em novos servidores.
* O tempo necessário para atualizar as aplicações dos clientes para utilizar o novo ouvinte Always On.

##### <a name="advantages"></a>Vantagens

* Pode testar o ambiente de produção real, o SQL Server e o SISTEMA criar alterações.
* Tem a opção de personalizar o armazenamento e potencialmente reduzir o tamanho de VM. Isto poderia resultar numa redução de custos.
* Pode atualizar a construção ou versão do seu SQL Server durante este processo. Também pode atualizar o Sistema Operativo.
* O anterior Always On Cluster pode funcionar como um alvo de reversão sólido.

##### <a name="disadvantages"></a>Desvantagens

* Tem de alterar o nome DNS do ouvinte se quiser que ambos os clusters Always On em funcionamento simultaneamente. Isto adiciona a sobrecarga da administração durante a migração, uma vez que as cordas de aplicação do cliente devem refletir o novo nome Listener.
* Deve implementar um mecanismo de sincronização entre os dois ambientes para os manter o mais próximo possível para minimizar os requisitos finais de sincronização antes da migração.
* Há um custo adicional durante a migração enquanto você tem o novo ambiente em funcionamento.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migração Sempre Em Implementações para tempo mínimo de inatividade

Existem duas estratégias para migrar sempre em implementações para o mínimo tempo de inatividade:

1. **Utilizar um Secundário Existente: Single-Site**
2. **Utilizar réplicas secundárias existentes: multi-site**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilizar um secundário existente: Single-Site

Uma estratégia para o tempo mínimo de inatividade é pegar numa nuvem secundária existente e removê-la do serviço de nuvem atual. Em seguida, copie os VHDs para a nova conta de Armazenamento Premium e crie o VM no novo serviço de nuvem. Em seguida, atualize o ouvinte em agrupamento e failover.

##### <a name="points-of-downtime"></a>Pontos de inatividade

* Há tempo de inatividade quando atualiza o nó final com o ponto final equilibrado de carga.
* A reconexão do seu cliente pode ser retardada dependendo da configuração do seu cliente/DNS.
* Há tempo de inatividade adicional se optar por tirar o grupo Always On Cluster offline para trocar os endereços IP. Pode evitar isso utilizando uma dependência de OR e possíveis proprietários para o recurso ip address adicionado. Consulte a secção 'Adicionar recurso de endereço IP na mesma sub-rede' do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Quando pretender que o nó adicionado participe como parceiro Always On Failover, tem de adicionar um Ponto final Azure com uma referência ao Conjunto Equilibrado de Carga. Quando executar o comando **Add-AzureEndpoint** para o fazer, as ligações atuais permanecem abertas, mas não podem ser estabelecidas novas ligações ao ouvinte até que o balançador de carga tenha sido atualizado. Nos testes, isto foi visto para durar 90-120s segundos, este deve ser testado.

##### <a name="advantages"></a>Vantagens

* Não há custos adicionais incorridos durante a migração.
* Uma migração de um para um.
* Complexidade reduzida.
* Permite um aumento de IOPS a partir de SKUs de armazenamento premium. Quando os discos são separados do VM e copiados para o novo serviço de nuvem, uma ferramenta de 3ª parte pode ser usada para aumentar o tamanho VHD, que fornece posições mais altas. Para aumentar os tamanhos de VHD, consulte esta [discussão do fórum.](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)

##### <a name="disadvantages"></a>Desvantagens

* Há uma perda temporária de HA e DR durante a migração.
* Como esta é uma migração de 1:1, você tem que usar um tamanho VM mínimo que suporta o seu número de VHDs, para que você possa não ser capaz de reduzir seus VMs.
* Este cenário utilizaria o comando **Azure Start-AzureStorageBlobCopy,** que é assíncrona. Não há SLA na conclusão da cópia. O tempo das cópias varia, enquanto isso depende da espera na fila, também depende da quantidade de dados para transferir. O tempo de cópia aumenta se a transferência for para outro centro de dados Azure que suporta o Armazenamento Premium noutra região. Se tiver apenas 2 nós, considere uma possível atenuação caso a cópia descodua mais tempo do que nos testes. Isto pode incluir as seguintes ideias.
  * Adicione um nó temporário do 3º SQL Server para HA antes da migração com tempo de inatividade acordado.
  * Executar a migração fora da manutenção programada de Azure.
  * Certifique-se de que configura o quórum do cluster corretamente.  

##### <a name="high-level-steps"></a>Passos gerais

Este documento não demonstra um exemplo final completo, no entanto o [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fornece detalhes que podem ser alavancados para o realizar.

![Tempo mínimo de tempo][8]

* Recolher a configuração do disco e remover o nó (não apagar VHDs anexados).
* Criar uma conta de Armazenamento Premium e copiar VHDs da conta de Armazenamento Padrão
* Crie um novo serviço de nuvem e reimplante o SQL2 VM nesse serviço de nuvem. Crie o VM utilizando o OS VHD original copiado e fixando os VHDs copiados.
* Configure ILB / ELB e adicione Pontos finais.
* Atualizar o Ouvinte por qualquer um dos dois:
  * Tirar o Grupo Always On offline e atualizar o Always On Listener com o novo endereço IP ILB/ELB.
  * Ou adicionar o recurso de endereço IP do novo Serviço cloud ILB/ELB através do PowerShell no agrupamento do Windows. Em seguida, desaprote os possíveis proprietários do recurso IP Address para o nó migrado, SQL2, e definir este como dependência de OR no Nome de Rede. Consulte a secção 'Adicionar recurso de endereço IP na mesma sub-rede' do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Verifique a configuração/propagação do DNS aos clientes.
* Migrar SQL1 VM, e passar pelos passos 2 - 4.
* Se utilizar os passos 5ii, adicione SQL1 como possível proprietário para o recurso ip address adicionado
* Teste de failovers.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilizar réplicas secundárias existentes): Multi-Site

Se tiver nós em mais de um datacenter (DC) ou se tiver um ambiente híbrido, então pode utilizar uma configuração Always On neste ambiente para minimizar o tempo de inatividade.

A abordagem é alterar a sincronização Always On para Synchronous para o local ou secundário Azure DC, e depois falhar para o SQL Server. Em seguida, copie os VHDs para uma conta de Armazenamento Premium e recoloque a máquina num novo serviço de nuvem. Atualize o ouvinte e, em seguida, recue.

##### <a name="points-of-downtime"></a>Pontos de inatividade

O tempo de paragem consiste no tempo de falhar para a alternativa DC e voltar. Também depende da configuração do seu cliente/DNS e a reconexão do seu cliente pode ser adiada.
Considere o seguinte exemplo de uma configuração híbrida Always On:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

* Pode utilizar as infraestruturas existentes.
* Tem a opção de pré-actualizar primeiro o armazenamento Azure no DR Azure DC.
* O armazenamento DR Azure DC pode ser reconfigurado.
* Há um mínimo de dois failovers durante a migração, excluindo os resultados dos testes.
* Não é necessário mover os dados do SQL Server com cópia de segurança e restaurar.

##### <a name="disadvantages"></a>Desvantagens

* Dependendo do acesso do cliente ao SQL Server, pode haver um aumento da latência quando o SQL Server estiver a funcionar numa DC alternativa à aplicação.
* O tempo de cópia dos VHDs para o armazenamento Premium pode ser longo. Isto pode afetar a sua decisão de manter o nó no Grupo Disponibilidade. Considere isto para quando as cargas de trabalho intensivas de log estão em execução durante a migração é necessária, uma vez que o nó primário tem de manter as transações não reabastecidas no seu registo de transações. Portanto, isto pode crescer significativamente.
* Este cenário utilizaria o comando **Azure Start-AzureStorageBlobCopy,** que é assíncrona. Não há SLA no final. O tempo das cópias varia, enquanto isso depende da espera na fila, também depende da quantidade de dados para transferir. Portanto, você só tem um nó no seu segundo centro de dados, você deve tomar medidas de mitigação no caso de a cópia demorar mais tempo do que em testes. Estas medidas de mitigação incluem as seguintes ideias:
  * Adicione um nó 2º SQL temporário para HA antes da migração com tempo de inatividade acordado.
  * Executar a migração fora da manutenção programada de Azure.
  * Certifique-se de que configura o quórum do cluster corretamente.

Este cenário pressupõe que documentou a sua instalação e sabe como o armazenamento é mapeado de forma a fazer alterações para configurações ótimas de cache de disco.

##### <a name="high-level-steps"></a>Passos gerais

![Multisite2][10]

* Faça do Azure DC alternativo o SQL Server Primary e faça dele o outro Parceiro de Falha Automática (AFP).
* Recolher informações de configuração do disco a partir do SQL2 e remover o nó (não apagar VHDs anexados).
* Crie uma conta de Armazenamento Premium e copie VHDs da conta de Armazenamento Padrão.
* Crie um novo serviço de nuvem e crie o SQL2 VM com os seus discos de armazenamento Premiums ligados.
* Configure ILB / ELB e adicione Pontos finais.
* Atualize o Always On Listener com o novo endereço IP ILB /ELB e teste failover.
* Verifique a configuração do DNS.
* Mude o AFP para SQL2 e, em seguida, migrar SQL1 e passar pelos passos 2 - 5.
* Teste de failovers.
* Mude o AFP de volta para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apêndice: Migrar um Multisite Sempre No Cluster para Armazenamento Premium

O restante deste artigo fornece um exemplo detalhado de conversão de um multi-site Always On cluster para armazenamento Premium. Também converte o Ouvinte de utilizar um equilibrador de carga externo (ELB) para um equilibrador de carga interno (ILB).

### <a name="environment"></a>Ambiente

* Windows 2k12 / SQL 2k12
* 1 Ficheiros DB em SP
* 2 x Piscinas de Armazenamento por Nó

![Apêndice1][11]

### <a name="vm"></a>VM:

Neste exemplo, vamos demonstrar a mudança de um ELB para o ILB. O ELB estava disponível antes do ILB, o que mostra como mudar para ILB durante a migração.

![Apêndice2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pré-etapas: Ligar à subscrição

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passo 2: Aumentar as falhas permitidas nos recursos\<Optional>

Em certos recursos que pertencem ao seu Grupo Always On Availability existem limites para quantas falhas podem ocorrer num período, onde o serviço de cluster tenta reiniciar o grupo de recursos. Recomenda-se que aumente este procedimento enquanto passa por este procedimento, uma vez que se não falhar manualmente e desencadear falhas ao desligar as máquinas, pode aproximar-se deste limite.

Seria prudente duplicar o subsídio de incumprimento, para o fazer no Failover Cluster Manager, ir para as Propriedades do grupo de recursos Always On:

![Apêndice3][13]

Altere as Falhas Máximas para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passo 3: Recurso ip de adição para o Grupo Cluster\<Optional>

Se tiver apenas um endereço IP para o Cluster Group e este estiver alinhado com a sub-rede de nuvem, tenha cuidado, se acidentalmente desativar todos os nós de cluster na nuvem dessa rede, então o recurso Cluster IP e o Cluster Network Name não poderão estar online. Nesta situação, evita atualizações a outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Passo 4: Configuração dns

Implementar uma transição suave depende da forma como o DNS está a ser utilizado e atualizado.
Quando o Always On é instalado, cria um grupo de Recursos do Cluster Windows, se abrir o Failover Cluster Manager, vê que no mínimo tem três recursos, os dois a que o documento se refere são:

* Nome de Rede Virtual (VNN) – o nome DNS a que os clientes se conectam quando querem ligar-se aos Servidores SQL via Always On.
* Ip Address Resource – o endereço IP que está associado ao VNN, pode ter mais de um, e numa configuração multisite tem um endereço IP por site/sub-rede.

Ao ligar-se ao SQL Server, o controlador sql Server Client recupera os registos DNS associados ao ouvinte e tenta ligar-se a cada endereço IP associado. Em seguida, discutimos alguns fatores que podem influenciar isto.

O número de registos DNS simultâneos associados ao nome do ouvinte depende não só do número de endereços IP associados, mas também da definição de 'RegisterAllIpProviders' no Clustering Failover para o recurso Always ON VNN.

Quando implementa Always On in Azure existem diferentes passos para criar os endereços Listener e IP, tem de configurar manualmente o 'RegisterAllIpProviders' para 1, isto é diferente de um local Sempre Na implantação onde já está definido para 1.

Se 'RegisterAllIpProviders' for 0, então só vê um registo DNS em DNS associado ao Ouvinte:

![Apêndice4][14]

Se 'RegisterAllIpProviders' for 1:

![Apêndice55][15]

O código abaixo despeja as definições de VNN e define-as para si. Para que a mudança entre em vigor, é necessário desligar o VNN e voltar a ligá-lo. Isto retira o Ouvinte offline causando perturbações na conectividade do cliente.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Numa etapa de migração posterior, é necessário atualizar o ouvinte Always On com um endereço IP atualizado que faz referência a um equilibrador de carga, isto envolve uma remoção e adição de recursos ip Address. Após a atualização IP, é necessário garantir que o novo endereço IP foi atualizado na Zona DNS e que os clientes estão a atualizar a cache de DNS local.

Se os seus clientes residirem num segmento de rede diferente e referenciar um servidor DNS diferente, tem de considerar o que acontece sobre a Transferência da Zona DNS durante a migração, uma vez que o tempo de reconexão da aplicação é limitado pelo menos pelo tempo de transferência de zona de quaisquer novos endereços IP para o ouvinte. Se estiver sob restrição de tempo aqui, deverá discutir e testar forçando uma transferência de zona incremental com as suas equipas Windows, e também colocar o registo de anfitrião DNS num tempo mais baixo para viver (TTL), para que os clientes atualização. Para obter mais informações, consulte [As Transferências de Zona Incremental](https://technet.microsoft.com/library/cc958973.aspx) e o [Start-DnsServerZoneTransfer.](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer)

Por predefinição, o TTL para DNS Record que está associado ao Ouvinte em Always On in Azure é de 1200 segundos. Pode desejar reduzi-lo se estiver sob restrição de tempo durante a sua migração para garantir que os clientes atualizem os seus DNS com o endereço IP atualizado para o ouvinte. Pode ver e modificar a configuração desativando a configuração do VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Quanto mais baixo o 'HostRecordTTL', ocorre uma maior quantidade de tráfego DNS.

##### <a name="client-application-settings"></a>Definições de aplicação de cliente

Se a sua aplicação de cliente SQL suportar o .NET 4.5 SQLClient, então pode utilizar a palavra-chave 'MULTISUBNETFAILOVER=TRUE'. Esta palavra-chave deve ser aplicada, pois permite uma ligação mais rápida ao SqL Always On Availability Group durante o failover. Enumera através de todos os endereços IP associados ao ouvinte Always On em paralelo, e executa uma velocidade de reluto de ligação TCP mais agressiva durante uma falha.

Para obter mais informações sobre as definições anteriores, consulte [a palavra-chave MultiSubnetFailover e as Funcionalidades Associadas.](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover) Consulte também [o Suporte SqlClient para Alta Disponibilidade, Recuperação de Desastres.](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx)

#### <a name="step-5-cluster-quorum-settings"></a>Passo 5: Configurações do quórum do cluster

Como vai retirar pelo menos um SQL Server de cada vez, deve modificar a definição do quórum do cluster, se utilizar o File Share Witness (FSW) com dois nós, deve definir o quórum para permitir a maioria do nó e utilizar o voto dinâmico, permitindo que um único nó permaneça de pé.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Para obter mais informações sobre a gestão e configuração do quórum do cluster, consulte [Configure e Gerencie o Quórum num Cluster failover do Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Passo 6: Extrair pontos finais e ACLs existentes

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Guarde este texto para um ficheiro.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passo 7: Alterar parceiros de failover e modos de replicação

Se tiver mais de dois SQL Servers, deve alterar o failover de outro secundário em outro DC ou no local para 'Synchronous' e torná-lo um Parceiro Automático de Failover (AFP), isto é, manter HA enquanto está a fazer alterações. Pode fazê-lo através do TSQL de modificar embora sSMS:

![Apêndice6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passo 8: Remover VM secundário do serviço de nuvem

Devias estar a planear migrar um nó secundário de nuvem primeiro. Se este nó for atualmente primário, deverá iniciar uma falha manual.

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
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
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

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 9: Alterar as definições de cache do disco no ficheiro CSV e guardar

Para volumes de dados, estes devem ser definidos para READONLY.

Para os volumes de TLOG, estes devem ser definidos para NENHUM.

![Apêndice7][17]

#### <a name="step-10-copy-vhds"></a>Passo 10: VHDs de cópia

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


Pode verificar o estado da cópia dos VHDs na conta de Armazenamento Premium:

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

![Apêndice8][18]

Espere até que tudo isto esteja registado como sucesso.

Para obter informações para bolhas individuais:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Passo 11: Registar disco OS

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Passo 12: Importar secundário para novo serviço de nuvem

O código abaixo também utiliza a opção adicional aqui pode importar a máquina e usar o VIP retenível.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Passo 13: Criar ILB em New Cloud Svc, Adicionar Pontos finais equilibrados de carga e ACLs

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

#### <a name="step-14-update-always-on"></a>Passo 14: Atualizar sempre ligado

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

![Apêndice9][19]

Agora remova o antigo endereço IP do serviço de nuvem.

![Apêndice10][20]

#### <a name="step-15-dns-update-check"></a>Passo 15: Verificação da atualização do DNS

Deverá agora verificar os Servidores DNS nas redes de clientes do SQL Server e certificar-se de que o clustering adicionou o registo extra de anfitrião para o endereço IP adicionado. Se esses servidores DNS não tiverem sido atualizados, considere forçar uma transferência de DNS Zone e certifique-se de que os clientes da sub-rede podem resolver ambos os endereços Sempre Em IP, pelo que não precisa de esperar pela replicação automática de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Passo 16: Reconfigurar Sempre Ligado

Neste ponto, você espera pelo nó secundário que foi migrado para ressincronizar totalmente com o nó no local e mudar para nó de replicação sincronizada e torná-lo o AFP.  

#### <a name="step-17-migrate-second-node"></a>Passo 17: Migrar segundo nó

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
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
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

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Passo 18: Alterar as definições de cache do disco no ficheiro CSV e guardar

Para volumes de dados, as definições de cache devem ser definidas para READONLY.

Para os volumes de TLOG, as definições de cache devem ser definidas para NENHUM.

![Apêndice11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Passo 19: Criar nova conta de armazenamento independente para nó secundário

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

#### <a name="step-20-copy-vhds"></a>Passo 20: VHDs de cópia

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

Pode verificar o estado da cópia VHD para todos os VHDs:

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

![Apêndice12][22]

Espere até que tudo isto esteja registado como sucesso.

Para obter informações para bolhas individuais:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Passo 21: Registar disco OS

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Passo 22: Adicionar pontos finais equilibrados de carga e ACLs

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

#### <a name="step-23-test-failover"></a>Passo 23: Teste falhado

Aguarde que o nó migrado se sincronize com o nó sempre no local. Coloque-o no modo de replicação sincronizado e aguarde até que seja sincronizado. Em seguida, falha do local para o primeiro nó migrado, que é a AFP. Uma vez que tenha funcionado, mude o último nó migrado para a AFP.

Deve testar falhas entre todos os nós e correr até testes de caos para garantir que as falhas funcionam como esperado e numa mansão oportuna.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Passo 24: Recoloe as definições do quórum do cluster / DNS TTL / Failover Pntrs / Sync Settings

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionar recurso de endereço IP na mesma sub-rede

Se tiver apenas dois SQL Servers e quiser emigrá-los para um novo serviço de cloud, mas quiser mantê-los na mesma sub-rede, pode evitar tirar o ouvinte offline para eliminar o endereço ip original e adicionar o Novo Endereço IP. Se estiver a migrar os VMs para outra sub-rede, não precisa de o fazer, uma vez que existe uma rede de clusters adicional que faz referência a essa sub-rede.

Depois de ter falado no secundário migrado e adicionado no novo recurso IP Address para o novo serviço de nuvem antes de falhar na Primária existente, deverá dar estes passos dentro do Cluster Failover Manager:

Para adicionar no Endereço IP, consulte o apêndice, passo 14.

1. Para o recurso ip address atual, altere o possível proprietário para 'Servidor SQL Primário Existente', no exemplo, 'dansqlams4':

    ![Apêndice13][23]
2. Para o novo recurso IP Address, altere o possível proprietário para 'Servidor SQL secundário migrado', no exemplo, 'dansqlams5':

    ![Apêndice14][24]
3. Uma vez definido este conjunto, você pode falhar, e quando o último nó é migrado, os Possíveis Proprietários devem ser editados de modo a que o nó seja adicionado como possível Proprietário:

    ![Apêndice15][25]

## <a name="additional-resources"></a>Recursos adicionais

* [Armazenamento Azure Premium](../disks-types.md)
* [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)
* [Servidor SQL em Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

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
