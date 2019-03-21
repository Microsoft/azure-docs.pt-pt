---
title: Gerir discos de VM no Azure Stack | Documentos da Microsoft
description: Crie discos para máquinas virtuais no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 4edaf782b193e99dfe4002eedb6f3a046fb7dcd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081462"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>Criar armazenamento de disco da máquina virtual no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve como criar o armazenamento de disco da máquina virtual com o portal do Azure Stack ou com o PowerShell.

## <a name="overview"></a>Descrição geral

A partir da versão 1808, o Azure Stack suporta a utilização de discos geridos e os discos não geridos em máquinas virtuais, como um sistema operativo (SO) e um disco de dados. Antes da versão 1808, são suportados apenas os discos não geridos. 

**[Discos geridos](../../virtual-machines/windows/managed-disks-overview.md)**  simplificar a gestão de discos para VMs IaaS do Azure ao gerir as contas de armazenamento associadas aos discos VM. Só tem de especificar o tamanho do disco é necessário, e a pilha do Azure cria e gere o disco por si.

Discos não geridos, exigem que crie uma conta de armazenamento para armazenar os discos. Os discos que cria são conhecidos como discos de VM e são armazenados em contentores na conta de armazenamento.

### <a name="best-practice-guidelines"></a>Diretrizes de práticas recomendadas

Para melhorar o desempenho e reduzir os custos gerais, recomendamos que coloque cada disco VM num contentor separado. Um contentor deve conter um disco de SO ou um disco de dados, mas não ambos simultaneamente. (No entanto, há nada para o impedir de colocar os dois tipos de disco no mesmo contentor.)

Se adicionar um ou mais discos de dados a uma VM, utilize contentores adicionais como uma localização para armazenar estes discos. O disco do SO para VMs adicionais também deve estar em seus próprios contentores.

Ao criar VMs, pode reutilizar a mesma conta de armazenamento para cada nova máquina virtual. Apenas os contentores que cria devem ser exclusivos.

### <a name="adding-new-disks"></a>Adição de novos discos

A tabela seguinte resume como adicionar discos ao utilizar o portal e com o PowerShell.

| Método | Opções
|-|-|
|Portal de utilizador|-Adicione novos discos de dados a uma VM existente. Novos discos são criados através do Azure Stack. </br> </br>-Adicione um ficheiro de disco (. vhd) existente para uma VM criada anteriormente. Para tal, tem de preparar o VHD e, em seguida, carregue o ficheiro para o Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Criar uma nova VM com um disco de SO e, ao mesmo tempo, adicione um ou mais discos de dados para essa VM. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Utilizar o portal para adicionar discos a uma VM

Por predefinição, quando utilizar o portal para criar uma VM para a maioria dos itens do marketplace, apenas o disco do SO é criado.

Depois de criar uma VM, pode utilizar o portal para:
* Criar um novo disco de dados e anexá-lo para a VM.
* Carregar um disco de dados existente e anexá-lo para a VM.

Cada disco não gerido, que adiciona deve ser colocado num contentor separado.

>[!NOTE]  
>Os discos criados e geridos pelo Azure são chamados [discos geridos](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Utilizar o portal para criar e anexar um disco de dados novo

1. No portal, selecione **todos os serviços** > **máquinas virtuais**.    
   ![Exemplo: Dashboard VM](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Selecione uma máquina virtual que tenha sido criada anteriormente.   
   ![Exemplo: Selecione uma VM no dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. Para a máquina virtual, selecione **discos** > **adicionar disco de dados**.       
   ![Exemplo: Anexar um disco novo à vm](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4. Para o disco de dados:
   -  Introduza o **LUN**. O LUN tem de ser um número válido.
   -  Selecione **criar disco**.
   ![Exemplo: Anexar um disco novo à vm](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. O criar gerenciados painel de disco:
   - Introduza o **nome** do disco.
   - Selecione um existente ou crie um novo **grupo de recursos**.
   - Selecione o **localização**. Por predefinição, a localização é definida para o mesmo contentor que contém o disco do SO.
   - Selecione o **tipo de conta**. 
      ![Exemplo: Anexar um disco novo à vm](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **Premium SSD**  
      Os discos Premium (SSD) são apoiados por unidades de estado sólido e oferecem um desempenho consistente e de baixa latência. Eles fornecem o melhor equilíbrio entre preço e desempenho e são ideais para aplicações de e/S intensivas e cargas de trabalho de produção.
       
      **HDD padrão**  
      Os discos Standard (HDD) são suportados por unidades magnéticas e são mais indicados para aplicações em que os dados são acedidos com pouca frequência. Zona - discos redundantes são apoiados por com redundância de armazenamento de zona (ZRS) que replica os dados em várias zonas e estão disponíveis mesmo que uma única zona está inativo. 

   - Selecione o **tipo de origem**.

     Crie um disco de um instantâneo de outro disco, um blob numa conta de armazenamento ou crie um disco vazio.

      **instantâneo**  
      Selecione um instantâneo, se estiver disponível. O instantâneo tem de estar no disponíveis na subscrição e localização da VM.

      **Blob de armazenamento**  
     - Adicione o URI do blob de armazenamento que contém a imagem de disco.  
     - Selecione **procurar** para abrir o painel de contas de armazenamento. Para obter instruções, consulte [adicionar um disco de dados a partir de uma conta de armazenamento](#add-a-data-disk-from-a-storage-account).
     - Selecione o tipo de SO da imagem, seja **Windows**, **Linux**, ou **nenhum (disco de dados)**.

       **Nenhum (disco vazio)**

   - Selecione o **tamanho (GiB)**.

     Com base no tamanho do disco do aumento dos custos do disco Standard. Premium disco os custos e desempenho aumento com base no tamanho do disco. Para obter mais informações, consulte [preços dos Managed Disks](https://go.microsoft.com/fwlink/?linkid=843142).

   - Selecione **Criar**. O Azure Stack cria e valida o disco gerido.

5. Depois do Azure Stack cria o disco e anexa-o para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**.   

   ![Exemplo: Disco do Vista](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Adicionar um disco de dados a partir de uma conta de armazenamento

Para obter mais informações sobre como trabalhar com contas de armazenamento no Azure Stack, veja [introdução ao armazenamento do Azure Stack](azure-stack-storage-overview.md).

1. Selecione o **conta de armazenamento** a utilizar.
2. Selecione o **contentor** onde pretende colocar o disco de dados. Partir do **contentores** painel, pode criar um novo contentor se desejar. Em seguida, pode alterar a localização para o novo disco para o seu próprio contentor. Quando utiliza um contentor separado para cada disco, distribuir o posicionamento do disco de dados que pode melhorar o desempenho.
3. Escolher **selecione** para guardar a seleção.

    ![Exemplo: Selecionar um contentor](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Anexar um disco de dados existente a uma VM

1.  [Preparar um ficheiro. vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) para utilização como disco de dados para uma VM. Carregue esse ficheiro. vhd para uma conta de armazenamento que utilizar com a VM que pretende anexar o ficheiro. vhd para.

    Recomendamos que utilize um contentor diferente para armazenar o ficheiro. vhd que o contentor que retém o disco do SO.   
    ![Exemplo: Carregar um ficheiro VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Depois do ficheiro. vhd é carregado, está pronto para anexar o VHD para uma VM. No menu à esquerda, selecione **máquinas virtuais**.  
 ![Exemplo: Selecione uma VM no dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Escolha a máquina virtual a partir da lista.

    ![Exemplo: Selecione uma VM no dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Na página para a máquina virtual, selecione **discos** > **Attach existente**.   

    ![Exemplo: Anexar um disco existente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Na **anexar disco existente** página, selecione **ficheiro VHD**. O **contas de armazenamento** é aberta a página.    

    ![Exemplo: Selecione um ficheiro VHD](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Sob **contas de armazenamento**, selecione a conta a utilizar e, em seguida, selecione um contentor que retém o ficheiro. vhd carregado anteriormente. Selecione o ficheiro. vhd e, em seguida, escolha **selecione** para guardar a seleção.    

    ![Exemplo: Selecionar um contentor](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Sob **anexar disco existente**, o ficheiro selecionado está listado em **ficheiro VHD**. Atualização do **colocação em cache do anfitrião** configuração do disco e, em seguida, selecione **OK** para guardar a nova configuração de disco para a VM.    

    ![Exemplo: Anexar o ficheiro VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Depois do Azure Stack cria o disco e anexa-o para a máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **discos de dados**.   

    ![Exemplo: Anexar de disco completa](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Utilize o PowerShell para adicionar vários discos não geridos para uma VM

Pode utilizar o PowerShell para Aprovisionar uma VM e adicione um novo disco de dados ou anexar pré-existente **. vhd** ficheiro como um disco de dados.

O **Add-AzureRmVMDataDisk** cmdlet adiciona um disco de dados a uma máquina virtual. Pode adicionar um disco de dados quando criar uma máquina virtual, ou pode adicionar um disco de dados para uma máquina virtual existente. Especifique a **VhdUri** parâmetro para distribuir os discos para diferentes recipientes.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Adicionar discos de dados para uma nova máquina virtual
Os exemplos seguintes utilizam comandos do PowerShell para criar uma VM com discos de dados de três, cada colocado num contentor diferente.

O primeiro comando cria um objeto de máquina virtual e, em seguida, armazena-a na *$VirtualMachine* variável. O comando atribui um nome e o tamanho para a máquina virtual.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                    -VMSize "Standard_A2"
```

Os próximos três comandos atribuam caminhos de três discos de dados para o *us $DataDiskVhdUri01*, *us $DataDiskVhdUri02*, e *us $DataDiskVhdUri03* variáveis. Defina um nome de caminho diferente no URL para distribuir os discos para diferentes recipientes.

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Os três comandos finais adicionam discos de dados para a máquina virtual armazenada na *$VirtualMachine*. Cada comando Especifica o nome, localização e propriedades adicionais do disco. O URI de cada disco é armazenado em *us $DataDiskVhdUri01*, *us $DataDiskVhdUri02*, e *us $DataDiskVhdUri03*.

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Utilize os seguintes comandos do PowerShell para adicionar a configuração de rede e disco do SO para a VM e, em seguida, inicie a nova VM.

```powershell
#set variables
$rgName = "myResourceGroup"
$location = "local"
#Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
    -CreateOption FromImage -Windows

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
    -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
-Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Adicionar discos de dados para uma máquina virtual existente

Os exemplos seguintes utilizam comandos do PowerShell para adicionar os três discos de dados a uma VM existente.
O primeiro comando obtém a máquina virtual com o nome VirtualMachine utilizando o **Get-AzureRmVM** cmdlet. O comando armazena a máquina virtual na *$VirtualMachine* variável.

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```
Os próximos três comandos atribuam caminhos de três discos de dados para as variáveis $DataDiskVhdUri01, US $DataDiskVhdUri02 e US $DataDiskVhdUri03.  Os nomes de caminho diferente do vhduri indicam contentores diferentes para a colocação de disco.
```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```
```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```
```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```


Os próximos três comandos adicionam os discos de dados para a máquina virtual armazenada na *$VirtualMachine* variável. Cada comando Especifica o nome, localização e propriedades adicionais do disco. O URI de cada disco é armazenado em *us $DataDiskVhdUri01*, *us $DataDiskVhdUri02*, e *us $DataDiskVhdUri03*.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

O comando final atualiza o estado da máquina virtual armazenado na *$VirtualMachine* além -*ResourceGroupName*.

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as máquinas virtuais do Azure Stack, veja [considerações para máquinas virtuais no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
