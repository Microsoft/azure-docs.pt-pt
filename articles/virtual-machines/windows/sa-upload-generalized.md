---
title: Faça upload de um VHD generalize para criar vários VMs em Azure
description: Faça upload de um VHD generalizado para uma conta de armazenamento Azure para criar um VM Windows para usar com o modelo de implementação do Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 7a5aa05a9045548e15aba667fdcdbd14fc8990e6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460312"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Faça upload de um VHD generalizado para o Azure para criar um novo VM

Este tópico cobre o upload de um disco não gerido generalizado para uma conta de armazenamento e, em seguida, criar um novo VM usando o disco carregado. Uma imagem VHD generalizada teve todas as informações da sua conta pessoal removidas usando sysprep. 

Se quiser criar um VM a partir de um VHD especializado numa conta de armazenamento, consulte [Create a VM a partir de um VHD especializado](sa-create-vm-specialized.md).

Este tópico cobre a utilização de contas de armazenamento, mas recomendamos que os clientes se desloquem a utilizar discos geridos. Para uma completa passagem de como preparar, carregar e criar um novo VM utilizando discos geridos, consulte [Criar um novo VM a partir de um VHD generalizado enviado para Azure utilizando discos geridos](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Preparar a VM

Um VHD generalizado teve todas as suas informações pessoais removidas usando o Sysprep. Se pretende utilizar o VHD como imagem para criar novos VMs, deve:
  
  * [Prepare um VHD do Windows para fazer o upload para o Azure](prepare-for-upload-vhd-image.md). 
  * Generalize a máquina virtual usando sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalize uma máquina virtual do Windows usando sysprep
Esta secção mostra-lhe como generalizar a sua máquina virtual Windows para ser utilizada como imagem. O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para mais detalhes sobre o Sysprep, veja [Como utilizar o Sysprep: uma Introdução](https://technet.microsoft.com/library/bb457073.aspx).

Certifique-se de que as funções do servidor que estão a funcionar na máquina são suportadas pela Sysprep. Para mais informações, consulte [Suporte Sysprep para Funções de Servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a executar o Sysprep antes de enviar o seu VHD para o Azure pela primeira vez, certifique-se de ter [preparado o seu VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

1. Inicie sessão na máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Mude o diretório para **%windir%\system32\sysprep,** e depois executar `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.
4. Em **Opções de Encerramento,** selecione **Shutdown**.
5. Clique em **OK**.
   
    ![Iniciar sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep estiver concluído, encerra a máquina virtual. 

> [!IMPORTANT]
> Não reinicie o VM até terminar o upload do VHD para o Azure ou criar uma imagem a partir do VM. Se o VM for acidentalmente reiniciado, faça sysprep para generalizá-lo novamente.
> 
> 


## <a name="upload-the-vhd"></a>Carregar o VHD

Faça o upload do VHD para uma conta de armazenamento Azure.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Se ainda não tiver a versão PowerShell 1.4 ou superior instalada, leia Como instalar e configurar o [PowerShell Azure](/powershell/azure/overview).

1. Abra o Azure PowerShell e inscreva-se na sua conta Azure. Abre-se uma janela pop-up para introduzir as credenciais da sua conta Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Obtenha as iDs de subscrição para as suas subscrições disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Detete a subscrição correta utilizando o ID de subscrição. Substitua-a `<subscriptionID>` com a identificação da subscrição correta.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

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
 
### <a name="start-the-upload"></a>Inicie o upload 

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


## <a name="create-a-new-vm"></a>Crie uma nova VM 

Agora pode utilizar o VHD carregado para criar um novo VM. 

### <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

O URI para o VHD a utilizar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Neste exemplo, o VHD chamado **myVHD** está na conta de **armazenamento minha conta** de armazenamento no contentor **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a vNet e a subnet da [rede virtual.](../../virtual-network/virtual-networks-overview.md)

1. Crie a sub-rede. A amostra seguinte cria uma subnet denominada **mySubnet** no grupo de recursos **myResourceGroup** com o prefixo de endereço de **10.0.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. A amostra seguinte cria uma rede virtual chamada **myVnet** na localização **dos EUA Ocidentais** com o prefixo de endereço de **10.0.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e interface de rede
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie o NIC. Este exemplo cria um NIC chamado **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança da rede e uma regra de RDP
Para poder iniciar sessão no seu VM utilizando RDP, é necessário ter uma regra de segurança que permita o acesso rdp na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite tráfego RDP sobre a porta 3389. Para mais informações sobre nsgs, consulte [Abrir portas para um VM em Azure utilizando powerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual
Criar uma variável para a rede virtual completa. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Crie a VM
O seguinte script PowerShell mostra como configurar as configurações da máquina virtual e usar a imagem VM carregada como fonte para a nova instalação.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Verifique se o VM foi criado
Quando estiver concluído, deverá ver o VM recém-criado no [portal Azure](https://portal.azure.com) sob as máquinas **Browse** > **Virtual,** ou utilizando os seguintes comandos PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerir a sua nova máquina virtual com o Azure PowerShell, consulte [Gerir máquinas virtuais utilizando](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)o Azure Resource Manager e o PowerShell .


