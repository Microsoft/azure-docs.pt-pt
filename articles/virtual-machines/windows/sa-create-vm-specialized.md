---
title: Criar VM a partir de um disco especializado no Azure | Documentos da Microsoft
description: Crie uma nova VM ao anexar um disco não gerido especializado, no modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 90d1d752481ada33c038ab563f6bd0d3bf5fad08
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086807"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Criar uma VM a partir de um VHD especializado numa conta de armazenamento

Crie uma nova VM ao anexar um disco não gerido especializado como o disco do SO com o Powershell. Um disco especializado é uma cópia do VHD a partir de uma VM existente que mantém as contas de utilizador, aplicativos e outros dados de estado da original VM. 

Tem duas opções:
* [Carregar um VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copie o VHD de uma VM do Azure existente](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: Carregar um VHD especializado

Pode carregar o VHD a partir de uma VM especializada criado com uma ferramenta de virtualização no local, como o Hyper-V ou numa VM exportada de outra cloud.

### <a name="prepare-the-vm"></a>Preparar a VM
Pode carregar um VHD especializado que foi criado com uma VM no local ou de um VHD que exportou a partir do noutra cloud. Um VHD especializado mantém as contas de utilizador, aplicativos e outros dados de estado da original VM. Se pretende usar o VHD como-consiste em criar uma nova VM, certifique-se de que os seguintes passos são concluídos. 
  
  * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Isso não é possível** generalizar a VM com o Sysprep.
  * Remova todos os agentes instalados na VM (ou seja, as ferramentas do VMware) e ferramentas de Virtualização do convidado.
  * Certifique-se de que a VM está configurada para solicitar o respetivo endereço IP e as definições de DNS através de DHCP. Isto garante que o servidor obtém o endereço IP na VNet em modo de arranque. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Precisa de uma conta de armazenamento no Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Para mostrar as contas de armazenamento disponíveis, escreva:

```powershell
Get-AzStorageAccount
```

Se pretender utilizar uma conta de armazenamento existente, avance para o carregamento a secção de imagem VM.

Se precisar de criar uma conta de armazenamento, siga estes passos:

1. É necessário o nome do grupo de recursos onde a conta de armazenamento deve ser criada. Para obter todos os grupos de recursos que estão na sua subscrição, escreva:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Para criar um grupo de recursos chamado **myResourceGroup** no **E.U.A. oeste** região, escreva:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Criar uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos, utilizando o [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD para a conta de armazenamento
Utilize o [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) cmdlet para carregar a imagem para um contentor na sua conta de armazenamento. Este exemplo carrega o ficheiro **myVHD.vhd** partir `"C:\Users\Public\Documents\Virtual hard disks\"` para uma conta de armazenamento com o nome **mystorageaccount** no **myResourceGroup** grupo de recursos. O ficheiro será colocado no contentor com o nome **mycontainer** e o novo nome de ficheiro serão **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se tiver êxito, receberá uma resposta que é semelhante a este:

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

Dependendo da sua ligação de rede e o tamanho do ficheiro VHD, este comando pode demorar algum tempo a concluir.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opção 2: Copie o VHD da VM do Azure existente

Pode copiar um VHD para outra conta de armazenamento a utilizar quando criar uma VM nova, duplicada.

### <a name="before-you-begin"></a>Antes de começar
Certifique-se de que:

* Tem informações sobre o **contas de armazenamento de origem e destino**. Para a VM de origem, tem de ter os nomes de conta e contentor de armazenamento. Normalmente, será o nome do contentor **vhds**. Também tem de ter uma conta de armazenamento de destino. Se ainda não tiver uma, pode criar um com o portal de qualquer (**todos os serviços** > contas de armazenamento > Add) ou utilizando o [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet. 
* Baixar e instalar o [AzCopy ferramenta](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Desaloque a VM
Desaloque a VM, o que liberta o VHD seja copiado. 

* **Portal**: Clique em **máquinas virtuais** > **myVM** > parar
* **PowerShell**: Uso [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) parar (desaloque) VM com o nome **myVM** no grupo de recursos **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

O **Status** é alterado para a VM do Azure de portal **parado** para **parada (desalocada)**.

### <a name="get-the-storage-account-urls"></a>Obter os URLs de conta de armazenamento
Terá dos URLs das contas de armazenamento de origem e de destino. A aparência do URLs: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se já sabe o nome de conta e contentor de armazenamento, apenas pode substituir as informações entre colchetes para criar o seu URL. 

Pode utilizar o portal do Azure ou o Azure Powershell para obter o URL:

* **Portal**: Clique nas **>** para **todos os serviços** > **contas de armazenamento** > *conta de armazenamento*  >  **Blobs** e o ficheiro de VHD de origem é provavelmente no **vhds** contentor. Clique em **propriedades** para o contentor e copie o texto rotulado **URL**. Terá dos URLs de contentores de origem e de destino. 
* **PowerShell**: Uso [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para obter as informações de VM com o nome **myVM** no grupo de recursos **myResourceGroup**. Nos resultados, procure na **perfil de armazenamento** secção para o **Uri de Vhd**. A primeira parte do Uri é o URL para o contentor e a última parte é o nome do VHD do SO para a VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obter as chaves de acesso de armazenamento
Localize as chaves de acesso para a origem e destino de contas de armazenamento. Para obter mais informações sobre chaves de acesso, consulte [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md).

* **Portal**: Clique em **todos os serviços** > **contas de armazenamento** > *conta de armazenamento* > **dechavesdeacesso**. Copie a chave identificada como **chave1**.
* **PowerShell**: Uso [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) para obter a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo de recursos **myResourceGroup**. Copie a chave rotulada **chave1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copie o VHD
Pode copiar ficheiros entre as contas de armazenamento com AzCopy. Para o contentor de destino, se o contentor especificado não existir, ela será criada para. 

Para utilizar o AzCopy, abra uma linha de comandos no seu computador local e navegue para a pasta onde o AzCopy é instalado. É semelhante à *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os ficheiros dentro de um contêiner, utilize o **/S** mudar. Isso pode ser usado para copiar o VHD do SO e todos os discos de dados que estejam no mesmo contentor. Este exemplo mostra como copiar todos os ficheiros no contentor **mysourcecontainer** na conta de armazenamento **mysourcestorageaccount** para o contentor **mydestinationcontainer**no **mydestinationstorageaccount** conta de armazenamento. Substitua os nomes das contas de armazenamento e contentores com os seus próprios. Substitua `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` com suas próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se pretender apenas copiar um VHD específico num contentor com vários ficheiros, também pode especificar o nome do arquivo usando a opção de /Pattern. Neste exemplo, apenas o ficheiro denominado **myFileName.vhd** serão copiados.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Quando estiver concluído, obterá uma mensagem semelhante:

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
* Quando utilizar o AZCopy, se vir o erro "Falha ao autenticar o pedido de servidor", certifique-se de que o valor do cabeçalho de autorização está formado corretamente, incluindo a assinatura. Se estiver a utilizar a chave 2 ou a chave de armazenamento secundário, tente utilizar a chave de armazenamento primária ou 1.

## <a name="create-the-new-vm"></a>Criar a nova VM 

Tem de criar a rede e outros recursos da VM a ser utilizado pela VM nova.

### <a name="create-the-subnet-and-vnet"></a>Criar a vNet e sub-rede

Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede denominada **mySubNet**, no grupo de recursos **myResourceGroup**e define o prefixo de endereço de sub-rede para **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a vNet. Este exemplo define o nome de rede virtual para ser **myVnetName**, a localização para **E.U.A. oeste**e o prefixo de endereço para a rede virtual para **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
   Para conseguir iniciar sessão na sua VM através de RDP, tem de ter uma regra de segurança que permite o acesso RDP na porta 3389. Uma vez criado o VHD para a nova VM a partir de um existente VM especializada, quando a VM estiver criada, pode utilizar uma conta existente da máquina de virtual de origem que tinha permissão para iniciar sessão através de RDP.
   Esta ação seja concluída antes de criar a interface de rede que será associado.  
   Este exemplo define o nome do NSG **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre pontos de extremidade e as regras do NSG, consulte [abrir portas para uma VM no Azure com o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e a NIC
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome do endereço IP público é definido como **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar a NIC. Neste exemplo, o nome do NIC está definido como **myNicName**. Este passo também associa o grupo de segurança de rede anteriormente criado com este NIC.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Defina o nome da VM e o tamanho

Este exemplo define o nome da VM para "myVM" e o tamanho da VM para "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicionar a NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configurar o disco do SO

1. Defina o URI para o VHD que carregou ou copiados. Neste exemplo, o ficheiro VHD com o nome **myOsDisk.vhd** é mantido numa conta de armazenamento com o nome **myStorageAccount** num contentor com o nome **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco do SO. Neste exemplo, quando o disco do SO é criado, o termo "osDisk" é acrescentado ao nome da VM para criar o nome do disco de SO. Este exemplo também especifica que este VHD baseado em Windows deve ser anexado à VM como o disco do SO.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: Se tiver discos de dados que precisem de ser anexado à VM, adicione os discos de dados com os URLs de dados VHDs e o número de unidade lógica (Lun) adequados.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Ao utilizar uma conta de armazenamento, os dados e os URLs de disco do sistema operativo mais ou menos assim: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pode encontrar isto no portal do navegar para o contentor de armazenamento de destino, clicando no sistema operativo ou o VHD que foi copiado de dados e, em seguida, copiar o conteúdo do URL.


### <a name="complete-the-vm"></a>Concluir a VM 

Crie a VM com as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se este comando foi concluída com êxito, verá a saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Deverá ver a VM criada recentemente ou na [portal do Azure](https://portal.azure.com), em **todos os serviços** > **máquinas virtuais**, ou utilizando o PowerShell seguinte comandos:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passos Seguintes
Inicie sessão na sua nova máquina virtual. Para obter mais informações, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure a executar o Windows](connect-logon.md).

