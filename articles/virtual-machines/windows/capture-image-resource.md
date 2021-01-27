---
title: Criar uma imagem gerida em Azure
description: Crie uma imagem gerida de um VM generalizado ou VHD em Azure. As imagens podem ser usadas para criar vários VMs que utilizam discos geridos.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 9b63ec5b8a5d0684a0e144de7dfe4114af9777e2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881894"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Create a managed image of a generalized VM in Azure (Criar uma imagem gerida de uma VM generalizada no Azure)

Pode ser criado um recurso de imagem gerida a partir de uma máquina virtual (VM) generalizada que é armazenada como um disco gerido ou um disco não gerido numa conta de armazenamento. A imagem pode ser utilizada para criar múltiplas VMs. Para obter informações sobre como as imagens geridas são faturadas, consulte [os preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/). 

Uma imagem gerida suporta até 20 implementações simultâneas. A tentativa de criar mais de 20 VMs simultaneamente, a partir da mesma imagem gerida, pode resultar em intervalos de provisão devido às limitações de desempenho de armazenamento de um único VHD. Para criar mais de 20 VMs simultaneamente, utilize uma imagem [das Galerias de Imagem Partilhada](../shared-image-galleries.md) configurada com 1 réplica para cada 20 implementações VM simultâneas.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM do Windows com o Sysprep

A Sysprep remove todas as suas informações pessoais e de segurança e, em seguida, prepara a máquina para ser usada como uma imagem. Para obter informações sobre a Sysprep, consulte a visão geral da [Sysprep.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

Certifique-se de que as funções do servidor em funcionamento na máquina são suportadas pela Sysprep. Para obter mais informações, consulte [o suporte do Sysprep para funções de servidor](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) e [cenários não suportados](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Depois de ter executado o Sysprep num VM, esse VM é considerado *generalizado* e não pode ser reiniciado. O processo de generalizar uma VM não é reversível. Se precisar de manter o funcionamento original do VM, deve criar uma [cópia do VM](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) e generalizar a sua cópia. 
>
>Sysprep requer que os discos sejam totalmente desencriptados. Se tiver ativado a encriptação no seu VM, desative a encriptação antes de executar o Sysprep.
>
> Se planeia executar o Sysprep antes de enviar o seu disco rígido virtual (VHD) para Azure pela primeira vez, certifique-se de que [preparou o seu VM](prepare-for-upload-vhd-image.md).  
> 
> 

Para generalizar o seu Windows VM, siga estes passos:

1. Inscreva-se no seu Windows VM.
   
2. Abra uma janela de pedido de comando como administrador. 

3. Elimine o diretório da pantera (C:\Windows\Panther). Em seguida, altere o diretório para %windir%\system32\sysprep e, em seguida, corra `sysprep.exe` .
   
4. Na caixa de diálogo de ferramentas de preparação do **sistema,** selecione **Enter System Out-of-Box Experience (OOBE)** e selecione a caixa de **verificação Generalize.**
   
5. Para **opções de encerramento**, selecione **Shutdown**.
   
6. Selecione **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Quando o Sysprep termina, desliga o VM. Não reinicie a VM.

> [!TIP]
> **Opcional** Utilize [o DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) para otimizar a sua imagem e reduzir o primeiro tempo de arranque do seu VM.
>
> Para otimizar a sua imagem, monte o seu VHD clicando duas vezes nela no explorador do Windows e, em seguida, execute o DISM com o `/optimize-image` parâmetro.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Onde D: é o caminho do VHD montado.
>
> A `DISM /optimize-image` execução deve ser a última modificação que fizer ao seu VHD. Se fizer alguma alteração no seu VHD antes da sua implantação, terá de voltar a `DISM /optimize-image` correr.

## <a name="create-a-managed-image-in-the-portal"></a>Criar uma imagem gerida no portal 

1. Vá ao [portal Azure](https://portal.azure.com) para gerir a imagem VM. Procure e selecione **máquinas Virtuais.**

2. Selecione o seu VM da lista.

3. Na página **da máquina Virtual** para o VM, no menu superior, selecione **Capture**.

   Aparece a página **de imagem Create.**

4. Para **nome**, aceite o nome pré-povoado ou introduza um nome que gostaria de usar para a imagem.

5. Para **o grupo de recursos**, selecione Criar **novo** e introduzir um nome, ou selecione um grupo de recursos para utilizar a partir da lista de drop-down.

6. Se pretender eliminar o VM de origem após a criação da imagem, selecione **eliminar automaticamente esta máquina virtual depois de criar a imagem**.

7. Se quiser a capacidade de utilizar a imagem em qualquer [zona de disponibilidade,](../../availability-zones/az-overview.md)selecione **On** for **Zone resiliency**.

8. Selecione **Criar** para criar a imagem.

Após a criação da imagem, pode encontrá-la como um recurso de **Imagem** na lista de recursos do grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Criar uma imagem de um VM usando PowerShell

 

A criação de uma imagem diretamente a partir do VM garante que a imagem inclui todos os discos associados ao VM, incluindo o disco de OS e quaisquer discos de dados. Este exemplo mostra como criar uma imagem gerida a partir de um VM que utiliza discos geridos.

Antes de começar, certifique-se de que tem a versão mais recente do módulo Azure PowerShell. Para encontrar a versão, corra `Get-Module -ListAvailable Az` no PowerShell. Se precisar de atualizar, consulte [instalar o Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/install-az-ps). Se estiver a executar o PowerShell localmente, corra `Connect-AzAccount` para criar uma ligação com a Azure.


> [!NOTE]
> Se quiser armazenar a sua imagem em armazenamento redundante de zona, precisa criá-la numa região que suporte [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o `-ZoneResilient` parâmetro na configuração de imagem `New-AzImageConfig` (comando).

Para criar uma imagem VM, siga estes passos:

1. Criar algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Certifique-se de que o VM foi transatado.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Desaprova o estado da máquina virtual para **generalizado.** 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Criar uma imagem a partir de um disco gerido usando PowerShell

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Criar uma imagem a partir de um instantâneo usando PowerShell

Pode criar uma imagem gerida a partir de um instantâneo de um VM generalizado seguindo estes passos:

    
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


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Criar uma imagem a partir de um VM que usa uma conta de armazenamento

Para criar uma imagem gerida a partir de um VM que não utilize discos geridos, precisa do URI do OS VHD na conta de armazenamento, no seguinte formato: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer* / *vhdfilename.vhd*. Neste exemplo, o VHD encontra-se na *conta mystorage,* num contentor chamado *vhdcontainer,* e o nome de ficheiro VHD é *vhdfilename.vhd*.


1.  Criar algumas variáveis.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Parar/negociar o VM.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque o VM como generalizado.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Crie a imagem utilizando o seu OS VHD generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Próximos passos
- [Criar um VM a partir de uma imagem gerida](create-vm-generalized-managed.md). 