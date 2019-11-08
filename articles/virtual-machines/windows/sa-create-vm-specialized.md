---
title: Criar VM de um disco especializado no Azure | Microsoft Docs
description: Crie uma nova VM anexando um disco não gerenciado especializado no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cdbf55aae52cec9df1ba34cbeb34c67b8e5fc5d0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749196"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Criar uma VM de um VHD especializado em uma conta de armazenamento

Crie uma nova VM anexando um disco não gerenciado especializado como o disco do sistema operacional usando o PowerShell. Um disco especializado é uma cópia do VHD de uma VM existente que mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. 

Tem duas opções:
* [Carregar um VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copiar o VHD de uma VM do Azure existente](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: carregar um VHD especializado

Você pode carregar o VHD de uma VM especializada criada com uma ferramenta de virtualização local, como o Hyper-V, ou uma VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Você pode carregar um VHD especializado que foi criado usando uma VM local ou um VHD exportado de outra nuvem. Um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Se você pretende usar o VHD como está para criar uma nova VM, verifique se as etapas a seguir foram concluídas. 
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalizar a VM usando Sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização convidados instalados na VM (ou seja, ferramentas do VMware).
  * Verifique se a VM está configurada para extrair seu endereço IP e as configurações de DNS via DHCP. Isso garante que o servidor obtenha um endereço IP na VNet quando ele for iniciado. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para armazenar a imagem de VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Para mostrar as contas de armazenamento disponíveis, digite:

```powershell
Get-AzStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção carregar a imagem da VM.

Se você precisar criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos em que a conta de armazenamento deve ser criada. Para descobrir todos os grupos de recursos que estão em sua assinatura, digite:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Para criar um grupo de recursos chamado **MyResource** Group na região **oeste dos EUA** , digite:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento denominada **mystorageaccount** nesse grupo de recursos usando o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD em sua conta de armazenamento
Use o cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para carregar a imagem em um contêiner em sua conta de armazenamento. Este exemplo carrega o arquivo **myVHD. vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` para uma conta de armazenamento denominada **mystorageaccount** no grupo de recursos **MyResource** Group. O arquivo será colocado no contêiner chamado **MyContainer** e o novo nome de arquivo será **myUploadedVHD. vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se for bem-sucedido, você receberá uma resposta semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da sua conexão de rede e do tamanho do arquivo VHD, esse comando pode demorar um pouco para ser concluído.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opção 2: copiar o VHD de uma VM do Azure existente

Você pode copiar um VHD para outra conta de armazenamento para usar ao criar uma nova VM duplicada.

### <a name="before-you-begin"></a>Antes de começar
Certifique-se de que você:

* Ter informações sobre as **contas de armazenamento de origem e de destino**. Para a VM de origem, você precisa ter os nomes da conta de armazenamento e do contêiner. Normalmente, o nome do contêiner será **VHDs**. Você também precisa ter uma conta de armazenamento de destino. Se você ainda não tiver uma, poderá criar uma usando o portal (**todos os serviços** > contas de armazenamento > Adicionar) ou usando o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) . 
* Baixar e instalar a [ferramenta AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Desalocar a VM
Desaloque a VM, que libera o VHD a ser copiado. 

* **Portal**: clique em **máquinas virtuais** > **myVM** > parar
* **PowerShell**: use [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) para parar (desalocar) a VM denominada **myVM** no grupo de recursos **MyResource**Group.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

O **status** da VM no portal do Azure é alterado de **parado** para **parado (desalocado)** .

### <a name="get-the-storage-account-urls"></a>Obter as URLs da conta de armazenamento
Você precisa das URLs das contas de armazenamento de origem e de destino. As URLs são semelhantes a: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se você já souber a conta de armazenamento e o nome do contêiner, poderá simplesmente substituir as informações entre colchetes para criar a URL. 

Você pode usar o portal do Azure ou o Azure PowerShell para obter a URL:

* **Portal**: clique no **>** para **todos os serviços** > **contas de armazenamento** > **BLOBs** de > de *conta de armazenamento* e o arquivo VHD de origem provavelmente está no contêiner **VHDs** . Clique em **Propriedades** para o contêiner e copie o texto rotulado como **URL**. Você precisará das URLs dos contêineres de origem e de destino. 
* **PowerShell**: use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para obter as informações para a VM denominada **myVM** no grupo de recursos **MyResource**Group. Nos resultados, examine a seção **perfil de armazenamento** do URI do **VHD**. A primeira parte do URI é a URL para o contêiner e a última parte é o nome do VHD do sistema operacional para a VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obter as chaves de acesso de armazenamento
Localize as chaves de acesso para as contas de armazenamento de origem e de destino. Para obter mais informações sobre chaves de acesso, consulte [sobre contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md).

* **Portal**: clique em **todos os serviços** > **contas de armazenamento** > conta de *armazenamento* > **chaves de acesso**. Copie a chave rotulada como **key1**.
* **PowerShell**: use [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) para obter a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo de recursos **MyResource**Group. Copie a chave rotulada como **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copiar o VHD
Você pode copiar arquivos entre contas de armazenamento usando o AzCopy. Para o contêiner de destino, se o contêiner especificado não existir, ele será criado para você. 

Para usar o AzCopy, abra um prompt de comando no computador local e navegue até a pasta em que o AzCopy está instalado. Será semelhante a *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os arquivos dentro de um contêiner, use a opção **/s** . Isso pode ser usado para copiar o VHD do sistema operacional e todos os discos de dados se eles estiverem no mesmo contêiner. Este exemplo mostra como copiar todos os arquivos no contêiner **mysourcecontainer** na conta de armazenamento **mysourcestorageaccount** para o contêiner **mydestinationcontainer** na conta de armazenamento **mydestinationstorageaccount** . Substitua os nomes das contas de armazenamento e contêineres pelos seus próprios. Substitua `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` por suas próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se você quiser apenas copiar um VHD específico em um contêiner com vários arquivos, também poderá especificar o nome do arquivo usando a opção/Pattern Neste exemplo, somente o arquivo chamado **Myfilename. vhd** será copiado.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Quando terminar, você receberá uma mensagem semelhante a:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Resolução de problemas
* Ao usar o AZCopy, se você vir o erro "o servidor falhou ao autenticar a solicitação", verifique se o valor do cabeçalho de autorização está formado corretamente, incluindo a assinatura. Se estiver usando a chave 2 ou a chave de armazenamento secundária, tente usar a chave de armazenamento primária ou 1ª.

## <a name="create-the-new-vm"></a>Criar a nova VM 

Você precisa criar rede e outros recursos de VM para serem usados pela nova VM.

### <a name="create-the-subnet-and-vnet"></a>Criar a sub-rede e a vNet

Crie a vNet e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mysubnet**, no grupo de recursos **MyResource**Group e define o prefixo de endereço de sub-rede como **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a vNet. Este exemplo define o nome da rede virtual como **myVnetName**, o local para o **oeste dos EUA**e o prefixo de endereço para a rede virtual como **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
   Para poder fazer logon em sua VM usando o RDP, você precisa ter uma regra de segurança que permita o acesso RDP na porta 3389. Como o VHD para a nova VM foi criado a partir de uma VM especializada existente, depois que a VM é criada, você pode usar uma conta existente da máquina virtual de origem que tinha permissão para fazer logon usando o RDP.
   Isso precisa ser concluído antes de criar a interface de rede à qual ele será associado.  
   Este exemplo define o nome do NSG como **myNsg** e o nome da regra RDP como **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre pontos de extremidade e regras de NSG, consulte [abrindo portas para uma VM no Azure usando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e uma NIC
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome do endereço IP público é definido como **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como **myNicName**. Esta etapa também associa o grupo de segurança de rede criado anteriormente com esta NIC.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Definir o nome e o tamanho da VM

Este exemplo define o nome da VM como "myVM" e o tamanho da VM como "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicionar a NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configurar o disco do sistema operacional

1. Defina o URI para o VHD que você carregou ou copiou. Neste exemplo, o arquivo VHD chamado **myOsDisk. vhd** é mantido em uma conta de armazenamento denominada **myStorageAccount** em um contêiner chamado **MyContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco do sistema operacional. Neste exemplo, quando o disco do sistema operacional é criado, o termo "osDisk" é anexado ao nome da VM para criar o nome do disco do sistema operacional. Este exemplo também especifica que esse VHD baseado em Windows deve ser anexado à VM como o disco do sistema operacional.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: se você tiver discos de dados que precisam ser anexados à VM, adicione os discos de dados usando as URLs de VHDs de dados e o número de unidade lógica (LUN) apropriado.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Ao usar uma conta de armazenamento, as URLs de disco de dados e do sistema operacional são parecidas com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-lo no portal navegando até o contêiner de armazenamento de destino, clicando no sistema operacional ou VHD de dados que foi copiado e, em seguida, copiando o conteúdo da URL.


### <a name="complete-the-vm"></a>Concluir a VM 

Crie a VM usando as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se esse comando tiver sido bem-sucedido, você verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifique se a VM foi criada
Você deve ver a VM recém-criada na [portal do Azure](https://portal.azure.com), em **todos os serviços** > **máquinas virtuais**ou usando os seguintes comandos do PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Entre em sua nova máquina virtual. Para obter mais informações, consulte [como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md).

