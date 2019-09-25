---
title: Criar uma imagem gerenciada no Azure | Microsoft Docs
description: Crie uma imagem gerenciada de uma VM ou um VHD generalizado no Azure. As imagens podem ser usadas para criar várias VMs que usam discos gerenciados.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: c133431bb2b84525a8ea875dea94cec8595733bb
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273869"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Criar uma imagem gerenciada de uma VM generalizada no Azure

Um recurso de imagem gerenciada pode ser criado a partir de uma VM (máquina virtual) generalizada que é armazenada como um disco gerenciado ou um disco não gerenciado em uma conta de armazenamento. A imagem pode então ser usada para criar várias VMs. Para obter informações sobre como as imagens gerenciadas são cobradas, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM do Windows com o Sysprep

O Sysprep remove todas as suas informações pessoais de conta e segurança e, em seguida, prepara a máquina para ser usada como uma imagem. Para obter informações sobre o Sysprep, consulte [visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador têm suporte pelo Sysprep. Para obter mais informações, consulte [suporte do Sysprep para funções de servidor](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) e [cenários sem suporte](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Depois de executar o Sysprep em uma VM, essa VM é considerada *generalizada* e não pode ser reiniciada. O processo de generalizar uma VM não é reversível. Se você precisar manter a VM original funcionando, deverá criar uma [cópia da VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizar sua cópia. 
>
> Se você planeja executar o Sysprep antes de carregar seu VHD (disco rígido virtual) no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Para generalizar sua VM do Windows, siga estas etapas:

1. Entre em sua VM do Windows.
   
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para%WINDIR%\system32\sysprep e execute `sysprep.exe`.
   
3. Na caixa de diálogo **ferramenta de preparação do sistema** , selecione entrar na experiência inicial **do sistema (OOBE)** e marque a caixa de seleção **generalizar** .
   
4. Para **Opções de desligamento**, selecione **desligar**.
   
5. Selecione **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Quando o Sysprep é concluído, ele desliga a VM. Não reinicie a VM.


## <a name="create-a-managed-image-in-the-portal"></a>Criar uma imagem gerenciada no portal 

1. Abra o [Portal do Azure](https://portal.azure.com).

2. No menu à esquerda, selecione **máquinas virtuais** e, em seguida, selecione a VM na lista.

3. Na página **máquina virtual** da VM, no menu superior, selecione **capturar**.

   A página **criar imagem** é exibida.

4. Para **nome**, aceite o nome previamente preenchido ou insira um nome que você deseja usar para a imagem.

5. Para **grupo de recursos**, selecione **criar novo** e insira um nome ou selecione **usar existente** e selecione um grupo de recursos a ser usado na lista suspensa.

6. Se você quiser excluir a VM de origem após a criação da imagem, selecione **excluir automaticamente esta máquina virtual depois de criar a imagem**.

7. Se você quiser a capacidade de usar a imagem em qualquer [zona de disponibilidade](../../availability-zones/az-overview.md), selecione **ativado** para **resiliência de zona**.

8. Selecione **criar** para criar a imagem.

9. Depois que a imagem for criada, você poderá encontrá-la como um recurso de **imagem** na lista de recursos no grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de uma VM usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A criação de uma imagem diretamente da VM garante que a imagem inclua todos os discos associados à VM, incluindo o disco do sistema operacional e os discos de dados. Este exemplo mostra como criar uma imagem gerenciada de uma VM que usa discos gerenciados.

Antes de começar, verifique se você tem a versão mais recente do módulo Azure PowerShell. Para localizar a versão, execute `Get-Module -ListAvailable Az` no PowerShell. Se você precisar atualizar, consulte [instalar Azure PowerShell no Windows com PowerShellGet](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, `Connect-AzAccount` execute para criar uma conexão com o Azure.


> [!NOTE]
> Se você quiser armazenar a imagem em um armazenamento com redundância de zona, precisará criá-la em uma região que ofereça suporte a [zonas](../../availability-zones/az-overview.md) de disponibilidade `-ZoneResilient` e incluir o parâmetro na configuração`New-AzImageConfig` da imagem (comando).

Para criar uma imagem de VM, siga estas etapas:

1. Crie algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Verifique se a VM foi desalocada.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Defina o status da máquina virtual como **generalizado**. 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Criar uma imagem de um disco gerenciado usando o PowerShell

Se você quiser criar uma imagem apenas do disco do sistema operacional, especifique a ID do disco gerenciado como o disco do sistema operacional:

    
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

3. Obtenha a ID do disco gerenciado.

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Criar uma imagem de um instantâneo usando o PowerShell

Você pode criar uma imagem gerenciada de um instantâneo de uma VM generalizada seguindo estas etapas:

    
1. Crie algumas variáveis. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtenha o instantâneo.

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


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Criar uma imagem de uma VM que usa uma conta de armazenamento

Para criar uma imagem gerenciada de uma VM que não usa discos gerenciados, você precisa do URI do VHD do sistema operacional na conta de armazenamento, no seguinte formato: https://*mystorageaccount*. blob.Core.Windows.NET/*vhdcontainer* /  *vhdfilename. vhd*. Neste exemplo, o VHD está em *mystorageaccount*, em um contêiner chamado *vhdcontainer*, e o nome de arquivo VHD é *vhdfilename. vhd*.


1.  Crie algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Pare/Desaloque a VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque a VM como generalizada.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Crie a imagem usando o VHD do sistema operacional generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passos seguintes
- [Crie uma VM com base em uma imagem gerenciada](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

