---
title: Criar uma imagem gerida no Azure | Documentos da Microsoft
description: Crie uma imagem gerida de uma VM ou um VHD generalizado no Azure. As imagens podem ser utilizadas para criar várias VMs que utilizam discos geridos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: b0dc587035509606059e3620201b2061bbe4d6a0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341770"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Criar uma imagem gerida de uma VM generalizada no Azure

Um recurso de imagem gerida pode ser criado a partir de uma máquina virtual (VM) generalizada que é armazenada como um disco gerido ou um disco não gerido numa conta de armazenamento. A imagem, em seguida, pode ser utilizada para criar múltiplas VMs. Para obter informações sobre como são faturadas imagens gerenciadas, consulte [preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM do Windows com o Sysprep

Sysprep remove todas as sua conta pessoal e informações de segurança e, em seguida, prepara a máquina a ser utilizado como uma imagem. Para obter informações sobre o Sysprep, consulte [visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Certifique-se de que as funções de servidor em execução na máquina são suportadas pelo Sysprep. Para obter mais informações, consulte [suporte de Sysprep para funções de servidor](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Depois de executar Sysprep numa VM, VM é considerada *generalizada* e não pode ser reiniciada. O processo de generalizar uma VM não é reversível. Se precisar de manter o funcionamento de VM original, deve criar um [cópia da VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizar a sua cópia. 
>
> Se pretender executar o Sysprep antes de carregar o seu disco rígido virtual (VHD) para o Azure pela primeira vez, certifique-se de que tem [preparou a sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Para generalizar a VM do Windows, siga estes passos:

1. Inicie sessão na sua VM do Windows.
   
2. Abra uma janela de linha de comandos como administrador. Altere o diretório para % windir%\system32\sysprep e, em seguida, execute `sysprep.exe`.
   
3. Na **ferramenta System Preparation** caixa de diálogo, selecione **Enter System Out-of-Box Experience (OOBE)** e selecione o **Generalize** caixa de verificação.
   
4. Para **opções de encerramento**, selecione **encerramento**.
   
5. Selecione **OK**.
   
    ![Inicie Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Quando Sysprep estiver concluída, encerra a VM. Não reinicie a VM.


## <a name="create-a-managed-image-in-the-portal"></a>Criar uma imagem gerida no portal 

1. Abra o [Portal do Azure](https://portal.azure.com).

2. No menu à esquerda, selecione **máquinas virtuais** e, em seguida, selecione a VM a partir da lista.

3. Na **Máquina Virtual** para a VM, no menu superior, selecione **capturar**.

   O **criar imagem** é apresentada a página.

4. Para **nome**, aceite o nome preenchido previamente ou introduza um nome que pretende utilizar para a imagem.

5. Para **grupo de recursos**, selecione **criar nova** e introduza um nome, ou pode selecionar **utilizar existente** e selecione um grupo de recursos para utilizar na lista pendente.

6. Se pretender eliminar a VM de origem depois da imagem ter sido criado, selecione **eliminar automaticamente esta máquina virtual depois de criar a imagem**.

7. Se pretender que a capacidade de utilizar a imagem em qualquer [zona de disponibilidade](../../availability-zones/az-overview.md), selecione **no** para **resiliência de zona**.

8. Selecione **criar** para criar a imagem.

9. Depois da imagem é criada, pode encontrar-o como um **imagem** resource na lista de recursos no grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de uma VM com o Powershell

Criar uma imagem diretamente a partir da VM garante que a imagem inclui todos os discos associados a VM, incluindo o disco do SO e de quaisquer discos de dados. Este exemplo mostra como criar uma imagem gerida a partir de uma VM que utiliza discos geridos.


Antes de começar, certifique-se de que tem a versão mais recente do módulo Azurerm PowerShell, que deve ser a versão 5.7.0 ou posterior. Para localizar a versão, execute `Get-Module -ListAvailable AzureRM.Compute` no PowerShell. Se precisar de atualizar, veja [instalar o Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps). Se estiver a executar PowerShell localmente, execute `Connect-AzAccount` para criar uma ligação com o Azure.


> [!NOTE]
> Se gostaria de armazenar a imagem no armazenamento com redundância de zona, terá de criá-la numa região que suporta [zonas de disponibilidade](../../availability-zones/az-overview.md) e incluir o `-ZoneResilient` parâmetro na configuração de imagem (`New-AzImageConfig` comando).

Para criar uma imagem de VM, siga estes passos:

1. Crie algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Certifique-se de que a VM foi desalocada.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Definir o estado da máquina virtual para o **generalizado**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenha a máquina virtual. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Crie a configuração da imagem.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Criar uma imagem a partir de um disco gerido com o PowerShell

Se quiser criar uma imagem de apenas o disco do SO, especifica o ID de disco gerido como disco do SO:

    
1. Crie algumas variáveis. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Obtenha a VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenha o ID do disco gerido.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Crie a configuração da imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Criar uma imagem a partir de um instantâneo com o Powershell

Pode criar uma imagem gerida a partir de um instantâneo de uma VM generalizada através dos seguintes passos:

    
1. Crie algumas variáveis. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtém o instantâneo.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Crie a configuração da imagem.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Crie a imagem.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Criar uma imagem a partir de um VHD numa conta de armazenamento

Crie uma imagem gerida a partir de um VHD do SO generalizado numa conta de armazenamento. É necessário o URI do VHD na conta de armazenamento, o que é o seguinte formato: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer* /  *vhdfilename.vhd*. Neste exemplo, o VHD está em *mystorageaccount*, um contentor com o nome *vhdcontainer*, e o nome do arquivo VHD *vhdfilename.vhd*.


1.  Crie algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Parar/desalocar a VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque a VM como generalizada.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Crie a imagem com o VHD do SO generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passos Seguintes
- [Criar uma VM a partir de uma imagem gerida](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

