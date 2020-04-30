---
title: Criar VM a partir de um disco especializado em Azure
description: Crie um novo VM anexando um disco especializado não gerido, no modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 60b0a0f0d83b9b83c9cf8d530881508af591de59
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82099653"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Crie um VM a partir de um VHD especializado numa conta de armazenamento

Crie um novo VM anexando um disco especializado não gerido como o disco OS utilizando powershell. Um disco especializado é uma cópia de VHD de um VM existente que mantém as contas de utilizador, aplicações e outros dados do Estado do seu VM original. 

Tem duas opções:
* [Carregar um VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copiar o VHD de um VM Azure existente](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: Carregar um VHD especializado

Você pode carregar o VHD a partir de um VM especializado criado com uma ferramenta de virtualização no local, como Hyper-V, ou um VM exportado de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Você pode carregar um VHD especializado que foi criado usando um VM no local ou um VHD exportado de outra nuvem. Um VHD especializado mantém as contas, aplicações e outros dados do Estado do seu VM original. Se pretender utilizar o VHD como está para criar um novo VM, certifique-se de que os seguintes passos estão concluídos. 
  
  * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize o VM utilizando sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização de hóspedes instalados no VM (isto é, ferramentas VMware).
  * Certifique-se de que o VM está configurado para puxar o seu endereço IP e as definições de DNS através do DHCP. Isto garante que o servidor obtém um endereço IP dentro do VNet quando é iniciado. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Precisa de uma conta de armazenamento em Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou criar uma nova. 

Para mostrar as contas de armazenamento disponíveis, escreva:

```powershell
Get-AzStorageAccount
```

Se pretender utilizar uma conta de armazenamento existente, dirija-se à secção de imagem VM.

Se precisar de criar uma conta de armazenamento, siga estes passos:

1. Precisa do nome do grupo de recursos onde deve ser criada a conta de armazenamento. Para descobrir todos os grupos de recursos que estão na sua subscrição, escreva:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Para criar um grupo de recursos chamado **myResourceGroup** na região **dos EUA Ocidentais,** escreva:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento chamada **mystorage account** neste grupo de recursos utilizando o cmdlet [New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Upload the VHD to your storage account (Carregar o VHD para a conta de armazenamento)
Utilize o cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para fazer o upload da imagem para um recipiente na sua conta de armazenamento. Este exemplo envia o ficheiro **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` uma conta de armazenamento chamada **mystorage account** no grupo de recursos **myResourceGroup.** O ficheiro será colocado no recipiente denominado **mycontainer** e o novo nome de ficheiro será **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se for bem sucedido, obtém-se uma resposta semelhante a esta:

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

Dependendo da ligação de rede e do tamanho do seu ficheiro VHD, este comando pode demorar algum tempo a ser concluído.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opção 2: Copiar o VHD de um VM Azure existente

Pode copiar um VHD para outra conta de armazenamento para utilizar ao criar um Novo VM duplicado.

### <a name="before-you-begin"></a>Antes de começar
Certifique-se de que:

* Tenha informações sobre as contas de armazenamento de **origem e destino.** Para a fonte VM, você precisa ter a conta de armazenamento e nomes de contentores. Normalmente, o nome do recipiente será **vhds**. Também precisa de ter uma conta de armazenamento de destino. Se ainda não tiver um, pode criar um usando o portal ( Todas as contas**de armazenamento de serviços** > > Adicionar) ou usando o cmdlet [New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) 
* Descarreguei e instalei a [ferramenta AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Deslocar o VM
Deslocar o VM, que liberta o VHD para ser copiado. 

* **Portal**: Clique em **máquinas** > virtuais**myVM** > Stop
* **Powershell**: Utilize [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) para parar (desolo) o VM nomeado **myVM** no grupo de recursos **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

O **Estado** do VM no portal Azure muda de **Stop** to **Stop (deallocated)**.

### <a name="get-the-storage-account-urls"></a>Obtenha os URLs da conta de armazenamento
Você precisa dos URLs das contas de armazenamento de origem e destino. Os URLs parecem: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se já conhece a conta de armazenamento e o nome do contentor, pode apenas substituir a informação entre os suportes para criar o seu URL. 

Pode utilizar o portal Azure ou o Azure Powershell para obter o URL:

* **Portal**: **>** Clique na*conta* > de armazenamento de contas de armazenamento de contas de armazenamento de **todos os serviços** > **Storage accounts** > **Blobs** e o seu ficheiro VHD fonte está provavelmente no recipiente **vhds.** Clique em **Propriedades** para o recipiente e copie o **URL**rotulado por texto . Você precisará dos URLs dos recipientes de origem e destino. 
* **Powershell**: Use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para obter a informação para VM chamada **myVM** no grupo de recursos **myResourceGroup**. Nos resultados, procure na secção de perfil de **Armazenamento** para o **Vhd Uri**. A primeira parte do Uri é o URL do recipiente e a última parte é o nome OS VHD para o VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obtenha as chaves de acesso ao armazenamento
Encontre as chaves de acesso para as contas de armazenamento de origem e destino. Para obter mais informações sobre as chaves de acesso, consulte as contas de [armazenamento do Azure.](../../storage/common/storage-create-storage-account.md)

* **Portal**: Clique em **todos os serviços** > **Storage accounts** > Conta de armazenamento de contas*de armazenamento* > Contas De acesso chaves de**acesso**. Copiar a chave rotulada como **chave1**.
* **Powershell**: Use [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) para obter a chave de armazenamento para a conta de **armazenamento mystorage account** no grupo de recursos **myResourceGroup**. Copiar a **tecla**1 .

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copiar o VHD
Pode copiar ficheiros entre contas de armazenamento utilizando o AzCopy. Para o recipiente de destino, se o recipiente especificado não existir, será criado para si. 

Para utilizar o AzCopy, abra um pedido de comando na sua máquina local e navegue para a pasta onde o AzCopy está instalado. Será semelhante aos *Ficheiros c:\Program (x86)\Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os ficheiros dentro de um recipiente, utilize o interruptor **/S.** Isto pode ser usado para copiar o VHD oss os e todos os discos de dados se estiverem no mesmo recipiente. Este exemplo mostra como copiar todos os ficheiros do **contentor mysourcecontainer** na conta **de armazenamento mysourcestorage account** para o contentor **mydestinationcontainer** do contentor na conta de armazenamento de **conta de armazenamento mydestination.** Substitua os nomes das contas de armazenamento e dos contentores pelos seus. `<sourceStorageAccountKey1>` Substitua `<destinationStorageAccountKey1>` e com as suas próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se pretender apenas copiar um VHD específico num recipiente com vários ficheiros, também pode especificar o nome do ficheiro utilizando o interruptor /Padrão. Neste exemplo, apenas o ficheiro denominado **myFileName.vhd** será copiado.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Quando estiver terminado, receberá uma mensagem que se parece com:

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
* Quando utilizar o AZCopy, se vir o erro "O Servidor não conseguiu autenticar o pedido", certifique-se de que o valor do cabeçalho de Autorização é formado corretamente, incluindo a assinatura. Se estiver a utilizar a Chave 2 ou a chave de armazenamento secundária, tente utilizar a chave de armazenamento primária ou 1ª.

## <a name="create-the-new-vm"></a>Criar o novo VM 

É necessário criar redes e outros recursos VM para serem utilizados pelo novo VM.

### <a name="create-the-subnet-and-vnet"></a>Criar a subNet e vNet

Crie a vNet e a subNet da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a subnet. Este exemplo cria uma subnet denominada **mySubNet**, no grupo de recursos **myResourceGroup,** e define o prefixo de endereço de sub-rede para **10.0.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie o vNet. Este exemplo define o nome da rede virtual como **myVnetName**, a localização para **Oeste dos EUA,** e o prefixo de endereço para a rede virtual para **10.0.0.0.16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança da rede e uma regra de RDP
   Para poder iniciar sessão no seu VM utilizando RDP, é necessário ter uma regra de segurança que permita o acesso rdp na porta 3389. Como o VHD para o novo VM foi criado a partir de um VM especializado existente, após a criação do VM pode utilizar uma conta existente a partir da máquina virtual de origem que tinha permissão para iniciar sessão na utilização de RDP.
   Isto tem de ser concluído antes de criar a interface de rede com a que estará associada.  
   Este exemplo define o nome NSG para **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre pontos finais e regras de NSG, consulte [Abrir portas para um VM em Azure utilizando powerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome de endereço IP público é definido para **o myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie o NIC. Neste exemplo, o nome NIC é definido para **myNicName**. Este passo também associa o Grupo de Segurança da Rede criado anteriormente com este NIC.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Detete o nome e o tamanho do VM

Este exemplo define o nome VM para "myVM" e o tamanho VM para "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicione o NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configure o disco OS

1. Defina o URI para o VHD que carregou ou copiou. Neste exemplo, o ficheiro VHD denominado **myOsDisk.vhd** é mantido numa conta de armazenamento chamada **myStorageAccount** num recipiente chamado **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco OS. Neste exemplo, quando o disco OS é criado, o termo "osDisk" é anexado ao nome VM para criar o nome do disco OS. Este exemplo também especifica que este VHD baseado no Windows deve ser ligado ao VM como o disco OS.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: Se tiver discos de dados que precisam de ser anexados ao VM, adicione os discos de dados utilizando os URLs dos VHDs de dados e o número de unidade lógica apropriado (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Ao utilizar uma conta de armazenamento, os URLs de `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`disco de dados e sistemaoperativo são parecidos com isto: . Pode encontrá-lo no portal navegando para o recipiente de armazenamento alvo, clicando no sistema operativo ou no VHD de dados que foi copiado e, em seguida, copiando o conteúdo do URL.


### <a name="complete-the-vm"></a>Complete o VM 

Crie o VM utilizando as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se este comando tiver sido bem sucedido, verá saques como este:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifique se o VM foi criado
Deve ver o VM recém-criado, quer no [portal Azure,](https://portal.azure.com)sob **todos os serviços** > **Máquinas virtuais,** quer utilizando os seguintes comandos PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Inscreva-se na sua nova máquina virtual. Para mais informações, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](connect-logon.md).

