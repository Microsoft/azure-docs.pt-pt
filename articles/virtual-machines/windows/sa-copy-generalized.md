---
title: Criar uma imagem não gerenciada de uma VM generalizada no Azure | Microsoft Docs
description: Crie uma imagem não gerenciada de uma VM do Windows generalizada para usar para criar várias cópias de uma VM no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: b0277e07f67b3f9124dc0e27b20e3d49e0d2f6e9
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749211"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Como criar uma imagem de VM não gerenciada de uma VM do Azure

Este artigo aborda o uso de contas de armazenamento. Recomendamos que você use Managed disks e imagens gerenciadas em vez de uma conta de armazenamento. Para obter mais informações, consulte [capturar uma imagem gerenciada de uma VM generalizada no Azure](capture-image-resource.md).

Este artigo mostra como usar Azure PowerShell para criar uma imagem de uma VM do Azure generalizada usando uma conta de armazenamento. Em seguida, você pode usar a imagem para criar outra VM. A imagem inclui o disco do sistema operacional e os discos de dados que estão anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, portanto, você precisa configurar esses recursos ao criar a nova VM. 

 

## <a name="generalize-the-vm"></a>Generalizar a VM 
Esta seção mostra como generalizar sua máquina virtual do Windows para uso como uma imagem. Generalizar uma VM remove todas as informações pessoais da sua conta, entre outras coisas, e prepara a máquina para ser usada como uma imagem. Para mais detalhes sobre o Sysprep, veja [Como utilizar o Sysprep: uma Introdução](https://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador têm suporte pelo Sysprep. Para obter mais informações, consulte [suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver carregando seu VHD no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

Você também pode generalizar uma VM do Linux usando `sudo waagent -deprovision+user` e, em seguida, usar o PowerShell para capturar a VM. Para obter informações sobre como usar a CLI para capturar uma VM, consulte [como generalizar e capturar uma máquina virtual Linux usando o CLI do Azure](../linux/capture-image.md).


1. Entre na máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Altere o diretório para **%windir%\system32\sysprep**e execute `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.
4. Em **Opções de desligamento**, selecione **desligar**.
5. Clique em **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep estiver concluído, encerra a máquina virtual. 

> [!IMPORTANT]
> Não reinicie a VM até terminar de carregar o VHD no Azure ou criar uma imagem da VM. Se a VM for reiniciada acidentalmente, execute o Sysprep para generaliza-la novamente.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell
1. Abra Azure PowerShell e entre em sua conta do Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Uma janela pop-up é aberta para que você insira suas credenciais de conta do Azure.
2. Obtenha as IDs de assinatura para suas assinaturas disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desaloque a VM e defina o estado como generalizado

> [!IMPORTANT] 
> Você não pode adicionar, editar ou remover marcas de uma VM depois que ela é marcada como generalizada. Se você quiser adicionar uma marca à VM, certifique-se de adicionar as marcas antes de marcá-las como generalizadas.
> 

1. Desaloque os recursos da VM.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *status* da VM no portal do Azure é alterado de **parado** para **parado (desalocado)** .
2. Defina o status da máquina virtual como **generalizado**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o status da VM. A seção **OSState/generalizada** para a VM deve ter o **DisplayStatus** definido como **VM generalizado**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem

Crie uma imagem de máquina virtual não gerenciada no contêiner de armazenamento de destino usando este comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O parâmetro `-Path` salva uma cópia do modelo JSON para a VM de origem em seu computador local. O parâmetro `-DestinationContainerName` é o nome do contêiner para o qual você deseja armazenar suas imagens. Se o contêiner não existir, ele será criado para você.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Você pode obter a URL da imagem do modelo de arquivo JSON. Vá para a seção **recursos** > **storageProfile** > **osDisk** > **imagem** > **URI** para o caminho completo da imagem. A URL da imagem é semelhante a: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Você também pode verificar o URI no Portal. A imagem é copiada para um contêiner chamado **System** em sua conta de armazenamento. 

## <a name="create-a-vm-from-the-image"></a>Crie uma VM a partir da imagem

Agora você pode criar uma ou mais VMs a partir da imagem não gerenciada.

### <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

O URI para o VHD a ser usado está no formato: https://**mystorageaccount**. blob.Core.Windows.NET/**MyContainer**/**MyVhdName**. vhd. Neste exemplo, o VHD chamado **myVHD** está na conta de armazenamento **mystorageaccount** **no contêiner MyContainer.**

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a vNet e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. O exemplo a seguir cria uma sub-rede chamada **mysubnet** no grupo de recursos **MyResource** Group com o prefixo de endereço **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Criar a rede virtual. O exemplo a seguir cria uma rede virtual chamada **myVnet** no local **oeste dos EUA** com o prefixo de endereço **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e uma interface de rede
Para ativar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Este exemplo cria uma NIC chamada **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para poder fazer logon em sua VM usando o RDP, você precisa ter uma regra de segurança que permita o acesso RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite o tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [abrindo portas para uma VM no Azure usando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
O PowerShell a seguir conclui as configurações de máquina virtual e usa a imagem não gerenciada como a origem para a nova instalação.

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

### <a name="verify-that-the-vm-was-created"></a>Verifique se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **procurar** > **máquinas virtuais**ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerenciar sua nova máquina virtual com Azure PowerShell, consulte [gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


