---
title: Criar uma imagem gerida em Azure
description: Crie uma imagem gerida de um VM ou VHD generalizado em Azure. As imagens podem ser usadas para criar vários VMs que usam discos geridos.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 258bddec85e4ab182ff0b07c49cdc93f92264f95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084469"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Create a managed image of a generalized VM in Azure (Criar uma imagem gerida de uma VM generalizada no Azure)

Pode ser criado um recurso de imagem gerida a partir de uma máquina virtual (VM) generalizada que é armazenada como um disco gerido ou um disco não gerido numa conta de armazenamento. A imagem pode ser utilizada para criar múltiplas VMs. Para obter informações sobre como as imagens geridas são faturadas, consulte [os preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM do Windows com o Sysprep

A Sysprep remove todas as suas informações pessoais e de segurança e, em seguida, prepara a máquina para ser usada como imagem. Para obter informações sobre sysprep, consulte a visão geral da [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Certifique-se de que as funções do servidor que estão a funcionar na máquina são suportadas pela Sysprep. Para mais informações, consulte o [suporte sysprep para funções](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) de servidor e [cenários não suportados](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Depois de ter executado sysprep em um VM, que vM é considerado *generalizado* e não pode ser reiniciado. O processo de generalizar uma VM não é reversível. Se precisar de manter o VM original a funcionar, deverá criar uma [cópia do VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizar a sua cópia. 
>
> Se planeia executar o Sysprep antes de enviar o disco rígido virtual (VHD) para o Azure pela primeira vez, certifique-se de ter [preparado o seu VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Para generalizar o seu VM windows, siga estes passos:

1. Inscreva-se no seu VM windows.
   
2. Abra uma janela de comando como administrador. Mude o diretório para %windir%\system32\sysprep, e depois executar `sysprep.exe`.
   
3. Na caixa de diálogo da ferramenta de preparação do **sistema,** selecione **Enter System Out-of-Box Experience (OOBE)** e selecione a caixa de verificação **Generalize.**
   
4. Para **opções de encerramento,** selecione **Shutdown**.
   
5. Selecione **OK**.
   
    ![Iniciar sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Quando o Sysprep completa, desliga o VM. Não reinicie a VM.

> [!TIP]
> **Opcional** Utilize o [DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) para otimizar a sua imagem e reduzir o primeiro tempo de arranque do VM.
>
> Para otimizar a sua imagem, monte o seu VHD clicando duas vezes `/optimize-image` nele no explorador do Windows e, em seguida, execute o DISM com o parâmetro.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Onde D: é o caminho do VHD montado.
>
> Correr `DISM /optimize-image` deve ser a última modificação que faz no seu VHD. Se fizer alterações no seu VHD antes da implantação, terá de voltar a correr. `DISM /optimize-image`

## <a name="create-a-managed-image-in-the-portal"></a>Criar uma imagem gerida no portal 

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a imagem VM. Procure e selecione **máquinas Virtuais**.

2. Selecione o seu VM da lista.

3. Na página da **máquina Virtual** para o VM, no menu superior, selecione **Capture**.

   A página **de imagem Create** aparece.

4. Para **Nome**, ou aceite o nome pré-povoado ou introduza um nome que gostaria de usar para a imagem.

5. Para **o grupo Recursos,** selecione **Criar novo** e introduza um nome ou selecione um grupo de recursos para utilizar a partir da lista de drop-down.

6. Se pretender eliminar o VM de origem depois de a imagem ter sido criada, **selecione eliminar automaticamente esta máquina virtual depois**de criar a imagem .

7. Se desejar a capacidade de utilizar a imagem em qualquer [zona de disponibilidade,](../../availability-zones/az-overview.md)selecione **On** for **Zone resiliency**.

8. Selecione **Criar** para criar a imagem.

Após a criação da imagem, pode encontrá-la como recurso **Image** na lista de recursos do grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de um VM usando powershell

 

A criação de uma imagem diretamente do VM garante que a imagem inclui todos os discos associados ao VM, incluindo o disco OS e quaisquer discos de dados. Este exemplo mostra como criar uma imagem gerida a partir de um VM que utiliza discos geridos.

Antes de começar, certifique-se de que tem a versão mais recente do módulo PowerShell Azure. Para encontrar a `Get-Module -ListAvailable Az` versão, corra no PowerShell. Se precisar de fazer o upgrade, consulte [Instalar o PowerShell Azure no Windows com o PowerShellGet](/powershell/azure/install-az-ps). Se estiver a executar a `Connect-AzAccount` PowerShell localmente, corra para criar uma ligação com o Azure.


> [!NOTE]
> Se quiser armazenar a sua imagem em armazenamento redundante, precisa criá-la numa região `-ZoneResilient` que suporte zonas de`New-AzImageConfig` [disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro na configuração da imagem (comando).

Para criar uma imagem VM, siga estes passos:

1. Criar algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Certifique-se de que o VM foi transferido.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Desloque o estado da máquina virtual para **generalizar**. 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Criar uma imagem a partir de um disco gerido usando powerShell

Se pretender criar uma imagem apenas do disco OS, especifique o ID do disco gerido como o disco OS:

    
1. Criar algumas variáveis. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Pegue o VM.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obter a identificação do disco gerido.

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Criar uma imagem a partir de um instantâneo usando Powershell

Pode criar uma imagem gerida a partir de uma imagem de um VM generalizado seguindo estes passos:

    
1. Criar algumas variáveis. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Pegue a foto.

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


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Criar uma imagem de um VM que usa uma conta de armazenamento

Para criar uma imagem gerida a partir de um VM que não utilize discos geridos, precisa do URI do VHD osS na conta de armazenamento, no seguinte formato: https://*mystorageaccount*.blob.core.windows.net//*vhdcontainer vhdfilename.vhd .**vhdcontainer* Neste exemplo, o VHD *encontra-se*na minha conta de armazenamento, num recipiente chamado *vhdcontainer,* e o nome de ficheiro VHD é *vhdfilename.vhd*.


1.  Criar algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Parar/desalocar o VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque o VM como generalizado.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Crie a imagem utilizando o seu VHD osso generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Passos seguintes
- [Criar um VM a partir de uma imagem gerida.](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)    

