---
title: Carregar um VHD para criar várias VMs no Azure de generalize | Documentos da Microsoft
description: Carregar um VHD generalizado para uma conta de armazenamento do Azure para criar uma VM do Windows para utilizar com o modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 658cee95d695a310291d5b7180815c89bc2f0401
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818112"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Carregar um VHD generalizado para o Azure para criar uma nova VM

Este tópico abrange carregar um disco não gerido generalizado para uma conta de armazenamento e, em seguida, criar uma nova VM com o disco carregado. Uma imagem VHD generalizada teve que todas as suas informações de conta pessoal removidas com o Sysprep. 

Se quiser criar uma VM a partir de um VHD especializado numa conta de armazenamento, veja [criar uma VM a partir de um VHD especializado](sa-create-vm-specialized.md).

Este tópico explica como utilizar contas de armazenamento, mas recomendamos que os clientes mudem para utilizar discos geridos em vez disso. Para obter uma descrição completa dos como preparar, carregar e criar uma nova VM com discos geridos, consulte [criar uma nova VM a partir de um VHD generalizado carregada para o Azure com o Managed Disks](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>Preparar a VM

Um VHD generalizado teve que todas as suas informações de conta pessoal removidas com o Sysprep. Se pretende usar o VHD como uma imagem para criar VMs novas a partir de, deve:
  
  * [Preparar um VHD do Windows para carregar para o Azure](prepare-for-upload-vhd-image.md). 
  * Generalizar a máquina virtual com o Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizar uma máquina de virtual do Windows com o Sysprep
Esta secção mostra-lhe como generalizar a máquina virtual do Windows para utilização como uma imagem. O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como utilizar Sysprep: Uma introdução](https://technet.microsoft.com/library/bb457073.aspx).

Certifique-se de que as funções de servidor em execução na máquina são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a executar o Sysprep antes de carregar o VHD para o Azure pela primeira vez, certifique-se de que tem [preparou a sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar Sysprep. 
> 
> 

1. Inicie sessão para a máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Altere o diretório para **%windir%\system32\sysprep**e, em seguida, execute `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.
4. Na **opções de encerramento**, selecione **encerramento**.
5. Clique em **OK**.
   
    ![Inicie Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep estiver concluído, encerra a máquina virtual. 

> [!IMPORTANT]
> Não reinicie a VM até terminar de carregar o VHD para o Azure ou criar uma imagem da VM. Se, acidentalmente, a VM é reiniciada, execute o Sysprep para generalizá-lo novamente.
> 
> 


## <a name="upload-the-vhd"></a>Carregar o VHD

Carrega o VHD para uma conta de armazenamento do Azure.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Se ainda não tiver o PowerShell versão 1.4 ou superior instalado, leia [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Abra o Azure PowerShell e inicie sessão na sua conta do Azure. É aberta uma janela de pop-up para introduzir as credenciais da conta do Azure.
   
    ```powershell
    Connect-AzureRmAccount
    ```
2. Obter os IDs de subscrição para as subscrições disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Definir a subscrição correta com o ID de subscrição. Substitua `<subscriptionID>` com o ID da subscrição correta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Precisa de uma conta de armazenamento no Azure para armazenar a imagem VM carregada. Pode utilizar uma conta de armazenamento existente ou crie um novo. 

Para mostrar as contas de armazenamento disponíveis, escreva:

```powershell
Get-AzureRmStorageAccount
```

Se pretender utilizar uma conta de armazenamento existente, avance para o carregamento a secção de imagem VM.

Se precisar de criar uma conta de armazenamento, siga estes passos:

1. É necessário o nome do grupo de recursos onde a conta de armazenamento deve ser criada. Para obter todos os grupos de recursos que estão na sua subscrição, escreva:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para criar um grupo de recursos chamado **myResourceGroup** no **E.U.A. oeste** região, escreva:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Criar uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos, utilizando o [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Iniciar o carregamento 

Utilize o [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet para carregar a imagem para um contentor na sua conta de armazenamento. Este exemplo carrega o ficheiro **myVHD.vhd** partir `"C:\Users\Public\Documents\Virtual hard disks\"` para uma conta de armazenamento com o nome **mystorageaccount** no **myResourceGroup** grupo de recursos. O ficheiro será colocado no contentor com o nome **mycontainer** e o novo nome de ficheiro serão **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
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


## <a name="create-a-new-vm"></a>Criar uma nova VM 

Agora, pode utilizar o VHD carregado para criar uma nova VM. 

### <a name="set-the-uri-of-the-vhd"></a>Defina o URI do VHD

O URI para o VHD utilizar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**. vhd. Neste exemplo, o VHD com o nome **myVHD** é na conta de armazenamento **mystorageaccount** no contêiner **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Criar a vNet e sub-rede do [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. O exemplo seguinte cria uma sub-rede denominada **mySubnet** no grupo de recursos **myResourceGroup** com o prefixo de endereço de **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. O exemplo seguinte cria uma rede virtual denominada **myVnet** no **E.U.A. oeste** localização com o prefixo de endereço de **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de rede e endereço IP pública
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público com o nome **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar a NIC. Este exemplo cria um NIC com o nome **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para poder iniciar sessão na sua VM através de RDP, terá de ter uma regra de segurança que permite o acesso RDP na porta 3389. 

Este exemplo cria um NSG com o nome **myNsg** que contém uma regra denominada **myRdpRule** que permite tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [abrir portas para uma VM no Azure com o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual
Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Crie a VM
O script do PowerShell seguinte mostra como configurar as configurações de máquina virtual e utilizar a imagem VM carregada como origem para a instalação de novo.



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
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Quando terminar, deverá ver a VM criada recentemente na [portal do Azure](https://portal.azure.com) sob **procurar** > **máquinas virtuais**, ou utilizando o PowerShell seguinte comandos:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos Seguintes
Para gerir a sua nova máquina virtual com o Azure PowerShell, veja [gerir máquinas virtuais utilizando o Azure Resource Manager e PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


