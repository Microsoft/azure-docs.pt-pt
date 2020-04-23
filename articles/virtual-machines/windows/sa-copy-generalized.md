---
title: Criar uma imagem não gerida de um VM generalizado em Azure
description: Crie uma imagem desmalizada de um VM do Windows generalizado para usar para criar várias cópias de um VM em Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 130764ad5504ded398a9fdf9fa27d6cb936fbacc
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099789"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Como criar uma imagem VM não gerida a partir de um VM Azure

Este artigo cobre a utilização de contas de armazenamento. Recomendamos que utilize discos geridos e imagens geridas em vez de uma conta de armazenamento. Para mais informações, consulte [Capture uma imagem gerida de um VM generalizado em Azure](capture-image-resource.md).

Este artigo mostra-lhe como usar o Azure PowerShell para criar uma imagem de um Azure VM generalizado usando uma conta de armazenamento. Em seguida, pode utilizar a imagem para criar outro VM. A imagem inclui o disco OS e os discos de dados que estão ligados à máquina virtual. A imagem não inclui os recursos de rede virtuais, por isso precisa de configurar esses recursos quando criar o novo VM. 

 

## <a name="generalize-the-vm"></a>Generalizar o VM 
Esta secção mostra-lhe como generalizar a sua máquina virtual Windows para ser utilizada como imagem. Generalizar um VM remove todas as informações pessoais da sua conta, entre outras coisas, e prepara a máquina para ser usada como imagem. Para mais detalhes sobre o Sysprep, veja [Como utilizar o Sysprep: uma Introdução](https://technet.microsoft.com/library/bb457073.aspx).

Certifique-se de que as funções do servidor que estão a funcionar na máquina são suportadas pela Sysprep. Para mais informações, consulte [Suporte Sysprep para Funções de Servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a enviar o seu VHD para o Azure pela primeira vez, certifique-se de ter [preparado o seu VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

Também pode generalizar um VM `sudo waagent -deprovision+user` Linux usando e, em seguida, usar powerShell para capturar o VM. Para obter informações sobre a utilização do CLI para capturar um VM, consulte [como generalizar e capturar uma máquina virtual Linux utilizando o Azure CLI](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell
1. Abra o Azure PowerShell e inscreva-se na sua conta Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Abre-se uma janela pop-up para introduzir as credenciais da sua conta Azure.
2. Obtenha as iDs de subscrição para as suas subscrições disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Detete a subscrição correta utilizando o ID de subscrição.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Deslocar o VM e definir o estado para generalizar

> [!IMPORTANT] 
> Não é possível adicionar, editar ou remover etiquetas de um VM uma vez que esteja marcado como generalizado. Se quiser adicionar uma etiqueta ao VM, certifique-se de adicionar as etiquetas antes de a marcar como generalizada.
> 

1. Deslocar os recursos vm.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *Estado* do VM no portal Azure muda de **Stop** to **Stop (deallocated)**.
2. Desloque o estado da máquina virtual para **generalizar**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o estado do VM. A secção **OSState/generalizada** para o VM deve ter o **DisplayStatus** definido para **VM generalizado**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem

Crie uma imagem de máquina virtual não gerida no recipiente de armazenamento de destino utilizando este comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O `-Path` parâmetro guarda uma cópia do modelo JSON para a fonte VM para o seu computador local. O `-DestinationContainerName` parâmetro é o nome do recipiente que pretende segurar as suas imagens. Se o recipiente não existir, é criado para si.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Pode obter o URL da sua imagem a partir do modelo de ficheiro JSON. Vá ao armazenamento de**image** >  **recursosPerfil** > **storageProfile** > **imagem** > **uri** secção para o caminho completo da sua imagem. O URL da imagem `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`parece: .
   
Também pode verificar o URI no portal. A imagem é copiada para um contentor chamado **sistema** na sua conta de armazenamento. 

## <a name="create-a-vm-from-the-image"></a>Crie uma VM a partir da imagem

Agora pode criar um ou mais VMs a partir da imagem não gerida.

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
    $location = "West US"
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
O PowerShell seguinte completa as configurações da máquina virtual e utiliza a imagem não gerida como fonte para a nova instalação.

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

### <a name="verify-that-the-vm-was-created"></a>Verifique se o VM foi criado
Quando estiver concluído, deverá ver o VM recém-criado no [portal Azure](https://portal.azure.com) sob as máquinas **Browse** > **Virtual,** ou utilizando os seguintes comandos PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerir a sua nova máquina virtual com o Azure PowerShell, consulte [Gerir máquinas virtuais utilizando](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)o Azure Resource Manager e o PowerShell .


