---
title: Criar uma imagem nãogerida de um VM generalizado em Azure
description: Crie uma imagem nãoangizada de um Windows VM generalizado para usar para criar várias cópias de um VM em Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 31b8200b63780388fb53db588c418951c500ac19
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84232911"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Como criar uma imagem de VM não gerida a partir de uma VM do Azure

Este artigo cobre usando contas de armazenamento. Recomendamos que utilize discos geridos e imagens geridas em vez de uma conta de armazenamento. Para obter mais informações, consulte [Capturar uma imagem gerida de um VM generalizado em Azure](capture-image-resource.md).

Este artigo mostra-lhe como usar a Azure PowerShell para criar uma imagem de um Azure VM generalizado usando uma conta de armazenamento. Em seguida, pode utilizar a imagem para criar outro VM. A imagem inclui o disco DE e os discos de dados que estão ligados à máquina virtual. A imagem não inclui os recursos de rede virtual, por isso é necessário configurar esses recursos quando criar o novo VM. 

 

## <a name="generalize-the-vm"></a>Generalizar o VM 
Esta secção mostra-lhe como generalizar a sua máquina virtual Windows para utilização como imagem. Generalizar um VM remove todas as informações da sua conta pessoal, entre outras coisas, e prepara a máquina para ser usada como imagem. Para mais detalhes sobre o Sysprep, veja [Como utilizar o Sysprep: uma Introdução](https://technet.microsoft.com/library/bb457073.aspx).

Certifique-se de que as funções do servidor em funcionamento na máquina são suportadas pela Sysprep. Para mais informações, consulte [o Suporte Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se estiver a enviar o seu VHD para Azure pela primeira vez, certifique-se de que [preparou o seu VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

Também pode generalizar um Linux VM utilizando `sudo waagent -deprovision+user` e, em seguida, usar o PowerShell para capturar o VM. Para obter informações sobre a utilização do CLI para capturar um VM, consulte [Como generalizar e capturar uma máquina virtual Linux utilizando o CLI Azure](../linux/capture-image.md).


1. Inicie sessão na máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Mude o diretório para **%windir%\system32\sysprep**, e depois corra `sysprep.exe` .
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.
4. Nas **opções de encerramento**, selecione **Shutdown**.
5. Clique em **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep estiver concluído, encerra a máquina virtual. 

> [!IMPORTANT]
> Não reinicie o VM até terminar de carregar o VHD para Azure ou criar uma imagem a partir do VM. Se o VM for reiniciado acidentalmente, faça sysprep para generalizá-lo novamente.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell
1. Abra o Azure PowerShell e inscreva-se na sua conta Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Uma janela pop-up abre-se para que você introduza as suas credenciais de conta Azure.
2. Obtenha os IDs de subscrição para as suas subscrições disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Desa esta medida de subscrição correta utilizando o ID de subscrição.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Deallocate o VM e definir o estado para generalizar

> [!IMPORTANT] 
> Não é possível adicionar, editar ou remover tags de um VM uma vez que esteja marcado como generalizado. Se quiser adicionar uma etiqueta ao VM, certifique-se de que adiciona as etiquetas antes de a marcar como generalizada.
> 

1. Negociar os recursos VM.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *Estatuto* para o VM no portal Azure muda de **Stop** to **Stop (deallocated)**.
2. Desaprova o estado da máquina virtual para **generalizado.** 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o estado do VM. A **secção OSState/Generalizada** para o VM deve ter o **DisplayStatus** definido para **VM generalizado**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem

Crie uma imagem de máquina virtual nãogerida no recipiente de armazenamento de destino utilizando este comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O `-Path` parâmetro guarda uma cópia do modelo JSON para a fonte VM para o seu computador local. O `-DestinationContainerName` parâmetro é o nome do recipiente que pretende guardar as suas imagens. Se o contentor não existir, é criado para si.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Pode obter o URL da sua imagem a partir do modelo de ficheiro JSON. Aceda **resources**ao  >  **armazenamento de recursosProfile**  >  **osDisk**  >  **image**  >  **uri** seção para o caminho completo da sua imagem. O URL da imagem parece: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` .
   
Também pode verificar o URI no portal. A imagem é copiada para um **sistema** de contentores nomeados na sua conta de armazenamento. 

## <a name="create-a-vm-from-the-image"></a>Crie uma VM a partir da imagem

Agora pode criar um ou mais VMs a partir da imagem nãogerida.

### <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

O URI para o VHD a utilizar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer** / **MyVhdName**.vhd. Neste exemplo, o VHD denominado **myVHD** está na conta de armazenamento **mystorageaccount** no **mycontainer**do contentor .

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie o vNet e a sub-rede da [rede virtual.](../../virtual-network/virtual-networks-overview.md)

1. Crie a sub-rede. A amostra a seguir cria uma sub-rede chamada **mySubnet** no grupo de recursos **myResourceGroup** com o prefixo de endereço de **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. A amostra a seguir cria uma rede virtual chamada **myVnet** na localização **oeste dos EUA** com o prefixo de endereço de **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e interface de rede
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/public-ip-addresses.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Criar o NIC. Este exemplo cria um NIC chamado **myNic.** 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança da rede e uma regra RDP
Para poder iniciar sessão no seu VM utilizando RDP, é necessário ter uma regra de segurança que permita o acesso do RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite o tráfego rdp sobre a porta 3389. Para obter mais informações sobre os NSGs, consulte [portas de abertura para um VM em Azure utilizando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Criar uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Crie a VM
O Seguinte PowerShell completa as configurações da máquina virtual e utiliza a imagem nãogerida como fonte para a nova instalação.

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
Quando estiver concluído, deverá ver o VM recém-criado no [portal Azure](https://portal.azure.com) sob as máquinas Virtual **Browse**  >  **Virtual machines**, ou utilizando os seguintes comandos PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerir a sua nova máquina virtual com a Azure PowerShell, consulte [Gerir máquinas virtuais utilizando o Azure Resource Manager e o PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


