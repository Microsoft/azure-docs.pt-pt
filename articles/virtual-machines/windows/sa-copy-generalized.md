---
title: Criar uma imagem não gerida de uma VM generalizada no Azure | Documentos da Microsoft
description: Crie uma imagem de unmanged de uma VM generalizada da Windows utilizar para criar várias cópias de uma VM no Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: a7181c91424e1d905329eefd13f926d0b2700081
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864979"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Como criar uma imagem VM não gerida a partir de uma VM do Azure

Este artigo explica como utilizar contas de armazenamento. Recomendamos que utilize discos geridos e imagens geridas em vez de uma conta de armazenamento. Para obter mais informações, consulte [capturar uma imagem gerida de uma VM generalizada no Azure](capture-image-resource.md).

Este artigo mostra-lhe como utilizar o Azure PowerShell para criar uma imagem de uma VM generalizada do Azure através de uma conta de armazenamento. Em seguida, pode utilizar a imagem para criar outra VM. A imagem inclui o disco do SO e os discos de dados que estão anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, por isso terá de configurar esses recursos, ao criar a nova VM. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-vm"></a>Generalizar a VM 
Esta secção mostra-lhe como generalizar a máquina virtual do Windows para utilização como uma imagem. Generalizar uma VM remove todas as suas informações de conta pessoal como, entre outras coisas e prepara a máquina a ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como utilizar Sysprep: Uma introdução](https://technet.microsoft.com/library/bb457073.aspx).

Certifique-se de que as funções de servidor em execução na máquina são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a carregar o VHD para o Azure pela primeira vez, certifique-se de que tem [preparou a sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar Sysprep. 
> 
> 

Também pode generalizar uma VM do Linux utilizar `sudo waagent -deprovision+user` e, em seguida, utilizar o PowerShell para capturar a VM. Para obter informações sobre como utilizar a CLI para capturar uma VM, consulte [como generalizar e capturar uma máquina virtual do Linux com a CLI do Azure](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell
1. Abra o Azure PowerShell e inicie sessão na sua conta do Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    É aberta uma janela de pop-up para introduzir as credenciais da conta do Azure.
2. Obter os IDs de subscrição para as subscrições disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Definir a subscrição correta com o ID de subscrição.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desaloque a VM e definir o estado como generalizada

> [!IMPORTANT] 
> Não é possível adicionar, editar ou remover etiquetas de uma VM, uma vez que ela é marcada como generalizada. Se pretender adicionar uma etiqueta para a VM, certifique-se de que adicionar as etiquetas antes de marcá-lo como generalizada.
> 

1. Desalocar os recursos da VM.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *Status* é alterado para a VM do Azure de portal **parado** para **parada (desalocada)**.
2. Definir o estado da máquina virtual para o **generalizado**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verificar o estado da VM. O **OSState/generalizada** secção para a VM deve ter o **DisplayStatus** definido como **VM generalizada**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem

Crie uma imagem de máquina de virtual não geridos no contentor de armazenamento de destino com o comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O `-Path` parâmetro salva uma cópia do modelo JSON para a VM de origem para o computador local. O `-DestinationContainerName` parâmetro é o nome do contentor que pretende manter suas imagens. Se o contentor não existir, será criada para.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Pode obter o URL da imagem do modelo de ficheiro JSON. Vá para o **recursos** > **storageProfile** > **osDisk** > **imagem**  >  **uri** secção para o caminho completo da sua imagem. O URL da imagem é semelhante a: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Também pode verificar o URI no portal. A imagem é copiada para um contentor com o nome **sistema** na sua conta de armazenamento. 

## <a name="create-a-vm-from-the-image"></a>Criar uma VM a partir da imagem

Agora pode criar uma ou mais VMs a partir da imagem não gerida.

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
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. O exemplo seguinte cria uma rede virtual denominada **myVnet** no **E.U.A. oeste** localização com o prefixo de endereço de **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de rede e endereço IP pública
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público com o nome **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar a NIC. Este exemplo cria um NIC com o nome **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para poder iniciar sessão na sua VM através de RDP, terá de ter uma regra de segurança que permite o acesso RDP na porta 3389. 

Este exemplo cria um NSG com o nome **myNsg** que contém uma regra denominada **myRdpRule** que permite tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [abrir portas para uma VM no Azure com o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Crie a VM
O PowerShell seguinte conclui as configurações de máquina virtual e utiliza a imagem não gerida como origem para a instalação de novo.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada
Quando terminar, deverá ver a VM criada recentemente na [portal do Azure](https://portal.azure.com) sob **procurar** > **máquinas virtuais**, ou utilizando o PowerShell seguinte comandos:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos Seguintes
Para gerir a sua nova máquina virtual com o Azure PowerShell, veja [gerir máquinas virtuais utilizando o Azure Resource Manager e PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


