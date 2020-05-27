---
title: Utilize o Armazenamento Premium Azure com o Servidor SQL [ SQL Server] Microsoft Docs
description: Este artigo utiliza recursos criados com o modelo de implementação clássico, e dá orientação sobre a utilização do Armazenamento Premium Azure com o SQL Server a funcionar em Máquinas Virtuais Azure.
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
ms.openlocfilehash: 07e8d2b6bd22029a4b6556ada62985167807eb77
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833936"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilizar o Armazenamento Premium do Azure com o SQL Server em Máquinas Virtuais

## <a name="overview"></a>Descrição geral

[Os SSDs premium Azure](../disks-types.md) são a próxima geração de armazenamento que proporciona baixa latência e alta produção IO. Funciona melhor para cargas de trabalho intensivas chave IO, como O Servidor SQL em [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)IaaS .

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo fornece planeamento e orientação para migrar uma Máquina Virtual que executa o Servidor SQL para utilizar o Armazenamento Premium. Isto inclui a infraestrutura Azure (networking, armazenamento) e os passos VM do Windows convidado. O exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) mostra um fim completo e abrangente para a migração final de como mover VMs maiores para tirar partido de um melhor armazenamento local de SSD com powerShell.

É importante compreender o processo de ponta a ponta da utilização do Armazenamento Premium Azure com o SQL Server em VMs IAAS. Isto inclui:

* Identificação dos pré-requisitos para a utilização do Armazenamento Premium.
* Exemplos de implementação do SQL Server no IaaS para o Armazenamento Premium para novas implementações.
* Exemplos de implementações existentes em migração, tanto servidores autónomos como implementações usando Grupos SQL Always On Availability.
* Possíveis abordagens migratórias.
* Exemplo completo de ponta a ponta mostrando passos do Servidor Azure, Windows e SQL para a migração de uma implementação sempre existente.

Para obter mais informações sobre o Servidor SQL em Máquinas Virtuais Azure, consulte [o Servidor SQL em Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **Revisores Técnicos:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Pré-requisitos para armazenamento premium

Existem vários pré-requisitos para a utilização do Armazenamento Premium.

### <a name="machine-size"></a>Tamanho da máquina

Para utilizar o Armazenamento Premium, é necessário utilizar máquinas virtuais da série DS (VM). Se não utilizou máquinas da Série DS no seu serviço de nuvem antes, deve eliminar o VM existente, manter os discos ligados e, em seguida, criar um novo serviço de nuvem antes de recriar o tamanho da função VM como DS*. Para obter mais informações sobre tamanhos de máquina virtual, consulte tamanhos de serviço de [máquina virtual e cloud para Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Serviços em nuvem

Só pode utilizar VMs DS* com Armazenamento Premium quando são criados num novo serviço na nuvem. Se estiver a utilizar o Servidor SQL Always On in Azure, o Always On Listener refere-se ao endereço IP do Equilíbrio de Carga Interna ou Externa Azure que está associado a um serviço na nuvem. Este artigo centra-se na forma de migrar, mantendo a disponibilidade neste cenário.

> [!NOTE]
> Uma Série DS* deve ser o primeiro VM que é implantado para o novo Serviço cloud.
>
>

### <a name="regional-vnets"></a>VNETS Regionais

Para DS* VMs tem de configurar a Rede Virtual (VNET) que acolhe os seus VMs para ser regional. Isto "alarga" o VNET para permitir que os VMmaiors sejam aprovisionados noutros clusters e permitir a comunicação entre eles. Na imagem a seguir, o local destacado mostra VNETs regionais, enquanto o primeiro resultado mostra um VNET "estreito".

![RegionalVNET][1]

Você pode levantar um bilhete de suporte microsoft para migrar para um VNET regional. A Microsoft faz então uma mudança. Para completar a migração para VNETs regionais, altere a propriedade AffinityGroup na configuração da rede. Primeiro exporte a Configuração de Rede em PowerShell e, em seguida, substitua a propriedade **AffinityGroup** no elemento **VirtualNetworkSite** por uma propriedade **de Localização.** Especifique `Location = XXXX` onde `XXXX` fica uma região de Azure. Em seguida, importar a nova configuração.

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

É necessário criar uma nova conta de armazenamento configurada para o Armazenamento Premium. Note que a utilização de Armazenamento Premium está definida na conta de armazenamento, não em VHDs individuais, no entanto, ao utilizar um VM série DS* pode anexar VHD's a partir de contas Premium e Standard Storage. Pode considerar isso se não quiser colocar o VHD OS na conta de Armazenamento Premium.

O seguinte comando **New-AzureStorageAccountPowerShell** com o **tipo** "Premium_LRS" cria uma Conta de Armazenamento Premium:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>Definições de cache VHDs

A principal diferença entre a criação de discos que fazem parte de uma conta de Armazenamento Premium é a definição de cache do disco. Para os discos de volume de dados do Servidor SQL, recomenda-se que utilize '**Leia o Caching**'. Para os volumes de registo de transações, a definição de cache do disco deve ser definida para '**Nenhum**'. Isto é diferente das recomendações para as contas de Armazenamento Padrão.

Uma vez fixados os VHDs, a regulação da cache não pode ser alterada. Você precisaria de desprender e recolocar o VHD com uma definição de cache atualizada.

### <a name="windows-storage-spaces"></a>Espaços de armazenamento de janelas

Pode utilizar espaços de [armazenamento windows](https://technet.microsoft.com/library/hh831739.aspx) como fez com o anterior Armazenamento Standard, isto permite migrar um VM que já está a utilizar espaços de armazenamento. O exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (passo 9 e para a frente) demonstra o código Powershell para extrair e importar um VM com múltiplos VHDs anexados.

As Piscinas de Armazenamento foram usadas com a conta de armazenamento Standard Azure para melhorar a entrada e reduzir a latência. Você pode encontrar valor em testar Piscinas de Armazenamento com Armazenamento Premium para novas implementações, mas eles adicionam complexidade adicional com configuração de armazenamento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Como descobrir qual o mapa de Discos Virtuais Azure para piscinas de armazenamento

Como existem diferentes recomendações de definição de cache para VHDs anexados, você pode decidir copiar os VHDs para uma conta de Armazenamento Premium. No entanto, quando os voltar a ligar à nova série DS VM, poderá ter de alterar as definições de cache. É mais simples aplicar as definições de cache recomendadas pelo Armazenamento Premium quando tem VHDs separados para os ficheiros de Dados SQL e ficheiros de registo (em vez de um único VHD que contém ambos).

> [!NOTE]
> Se tiver dados do SQL Server e ficheiros de registo no mesmo volume, a opção de cache que escolher depende dos padrões de acesso da IO para as suas cargas de trabalho na base de dados. Só os testes podem demonstrar qual a opção de cache é a melhor para este cenário.
>
>

No entanto, se estiver a utilizar espaços de armazenamento do Windows que são compostos por vários VHDs, precisa de ver os seus scripts originais para identificar quais os VHDs anexados em que piscina específica, para que possa definir as definições de cache em conformidade para cada disco.

Se não tiver um script original disponível para mostrar quais vHDs mapeiam para o pool de armazenamento, pode usar os seguintes passos para determinar o mapeamento do disco/armazenamento da piscina.

Para cada disco, utilize os seguintes passos:

1. Obtenha a lista dos discos anexados à VM com o comando **Get-AzureVM:**

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. Reparem no DiskName e no LUN.

    ![Nome de discoAndLUN][2]
1. Ambiente de trabalho remoto no VM. Em seguida, vá para **computer management**  |  **device manager**disk  |  **drives**. Veja as propriedades de cada um dos 'Discos Virtuais da Microsoft'

    ![Propriedades VirtualDisk][3]
1. O número lun aqui é uma referência ao número LUN que especifica ao anexar o VHD ao VM.
1. Para o 'Microsoft Virtual Disk' vá ao separador **Detalhes,** depois na lista **de propriedades,** vá para a **Chave do Condutor**. No **Valor**, note o **Offset**, que é 0002 na seguinte imagem. O 0002 denota o PhysicalDisk2 que o depósito refere.

    ![Detalhes de Propriedade VirtualDisk][4]
1. Para cada piscina de armazenamento, despeje os discos associados:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Agora pode utilizar estas informações para associar VHDs anexados a Discos Físicos em Piscinas de Armazenamento.

Depois de ter mapeado VHDs em Discos Físicos em Piscinas de Armazenamento, pode então desmontá-los e copiá-los para uma conta de Armazenamento Premium e, em seguida, anexá-los com a definição de cache correta. Veja o exemplo no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage), passos 8 a 12. Estes passos mostram como extrair uma configuração de disco VHD anexada a VM para um ficheiro CSV, copiar os VHDs, alterar as definições de cache de configuração do disco e, finalmente, reimplantar o VM como um VM série DS com todos os discos anexados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Largura de banda de armazenamento VM e entrada de armazenamento VHD

A quantidade de desempenho de armazenamento depende do tamanho DS* VM especificado e dos tamanhos VHD. Os VMs têm diferentes licenças para o número de VHDs que podem ser anexados e a largura de banda máxima que suportam (MB/s). Para obter os números específicos de largura de banda, consulte tamanhos de serviço de [máquina virtual e cloud para Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

O aumento dos IOPS é alcançado com tamanhos de disco maiores. Devia supor isso quando pensa no seu caminho de migração. Para mais detalhes, [consulte a tabela para iOPS e tipos](../disks-types.md#premium-ssd)de discos .

Por fim, considere que os VMs têm diferentes larguras de banda máximas de disco que suportam para todos os discos ligados. Sob alta carga, pode saturar a largura de banda máxima disponível para o tamanho da função VM. Por exemplo, uma Standard_DS14 suporta até 512MB/s; portanto, com três discos P30 pode saturar a largura de banda do vM do disco. Mas neste exemplo, o limite de entrada poderia ser ultrapassado dependendo da mistura de iOs de leitura e escrita.

## <a name="new-deployments"></a>Novas implementações

As próximas duas secções demonstram como pode implantar VMs do Servidor SQL para armazenamento premium. Como mencionado anteriormente, não precisa necessariamente de colocar o disco OS no armazenamento Premium. Você pode optar por fazê-lo se pretende colocar qualquer carga de trabalho intensiva de IO no VHD OS.

O primeiro exemplo demonstra a utilização das imagens existentes da Azure Gallery Images. O segundo exemplo mostra como usar uma imagem VM personalizada que você tem numa conta de armazenamento Standard existente.

> [!NOTE]
> Estes exemplos assumem que já criou um VNET regional.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Crie um novo VM com Armazenamento Premium com Imagem de Galeria

O exemplo abaixo mostra como colocar o VHD OS no armazenamento premium e anexar VHDs de armazenamento premium. No entanto, também pode colocar o disco OS numa conta de Armazenamento Padrão e, em seguida, anexar VHDs que residem numa conta de Armazenamento Premium. Ambos os cenários são demonstrados.

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

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Passo 3: Reserve um serviço de nuvem VIP (Opcional)

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

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Passo 5: Colocação de Os VHD em Armazenamento Standard ou Premium

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

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Crie um novo VM para usar o Armazenamento Premium com uma imagem personalizada

Este cenário demonstra onde tem imagens personalizadas existentes que residem numa conta de Armazenamento Padrão. Como mencionado se pretender colocar o OS VHD no Armazenamento Premium, precisa copiar a imagem que existe na conta De armazenamento padrão e transferi-la para um Armazenamento Premium antes de poder ser utilizada. Se tiver uma imagem no local, também pode utilizar este método para copiar diretamente para a conta de Armazenamento Premium.

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

#### <a name="step-3-use-existing-image"></a>Passo 3: Utilize a imagem existente

Pode usar uma imagem existente. Ou pode [tirar uma imagem de uma máquina existente.](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) Note que a máquina que você imagem não tem que ser máquina DS*. Uma vez que tenha a imagem, os seguintes passos mostram como copiá-la para a conta de Armazenamento Premium com o comando **Start-AzureStorageBlobCopy** PowerShell.

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

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Passo 4: Copiar Blob entre Contas de Armazenamento

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

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Passo 6: Adicionar disco de imagem ao Repositório de disco Azure em Subscrição

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Pode descobrir que, mesmo que o estado reporte como sucesso, ainda pode ter um erro de locação de disco. Neste caso, espere cerca de 10 minutos.

#### <a name="step-7--build-the-vm"></a>Passo 7: Construir o VM

Aqui está a construir o VM a partir da sua imagem e a anexar dois VHDs de Armazenamento Premium:

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

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementações existentes que não usam Sempre Em Grupos de Disponibilidade

> [!NOTE]
> Para as implementações existentes, consulte primeiro a secção [pré-requisitos](#prerequisites-for-premium-storage) deste artigo.

Existem diferentes considerações para implementações do SQL Server que não usam sempre em grupos de disponibilidade e aqueles que o fazem. Se não estiver a utilizar o Always On e tiver um Servidor SQL autónomo existente, pode fazer upgrade para o Armazenamento Premium utilizando um novo serviço de nuvem e uma conta de armazenamento. Considere as seguintes opções:

* **Crie um novo VM do Servidor SQL**. Pode criar um novo VM de Servidor SQL que utiliza uma conta de Armazenamento Premium, conforme documentado em Novas Implementações. Em seguida, volte a fazer a sua cópia de segurança e restaure as bases de dados do SQL Server e do utilizador. A aplicação precisa de ser atualizada para fazer referência ao novo Servidor SQL se estiver a ser acedido interna ou externamente. Precisaria de copiar todos os objetos 'out of db' como se estivesse a fazer uma migração SQL Server Side by Side (SxS). Isto inclui objetos como logins, certificados e servidores ligados.
* **Migrar um VM de servidor SQL existente**. Isto requer que o SQL Server VM seja desligado, transferindo-o depois para um novo serviço na nuvem, que inclui copiar todos os seus VHDs anexados para a conta de Armazenamento Premium. Quando o VM entra on-line, a aplicação refere o nome do servidor como antes. Esteja ciente de que o tamanho do disco existente afeta as características de desempenho. Por exemplo, um disco de 400 GB é arredondado até um P20. Se souber que não necessita desse desempenho no disco, poderá recriar o VM como VM série DS e anexar VHDs de armazenamento Premium da especificação de tamanho/desempenho que necessita. Depois, pode desprender e voltar a colocar os ficheiros SQL DB.

> [!NOTE]
> Ao copiar os discos VHD deve estar ciente do tamanho, dependendo do tamanho significa em que tipo de Disco de Armazenamento Premium se insere, isto determina a especificação de desempenho do disco. O Azure roda até ao tamanho do disco mais próximo, por isso, se tiver um disco de 400GB, este é arredondado até um P20. Dependendo dos requisitos io existentes do VHD os solado, pode não precisar de migrar para uma conta de Armazenamento Premium.

Se o seu Servidor SQL for acedido externamente, o serviço de nuvem VIP muda. Também tem de atualizar as definições de pontos finais, ACLs e DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementações existentes que usam sempre em grupos de disponibilidade

> [!NOTE]
> Para as implementações existentes, consulte primeiro a secção [pré-requisitos](#prerequisites-for-premium-storage) deste artigo.

Inicialmente nesta secção, vemos como o Always On interage com o Azure Networking. Em seguida, decomparamos as migrações em dois cenários: migrações onde algum tempo de inatividade pode ser tolerado e migrações onde é preciso atingir o mínimo de tempo de inatividade.

No local, os grupos De SQL Server Always On Availability utilizam um Ouvinte no local que regista um nome Virtual DNS juntamente com um endereço IP que é partilhado entre um ou mais Servidores SQL. Quando os clientes ligam são encaminhados através do IP ouvinte para o Servidor SQL Primário. Este é o servidor que detém o recurso Always On IP na altura.

![ImplementaçõesUse Sempre Ligado][6]

No Microsoft Azure pode ter apenas um endereço IP atribuído a um NIC no VM, por isso, para obter a mesma camada de abstração que no local, o Azure utiliza o endereço IP que é atribuído aos Equilibradores de Carga Interna/Externa (ILB/ELB). O recurso IP que é partilhado entre os servidores é definido para o mesmo IP que o ILB/ELB. Isto é publicado no DNS, e o tráfego do cliente é passado através do ILB/ELB para a réplica primary SQL Server. O ILB/ELB sabe qual o Servidor SQL que é primário, uma vez que utiliza sondas para sondar o recurso Always On IP. No exemplo anterior, sonda cada nó que tem um ponto final referenciado pelo ELB/ILB, o que responder é o Servidor SQL Primário.

> [!NOTE]
> O ILB e o ELB são ambos atribuídos a um determinado serviço de nuvem Azure, pelo que qualquer migração em nuvem em Azure significa, muito provavelmente, que o IP do Balancer de Carga muda.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migrar sempre em implementações que podem permitir algum tempo de inatividade

Existem duas estratégias para migrar Sempre Em implementações que permitem algum tempo de inatividade:

1. **Adicione mais réplicas secundárias a um cluster sempre existente**
2. **Migrar para um novo Sempre On Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Adicione mais Réplicas Secundárias a um aglomerado existente sempre em cluster

Uma estratégia é adicionar mais secundárias ao Grupo Sempre Disponível. Você precisa adicioná-los em um novo serviço na nuvem e atualizar o ouvinte com o novo balanceor de carga IP.

##### <a name="points-of-downtime"></a>Pontos de inatividade:

* Validação do Cluster.
* Testar sempre em falhas para novos secundários.

Se estiver a utilizar pools de armazenamento do Windows dentro do VM para uma maior entrada de IO, então estes são retirados offline durante uma Validação completa do cluster. O teste de validação é necessário quando adiciona nós ao cluster. O tempo que leva para executar o teste pode variar, por isso deve testar isso no seu ambiente de teste representativo para obter um tempo aproximado de quanto tempo isto demora.

Deve fornecer tempo para realizar testes de failover manual e caos nos recém-adicionados nódosos para garantir as funções sempre em alta disponibilidade, como esperado.

![ImplantaçãoUseAlways On2][7]

> [!NOTE]
> Deve parar todas as instâncias do Servidor SQL onde as Piscinas de Armazenamento são utilizadas antes da validação.
>
> ##### <a name="high-level-steps"></a>Passos gerais
>

1. Crie dois novos Servidores SQL em novo serviço na nuvem com armazenamento Premium anexado.
2. Copie as cópias de cópias completas e restaure com **NORECOVERY**.
3. Copiar sobre objetos dependentes 'fora do utilizador DB', tais como logins, etc.
4. Crie um novo Balancer de Carga Interna (ILB) ou utilize um Balancer de Carga Externa (ELB) e, em seguida, crie pontos finais equilibrados de carga em ambos os novos nós.

   > [!NOTE]
   > Verifique se todos os Nós têm a configuração de Ponto Final correto antes de continuar
   >
   >
5. Parar o acesso ao utilizador/aplicação ao servidor SQL (se utilizar piscinas de armazenamento).
6. Pare os serviços de motor do servidor SQL em todos os nós (se utilizar piscinas de armazenamento).
7. Adicione novos Nós ao cluster e execute a validação completa.
8. Assim que a Validação for bem sucedida, inicie todos os Serviços de ServidorEs SQL.
9. Registos de transações de backup e restauração das bases de dados dos utilizadores.
10. Adicione novos nódos os nódoos no Grupo Sempre Em Disponibilidade e coloque a replicação em **Synchronous**.
11. Adicione o recurso de endereço IP do novo Serviço cloud ILB/ELB através do PowerShell for Always On com base no exemplo multi-site no [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). No clustering Windows, detete os **possíveis proprietários** do recurso **IP Address** para os novos nós antigos. Consulte a secção "Adicionar recurso ip endereço na mesma subnet" do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Falha para um dos novos nódosos.
13. Faça os novos nódosos Auto Failover Partners e teste falhas.
14. Remova os nódosos originais do Grupo de Disponibilidade.

##### <a name="advantages"></a>Vantagens

* Os novos Servidores SQL podem ser testados (SQL Server e Application) antes de serem adicionados ao Always On.
* Pode alterar o tamanho do VM e personalizar o armazenamento para os seus requisitos exatos. No entanto, seria benéfico manter todos os caminhos dos ficheiros SQL iguais.
* Pode controlar quando a transferência dos backups DB para as Réplicas Secundárias for iniciada. Isto difere da utilização do comando **Azure Start-AzureStorageBlobCopy** para copiar VHDs, porque esta é uma cópia assíncrona.

##### <a name="disadvantages"></a>Desvantagens

* Ao utilizar piscinas de armazenamento do Windows, há tempo de paragem do Cluster durante a Validação completa do cluster para os novos nós adicionais.
* Dependendo da Versão SQL Server e do número existente de réplicas secundárias, poderá não ser capaz de adicionar mais réplicas secundárias sem remover os secundários existentes.
* Pode haver um longo tempo de transferência de dados SQL durante a configuração dos secundários.
* Há custos adicionais durante a migração enquanto você tem novas máquinas funcionando em paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar para um novo Sempre On Cluster

Outra estratégia é criar um novo Sempre On Cluster com novos nós em novo serviço na nuvem e, em seguida, redirecionar os clientes para usá-lo.

##### <a name="points-of-downtime"></a>Pontos de inatividade

Há tempo de inatividade quando transfere aplicações e utilizadores para o novo ouvinte Always On. O tempo de inatividade depende de:

* O tempo que demorou a restaurar as cópias de segurança de registo de transações finais em bases de dados de novos servidores.
* O tempo que leva para atualizar as aplicações do cliente para usar o novo ouvinte Always On.

##### <a name="advantages"></a>Vantagens

* Pode testar o ambiente de produção real, o SQL Server e o SISTEMA de alterações.
* Tem a opção de personalizar o armazenamento e de reduzir potencialmente o tamanho do VM. Isto poderia resultar numa redução de custos.
* Pode atualizar a sua construção ou versão do SQL Server durante este processo. Também pode atualizar o Sistema Operativo.
* O anterior Always On Cluster pode funcionar como um alvo de retrocesso sólido.

##### <a name="disadvantages"></a>Desvantagens

* Tem de alterar o nome DNS do ouvinte se quiser ambos os clusters sempre ligados em simultâneo. Isto adiciona a administração durante a migração, uma vez que as cordas de aplicação do cliente devem refletir o novo nome Listener.
* É necessário implementar um mecanismo de sincronização entre os dois ambientes para os manter o mais próximo possível para minimizar os requisitos finais de sincronização antes da migração.
* Há um custo adicional durante a migração enquanto você tem o novo ambiente em funcionamento.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migrar sempre em implementações para o mínimo de tempo de inatividade

Existem duas estratégias para migrar Sempre Em implementações para o mínimo tempo de inatividade:

1. **Utilize um secundário existente: único site**
2. **Utilizar réplicas secundárias existentes): Multi-Site**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilizar um secundário existente: Single-Site

Uma estratégia para o mínimo de tempo de inatividade é pegar numa nuvem secundária existente e removê-la do serviço de nuvem atual. Em seguida, copie os VHDs para a nova conta de Armazenamento Premium e crie o VM no novo serviço na nuvem. Em seguida, atualize o ouvinte em agrupamento e falha.

##### <a name="points-of-downtime"></a>Pontos de inatividade

* Há tempo de inatividade quando actualizao o nó final com o ponto final equilibrado.
* A reconexão do seu cliente pode atrasar-se dependendo da configuração do seu cliente/DNS.
* Existe tempo adicional de inatividade se optar por desligar o grupo Always On Cluster para trocar os endereços IP. Pode evitá-lo utilizando uma dependência de OR e possíveis proprietários para o recurso ip address adicionado. Consulte a secção "Adicionar recurso ip endereço na mesma subnet" do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Quando pretender que o nó adicionado participe como um Parceiro Always On Failover, precisa adicionar um Ponto Final Azure com referência ao Conjunto Equilibrado de Carga. Quando executa o comando **Add-AzureEndpoint** para o fazer, as ligações atuais permanecem abertas, mas novas ligações ao ouvinte não podem ser estabelecidas até que o equilibrador de carga esteja atualizado. Nos testes, este foi visto para durar 90-120segundos, este deve ser testado.

##### <a name="advantages"></a>Vantagens

* Nenhum custo extra incorrido durante a migração.
* Uma migração de um para um.
* Complexidade reduzida.
* Permite um aumento de IOPS a partir de SKUs de Armazenamento Premium. Quando os discos são separados do VM e copiados para o novo serviço de nuvem, uma ferramenta de terceiros pode ser usada para aumentar o tamanho VHD, que fornece maiores potências. Para aumentar os tamanhos de VHD, consulte esta discussão do [fórum.](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)

##### <a name="disadvantages"></a>Desvantagens

* Há uma perda temporária de HA e DR durante a migração.
* Como esta é uma migração 1:1, você tem que usar um tamanho VM mínimo que suporta o seu número de VHDs, para que você não seja capaz de reduzir seus VMs.
* Este cenário utilizaria o comando Azure **Start-AzureStorageBlobCopy,** que é assíncrono. Não há SLA na conclusão da cópia. O tempo das cópias varia, enquanto isso depende da espera na fila, também depende da quantidade de dados para transferir. O tempo de cópia aumenta se a transferência for para outro centro de dados Azure que suporta o Armazenamento Premium noutra região. Se tiver apenas 2 nós, considere uma possível mitigação no caso de a cópia demorar mais tempo do que em testes. Isto pode incluir as seguintes ideias.
  * Adicione um nó temporário de 3º SQL Server para HA antes da migração com o tempo de inatividade acordado.
  * Executar a migração fora da manutenção programada de Azure.
  * Certifique-se de que configura corretamente o quórum do cluster.  

##### <a name="high-level-steps"></a>Passos gerais

Este documento não demonstra um exemplo final completo, no entanto o [Apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fornece detalhes que podem ser alavancados para o executar.

![Tempo de Downtime Mínimo][8]

* Recolha a configuração do disco e retire o nó (não elimine Os VHDs ligados).
* Crie uma conta de armazenamento premium e copie VHDs da conta de Armazenamento Padrão
* Crie um novo serviço de nuvem e reimplante o VM SQL2 nesse serviço de nuvem. Crie o VM utilizando o VHD original copiado e prenda os VHDs copiados.
* Configure ILB / ELB e adicione pontos finais.
* Atualizar Ouvinte por qualquer um deles:
  * Levando o Grupo Always On offline e atualizando o Sempre On Listener com o novo endereço IP ILB/ELB.
  * Ou adicionar o recurso de endereço IP do novo Serviço cloud ILB/ELB através do PowerShell no clustering windows. Em seguida, detete tese os possíveis proprietários do recurso IP Address para o nó migrado, SQL2, e coloque-o como dependência de OR no Nome da Rede. Consulte a secção "Adicionar recurso ip endereço na mesma subnet" do [apêndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Verifique a configuração/propagação do DNS aos clientes.
* Migrar SQL1 VM, e passar pelos passos 2 - 4.
* Se utilizar os passos 5ii, adicione o SQL1 como possível proprietário para o recurso de endereço IP adicionado
* Falhas nos testes.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilizar réplicas secundárias existentes: Multi-Site

Se tiver nós em mais de um datacenter Azure (DC) ou se tiver um ambiente híbrido, então pode utilizar uma configuração Always On neste ambiente para minimizar o tempo de inatividade.

A abordagem é alterar a sincronização Always On para Synchronous para o in-local ou secundário Azure DC, e depois falhar com aquele Servidor SQL. Em seguida, copie os VHDs para uma conta de Armazenamento Premium e reimplante a máquina num novo serviço na nuvem. Atualize o ouvinte e depois volte atrás.

##### <a name="points-of-downtime"></a>Pontos de inatividade

O tempo de inatividade consiste no tempo para falhar com a alternativa DC e voltar. Também depende da configuração do seu cliente/DNS e a reconexão do seu cliente pode ser adiada.
Considere o seguinte exemplo de um híbrido Sempre Na configuração:

![MultiSite1][9]

##### <a name="advantages"></a>Vantagens

* Pode utilizar as infraestruturas existentes.
* Tem a opção de pré-actualizar primeiro o armazenamento Azure no DR Azure DC.
* O armazenamento DR Azure DC pode ser reconfigurado.
* Há um mínimo de duas falhas durante a migração, excluindo falhas nos testes.
* Não é necessário mover os dados do SQL Server com cópia de segurança e restauro.

##### <a name="disadvantages"></a>Desvantagens

* Dependendo do acesso do cliente ao SQL Server, pode haver um aumento da latência quando o SQL Server estiver a funcionar num DC alternativo à aplicação.
* O tempo de cópia dos VHDs para o armazenamento Premium pode ser longo. Isto pode afetar a sua decisão sobre se deve manter o nó no Grupo de Disponibilidade. Considere isto para quando as cargas de trabalho intensivas de registo estão em execução durante a migração é necessária, uma vez que o nó primário tem que manter as transações não replicadas no seu registo de transações. Portanto, isto pode crescer significativamente.
* Este cenário utilizaria o comando Azure **Start-AzureStorageBlobCopy,** que é assíncrono. Não há SLA concluída. O tempo das cópias varia, enquanto isso depende da espera na fila, também depende da quantidade de dados para transferir. Portanto, você só tem um nó no seu 2º data center, você deve tomar medidas de mitigação no caso da cópia demorar mais tempo do que em testes. Estes passos de mitigação incluem as seguintes ideias:
  * Adicione um nó temporário de 2º SQL para HA antes da migração com o tempo de inatividade acordado.
  * Executar a migração fora da manutenção programada de Azure.
  * Certifique-se de que configura corretamente o quórum do cluster.

Este cenário pressupõe que documentou a sua instalação e sabe como o armazenamento é mapeado de forma a fazer alterações para as melhores definições de cache do disco.

##### <a name="high-level-steps"></a>Passos gerais

![Multisite2][10]

* Faça o Azure DC alternativo, o SQL Server Primary, e faça dele o outro Parceiro de Falha automática (AFP).
* Recolha informações de configuração do disco a partir do SQL2 e retire o nó (não elimine Os VHDs ligados).
* Crie uma conta de Armazenamento Premium e copie VHDs da conta De armazenamento padrão.
* Crie um novo serviço na nuvem e crie o VM SQL2 com os seus discos de armazenamento de prémios anexados.
* Configure ILB / ELB e adicione pontos finais.
* Atualize o Sempre On Listener com o novo endereço IP ILB /ELB e teste failover.
* Verifique a configuração do DNS.
* Mude a AFP para SQL2 e, em seguida, emigra o SQL1 e passe pelos passos 2 - 5.
* Falhas nos testes.
* Mude o AFP de volta para SQL1 e SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apêndice: Migrar um multisite sempre em cluster para armazenamento premium

O restante deste artigo fornece um exemplo detalhado de conversão de um multi-site Sempre Em cluster para armazenamento Premium. Também converte o Ouvinte de utilizar um equilibrador de carga externo (ELB) para um equilibrador de carga interna (ILB).

### <a name="environment"></a>Ambiente

* Windows 2k12 / SQL 2k12
* 1 DB Ficheiros em SP
* 2 x Piscinas de Armazenamento por Nó

![Apêndice1][11]

### <a name="vm"></a>VM:

Neste exemplo, vamos demonstrar a mudança de um ELB para o ILB. O ELB estava disponível antes do ILB, o que mostra como mudar para ILB durante a migração.

![Apêndice2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pré-passos: Ligar à subscrição

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

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Passo 2: Aumentar as falhas permitidas nos recursos \< Opcional>

Em certos recursos que pertencem ao seu Grupo Sempre Em Disponibilidade existem limites para quantas falhas podem ocorrer num período, onde o serviço de cluster tenta reiniciar o grupo de recursos. Recomenda-se que aumente isto enquanto estiver a passar por este procedimento, uma vez que se não falhar manualmente e acionar falhas, desligando as máquinas, pode aproximar-se deste limite.

Seria prudente duplicar o subsídio de falência, fazê-lo no Failover Cluster Manager, ir para as Propriedades do grupo de recursos Always On:

![Apêndice3][13]

Alterar as Falhas Máximas para 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Passo 3: Recurso de endereço IP de adição para> opcionais do grupo cluster \<

Se tiver apenas um endereço IP para o Cluster Group e este estiver alinhado com a subnet cloud, tenha cuidado, se acidentalmente desligar todos os nós de cluster na nuvem dessa rede, então o recurso IP cluster e o Cluster Network Name não podem ficar on-line. Nesta situação, evita atualizações a outros recursos de cluster.

#### <a name="step-4-dns-configuration"></a>Passo 4: Configuração DNS

A implementação de uma transição suave depende da forma como o DNS está a ser utilizado e atualizado.
Quando o Always On está instalado, cria um grupo de Recursos de Cluster windows, se abrir o Failover Cluster Manager, vê que no mínimo tem três recursos, os dois a que o documento se refere são:

* Nome de Rede Virtual (VNN) – o nome DNS a que os clientes se ligam quando querem ligar-se aos Servidores SQL via Always On.
* Recurso de endereço IP – o endereço IP associado ao VNN, pode ter mais de um, e numa configuração multisite tem um endereço IP por site/subnet.

Ao ligar-se ao SQL Server, o controlador do Cliente Do Servidor SQL recupera os registos DNS associados ao ouvinte e tenta ligar-se a cada endereço IP sempre ligado. Em seguida, discutimos alguns fatores que podem influenciar isto.

O número de registos dNS simultâneos associados ao nome do ouvinte depende não só do número de endereços IP associados, mas da definição 'RegisterAllIpProviders' no Clustering Failover para o recurso VNN sempre ON.

Quando se implementa o Always On in Azure existem diferentes passos para criar os Endereços Listener e IP, tem de configurar manualmente os 'RegisterAllIpProviders' para 1, isto é diferente de uma implementação no local sempre em funcionamento, onde já está definido para 1.

Se 'RegisterAllIpProviders' for 0, então basta ver um registo DNS em DNS associado ao Ouvinte:

![Apêndice4][14]

Se 'RegisterAllIpProviders' for 1:

![Apêndice5][15]

O código abaixo despeja as definições vNN e define-o para si. Para que a mudança faça efeito, é necessário desligar o VNN e voltar a ligá-lo online. Isto desliga o Ouvinte causando uma perturbação na conectividade do cliente.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Num passo posterior de migração, é necessário atualizar o ouvinte Always On com um endereço IP atualizado que faz referência a um equilibrador de carga, isto envolve uma remoção e adição de recursos ip Address. Após a atualização IP, você precisa garantir que o novo endereço IP foi atualizado na Zona DNS e que os clientes estão atualizando o seu cache DNS local.

Se os seus clientes residem num segmento de rede diferente e referenciarem um servidor DNS diferente, é necessário considerar o que acontece sobre a Transferência de Zona DNS durante a migração, uma vez que o tempo de religação da aplicação é limitado pelo menos pelo tempo de transferência de zona de quaisquer novos endereços IP para o ouvinte. Se estiver sob restrição de tempo aqui, deve discutir e testar forçando uma transferência de zona incremental com as suas equipas Windows, e também colocar o registo de anfitriões dNS para um tempo mais baixo para viver (TTL), para que os clientes atualizem. Para mais informações, consulte [Transferências incrementais](https://technet.microsoft.com/library/cc958973.aspx) de zonas e [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Por defeito, o TTL para DNS Record que está associado ao Ouvinte em Always On in Azure é de 1200 segundos. Poderá pretender reduzi-lo se estiver sob restrição de tempo durante a sua migração para garantir que os clientes atualizem os seus DNS com o endereço IP atualizado para o ouvinte. Pode ver e modificar a configuração despejando a configuração do VNN:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Quanto mais baixo o 'HostRecordTTL', ocorre uma maior quantidade de tráfego de DNS.

##### <a name="client-application-settings"></a>Definições de aplicação do cliente

Se a sua aplicação de cliente SQL suportar o .NET 4.5 SQLClient, então pode usar a palavra-chave 'MULTISUBNETFAILOVER=TRUE'. Esta palavra-chave deve ser aplicada, pois permite uma ligação mais rápida ao Grupo SQL Always On Availability durante a falha. Ele enumera todos os endereços IP associados ao ouvinte Always On em paralelo, e executa uma velocidade de retry de ligação TCP mais agressiva durante uma falha.

Para obter mais informações sobre as definições anteriores, consulte a [palavra-chave MultiSubnetFailover e as funcionalidades associadas](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte também [o Suporte ao Cliente SqlClient para alta disponibilidade, recuperação de desastres.](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx)

#### <a name="step-5-cluster-quorum-settings"></a>Passo 5: Definições de quórum do cluster

Como vai retirar pelo menos um Servidor SQL de cada vez, deve modificar a definição de quórum do cluster, se utilizar file Share Witness (FSW) com dois nós, deve definir o quórum para permitir a maioria do nó e utilizar o voto dinâmico, permitindo que um único nó permaneça de pé.

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

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Passo 7: Alterar parceiros de falha e modos de replicação

Se tiver mais de dois Servidores SQL, deverá alterar a falha de outro secundário noutro DC ou no local para 'Synchronous' e torná-lo um Parceiro De Failover Automático (AFP), isto é para manter o HA enquanto está a fazer alterações. Pode fazê-lo através do TSQL de modificar embora o SSMS:

![Apêndice6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Passo 8: Remover VM secundário do serviço de nuvem

Devias estar a planear migrar um nó secundário em nuvem primeiro. Se este nó estiver atualmente primário, deverá iniciar uma falha manual.

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

Para os volumes de dados, estes devem ser definidos para READONLY.

Para os volumes TLOG, estes devem ser definidos para NENHUM.

![Apêndice7][17]

#### <a name="step-10-copy-vhds"></a>Passo 10: Copy VHDS

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

Espere até que todos estes sejam gravados como sucesso.

Para obter informações sobre bolhas individuais:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>Passo 11: Registar o disco OS

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

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Passo 12: Importar secundário para novo serviço na nuvem

O código abaixo também utiliza a opção adicional aqui que você pode importar a máquina e usar o VIP retentível.

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

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Passo 13: Criar ILB em New Cloud Svc, Adicionar pontos finais equilibrados e ACLs de carga

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

#### <a name="step-14-update-always-on"></a>Passo 14: Atualizar Sempre Ligado

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

#### <a name="step-15-dns-update-check"></a>Passo 15: Verificação de atualização do DNS

Deverá agora verificar os Servidores DNS nas suas redes de clientes Do QL Server e certificar-se de que o clustering adicionou o registo extra do anfitrião para o endereço IP adicionado. Se esses servidores DNS não tiverem atualizado, considere forçar uma transferência de DNS Zone e certifique-se de que os clientes lá dentro subnet são capazes de resolver ambos os Endereços IP sempre em endereços IP, isto é, por isso não precisa esperar pela replicação automática de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Passo 16: Reconfigurar Sempre Ligado

Neste ponto, espera-se pelo nó secundário que foi migrado para ressincronizar totalmente com o nó no local e mudar para o nó de replicação sincronizado e torná-lo a AFP.  

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

Para os volumes TLOG, as definições de cache devem ser definidas para NENHUMA.

![Apêndice11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Passo 19: Criar nova conta de armazenamento independente para o nó secundário

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

#### <a name="step-20-copy-vhds"></a>Passo 20: Copy VHDS

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

Espere até que todos estes sejam gravados como sucesso.

Para obter informações sobre bolhas individuais:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>Passo 21: Registar o disco OS

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

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Passo 22: Adicionar pontos finais equilibrados e ACLs equilibrados

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

#### <a name="step-23-test-failover"></a>Passo 23: Falha no teste

Espere que o nó migrado sincronize com as instalações Sempre No nó. Coloque-o no modo de replicação sincronizada e aguarde até que seja sincronizado. Em seguida, a falha das instalações para o primeiro nó migrado, que é a AFP. Uma vez que tenha funcionado, mude o último nó migrado para a AFP.

Você deve testar falhas entre todos os nós e executar embora testes de caos para garantir que as falhas funcionam como esperado e em uma mansão oportuna.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Passo 24: Repor as definições de quórum do cluster / DNS TTL / Failover Pntrs / Sync Settings

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Adicionar recurso de endereço IP na mesma subnet

Se tiver apenas dois Servidores SQL e pretender emigrar para um novo serviço na nuvem, mas quiser mantê-los na mesma sub-rede, pode evitar desligar o ouvinte para eliminar o endereço IP original E adicionar o Novo Endereço IP. Se estiver a migrar os VMs para outra subneta, não precisa de o fazer, uma vez que existe uma rede de cluster adicional que refere essa sub-rede.

Depois de ter criado o secundário migrado e adicionado no novo recurso IP Address para o novo serviço de nuvem antes de falhar sobre a Primary existente, deve tomar estes passos dentro do Cluster Failover Manager:

Para adicionar o endereço IP, consulte o Apêndice, passo 14.

1. Para o recurso IP Address atual, altere o possível proprietário para 'Server SQL Primário existente', no exemplo, 'dansqlams4':

    ![Apêndice13][23]
2. Para o novo recurso IP Address, altere o possível proprietário para 'SQL Server secundário migrado', no exemplo, 'dansqlams5':

    ![Apêndice14][24]
3. Uma vez definido, pode falhar, e quando o último nó é migrado, os Possíveis Proprietários devem ser editados de modo a que o nó seja adicionado como possível proprietário:

    ![Apêndice15][25]

## <a name="additional-resources"></a>Recursos adicionais

* [Armazenamento Azure Premium](../disks-types.md)
* [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/)
* [Servidor SQL em Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

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
