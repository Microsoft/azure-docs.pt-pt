---
title: Criar VM a partir de um disco especializado em Azure
description: Criar um novo VM anexando um disco especializado não gerido, no modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: b2466cc1d36206d0a6a382c948969ad6c28a199f
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84232813"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Criar um VM a partir de um VHD especializado numa conta de armazenamento

Crie um novo VM anexando um disco especializado não gerido como o disco OS utilizando o Powershell. Um disco especializado é uma cópia do VHD de um VM existente que mantém as contas de utilizador, aplicações e outros dados estatais do seu VM original. 

Tem duas opções:
* [Carregar um VHD](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copie o VHD de um VM Azure existente](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Opção 1: Carregar um VHD especializado

Você pode carregar o VHD a partir de um VM especializado criado com uma ferramenta de virtualização no local, como Hyper-V, ou um VM exportado de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Você pode carregar um VHD especializado que foi criado usando um VM no local ou um VHD exportado de outra nuvem. Um VHD especializado mantém as contas de utilizador, aplicações e outros dados estatais do seu VM original. Se pretender utilizar o VHD como é para criar um novo VM, certifique-se de que os seguintes passos estão concluídos. 
  
  * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize o VM utilizando o Sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização de hóspedes instalados no VM (por isso, ferramentas VMware).
  * Certifique-se de que o VM está configurado para puxar o seu endereço IP e as definições de DNS via DHCP. Isto garante que o servidor obtém um endereço IP dentro do VNet quando este começar. 


### <a name="get-the-storage-account"></a>Obtenha a conta de armazenamento
Precisa de uma conta de armazenamento em Azure para armazenar a imagem em VM carregada. Pode utilizar uma conta de armazenamento existente ou criar uma nova. 

Para mostrar as contas de armazenamento disponíveis, escreva:

```powershell
Get-AzStorageAccount
```

Se pretender utilizar uma conta de armazenamento existente, dirija-se à secção de imagem VM.

Se precisar de criar uma conta de armazenamento, siga estes passos:

1. Precisa do nome do grupo de recursos onde a conta de armazenamento deve ser criada. Para descobrir todos os grupos de recursos que estão na sua subscrição, escreva:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Para criar um grupo de recursos chamado **myResourceGroup** na região oeste **dos EUA,** escreva:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento denominada **mystorageaccount** neste grupo de recursos utilizando o [cmdlet New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Upload the VHD to your storage account (Carregar o VHD para a conta de armazenamento)
Utilize o [cmdlet Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para enviar a imagem para um recipiente na sua conta de armazenamento. Este exemplo envia o ficheiro **myVHD.vhd** para uma conta de `"C:\Users\Public\Documents\Virtual hard disks\"` armazenamento chamada **mystorageaccount** no grupo de recursos **myResourceGroup.** O ficheiro será colocado no contentor chamado **mycontainer** e o novo nome do ficheiro será **myUploadedVHD.vhd**.

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

Dependendo da sua ligação à rede e do tamanho do seu ficheiro VHD, este comando pode demorar algum tempo a ser concluído.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opção 2: Copiar o VHD de um VM Azure existente

Pode copiar um VHD para outra conta de armazenamento para utilizar ao criar um novo VM duplicado.

### <a name="before-you-begin"></a>Antes de começar
Certifique-se de que:

* Tenha informações sobre as contas de armazenamento de **origem e destino.** Para a fonte VM, você precisa ter a conta de armazenamento e os nomes do recipiente. Normalmente, o nome do recipiente será **vhds**. Também precisa de ter uma conta de armazenamento de destino. Se ainda não tiver um, pode criar um utilizando o portal **(Todos os serviços** > contas de Armazenamento > Add) ou utilizando o cmdlet [New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) 
* Descarregue e instale a [ferramenta AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Deallocate o VM
Deallocate o VM, que liberta o VHD para ser copiado. 

* **Portal**: Clique em **máquinas virtuais**  >  **myVM** > Stop
* **Powershell**: Use [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) para parar (deallocate) o VM nomeado **myVM** no grupo de recursos **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

O **Estatuto** para o VM no portal Azure muda de **Stop** to **Stop (deallocated)**.

### <a name="get-the-storage-account-urls"></a>Obtenha os URLs da conta de armazenamento
Você precisa dos URLs das contas de armazenamento de origem e destino. Os URLs parecem: `https://<storageaccount>.blob.core.windows.net/<containerName>/` . Se já conhece a conta de armazenamento e o nome do recipiente, pode apenas substituir as informações entre os suportes para criar o seu URL. 

Pode utilizar o portal Azure ou Azure Powershell para obter o URL:

* **Portal**: Clique na **>** conta de armazenamento de contas de armazenamento de todos os **All services**  >  **serviços**  >  *storage account*  >  **Blobs** e o seu ficheiro VHD de origem provavelmente está no recipiente **vhds.** Clique em **Propriedades** para o recipiente e copie o URL com rótulo **de**texto . Você precisará dos URLs dos contentores de origem e destino. 
* **Powershell**: Utilize [o Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para obter as informações relativas à VM nomeada **myVM** no grupo de recursos **myResourceGroup**. Nos resultados, procure na secção **de perfil de armazenamento** para o **Vhd Uri.** A primeira parte do Uri é o URL do recipiente e a última parte é o nome OS VHD para o VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Obtenha as chaves de acesso ao armazenamento
Encontre as chaves de acesso para as contas de armazenamento de origem e destino. Para obter mais informações sobre as chaves de acesso, consulte [as contas de armazenamento do Azure.](../../storage/common/storage-create-storage-account.md)

* **Portal**: Clique **em todos os serviços**Armazena as contas de armazenamento de  >  **Storage accounts**  >  *contas*  >  **As teclas de acesso**. Copie a chave rotulada como **key1**.
* **Powershell**: Utilize [o Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) para obter a chave de armazenamento para a conta de armazenamento **mystorageaccount** no grupo de recursos **myResourceGroup**. Copie a chave com a **chave 1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copiar o VHD
Pode copiar ficheiros entre contas de armazenamento utilizando o AzCopy. Para o contentor de destino, se o recipiente especificado não existir, será criado para si. 

Para utilizar o AzCopy, abra um pedido de comando na sua máquina local e navegue para a pasta onde o AzCopy está instalado. Será semelhante a *C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos os ficheiros dentro de um recipiente, utilize o interruptor **/S.** Isto pode ser usado para copiar o OS VHD e todos os discos de dados se estiverem no mesmo recipiente. Este exemplo mostra como copiar todos os ficheiros do **mysourcecontainer** do contentor na conta de armazenamento **mysourcestorageaccount** para o contentor **mydestinationcontainer** na conta de armazenamento **de mydestinationstorageaccount.** Substitua os nomes das contas de armazenamento e dos recipientes pelos seus. Substitua `<sourceStorageAccountKey1>` e por as suas `<destinationStorageAccountKey1>` próprias chaves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se pretender apenas copiar um VHD específico num recipiente com vários ficheiros, também pode especificar o nome do ficheiro utilizando o interruptor /Padrão. Neste exemplo, apenas o ficheiro nomeado **myFileName.vhd** será copiado.

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
* Quando utilizar o AZCopy, se vir o erro "O Servidor não autenticou o pedido", certifique-se de que o valor do cabeçalho de Autorização é formado corretamente, incluindo a assinatura. Se estiver a utilizar a chave 2 ou a chave de armazenamento secundária, tente utilizar a chave de armazenamento primária ou 1ª.

## <a name="create-the-new-vm"></a>Criar o novo VM 

É necessário criar networking e outros recursos VM para ser utilizado pelo novo VM.

### <a name="create-the-subnet-and-vnet"></a>Criar o subNet e vNet

Crie o vNet e o subNet da [rede virtual.](../../virtual-network/virtual-networks-overview.md)

1. Crie o subNet. Este exemplo cria uma sub-rede chamada **mySubNet**, no grupo de recursos **myResourceGroup**, e define o prefixo do endereço da sub-rede para **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie o vNet. Este exemplo define o nome de rede virtual como **myVnetName**, a localização para **Os Eua Ocidentais**, e o prefixo de endereço para a rede virtual para **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança da rede e uma regra RDP
   Para poder iniciar sessão no seu VM utilizando RDP, é necessário ter uma regra de segurança que permita o acesso do RDP na porta 3389. Uma vez que o VHD para o novo VM foi criado a partir de um VM especializado existente, após a criação do VM pode utilizar uma conta existente a partir da máquina virtual de origem que tinha permissão para iniciar sessão usando RDP.
   Isto precisa de ser concluído antes de criar a interface de rede a que estará associada.  
   Este exemplo define o nome NSG ao **myNsg** e o nome da regra RDP ao **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre os pontos finais e as regras NSG, consulte [portas de abertura para um VM em Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/public-ip-addresses.md) e uma interface de rede.

1. Crie o IP público. Neste exemplo, o nome do endereço IP público está definido para **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar o NIC. Neste exemplo, o nome NIC é definido para **o meu Nome DeNico.** Este passo também associa o Grupo de Segurança de Rede criado anteriormente com este NIC.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Definir o nome e o tamanho VM

Este exemplo define o nome VM como "myVM" e o tamanho VM para "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Adicione o NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configure o disco OS

1. Desloque o URI para o VHD que fez o upload ou copiou. Neste exemplo, o ficheiro VHD chamado **myOsDisk.vhd** é mantido numa conta de armazenamento chamada **myStorageAccount** em um recipiente chamado **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco DE. Neste exemplo, quando o disco de SO é criado, o termo "osDisk" é anexado ao nome VM para criar o nome do disco OS. Este exemplo também especifica que este VHD baseado no Windows deve ser ligado ao VM como o disco OS.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: Se tiver discos de dados que precisam de ser ligados ao VM, adicione os discos de dados utilizando os URLs dos VHDs de dados e o Número de Unidade Lógica apropriado (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Ao utilizar uma conta de armazenamento, os URLs de disco de dados e sistema operativo são mais ou menos assim: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` . Pode encontrá-lo no portal navegando para o recipiente de armazenamento alvo, clicando no sistema operativo ou nos dados VHD que foram copiados e, em seguida, copiando o conteúdo do URL.


### <a name="complete-the-vm"></a>Complete o VM 

Crie o VM utilizando as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se este comando foi bem sucedido, verás uma saída destas:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifique se o VM foi criado
Deverá ver o VM recém-criado no [portal Azure,](https://portal.azure.com)em todas as máquinas virtuais **de**  >  **serviços,** ou utilizando os seguintes comandos PowerShell:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Inscreva-se na sua nova máquina virtual. Para obter mais informações, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](connect-logon.md).

