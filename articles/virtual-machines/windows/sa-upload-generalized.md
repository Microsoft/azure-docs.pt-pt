---
title: Carregar um VHD generalizado para criar várias VMs no Azure
description: Carregue um VHD generalizado em uma conta de armazenamento do Azure para criar uma VM do Windows a ser usada com o modelo de implantação do Gerenciador de recursos.
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
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 933b648f15418c4838d3da1ea8379267765c784b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073331"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Carregar um VHD generalizado para o Azure para criar uma nova VM

Este tópico aborda o carregamento de um disco não gerenciado generalizado para uma conta de armazenamento e, em seguida, a criação de uma nova VM usando o disco carregado. Uma imagem de VHD generalizada teve todas as informações de sua conta pessoal removidas usando o Sysprep. 

Se você quiser criar uma VM de um VHD especializado em uma conta de armazenamento, consulte [criar uma VM de um VHD especializado](sa-create-vm-specialized.md).

Este tópico aborda o uso de contas de armazenamento, mas recomendamos que os clientes se movam para o uso de Managed Disks. Para obter um passo a passo completo de como preparar, carregar e criar uma nova VM usando discos gerenciados, consulte [criar uma nova VM com base em um VHD generalizado carregado no Azure usando Managed disks](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Preparar a VM

Um VHD generalizado teve todas as informações de sua conta pessoal removidas usando o Sysprep. Se você pretende usar o VHD como uma imagem para criar novas VMs a partir do, você deve:
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md). 
  * Generalizar a máquina virtual usando o Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizar uma máquina virtual do Windows usando o Sysprep
Esta seção mostra como generalizar sua máquina virtual do Windows para uso como uma imagem. O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para mais detalhes sobre o Sysprep, veja [Como utilizar o Sysprep: uma Introdução](https://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador têm suporte pelo Sysprep. Para obter mais informações, consulte [suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver executando o Sysprep antes de carregar o VHD no Azure pela primeira vez, certifique-se de ter [preparado sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

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


## <a name="upload-the-vhd"></a>Carregar o VHD

Carregue o VHD em uma conta de armazenamento do Azure.

### <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Se você ainda não tiver o PowerShell versão 1,4 ou superior instalado, leia [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Abra Azure PowerShell e entre em sua conta do Azure. Uma janela pop-up é aberta para que você insira suas credenciais de conta do Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Obtenha as IDs de assinatura para suas assinaturas disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura. Substitua `<subscriptionID>` pela ID da assinatura correta.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

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
 
### <a name="start-the-upload"></a>Iniciar o carregamento 

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


## <a name="create-a-new-vm"></a>Crie uma nova VM 

Agora você pode usar o VHD carregado para criar uma nova VM. 

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
    $location = "WestUS"
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
O script do PowerShell a seguir mostra como definir as configurações de máquina virtual e usar a imagem de VM carregada como a origem para a nova instalação.



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

## <a name="verify-that-the-vm-was-created"></a>Verifique se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **procurar** > **máquinas virtuais**ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passos seguintes
Para gerenciar sua nova máquina virtual com Azure PowerShell, consulte [gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


