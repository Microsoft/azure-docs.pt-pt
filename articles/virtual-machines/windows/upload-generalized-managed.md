---
title: Criar uma VM com base em um VHD generalizado carregado
description: Carregue um VHD generalizado no Azure e use-o para criar novas VMs no modelo de implantação do Gerenciador de recursos.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464944"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure

Este artigo explica como usar o PowerShell para carregar um VHD de uma VM generalizada para o Azure, criar uma imagem do VHD e criar uma nova VM a partir dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização local ou de outra nuvem. O uso de [Managed disks](managed-disks-overview.md) para a nova VM simplifica o gerenciamento de VM e fornece melhor disponibilidade quando a VM é colocada em um conjunto de disponibilidade. 

Para obter um script de exemplo, consulte [script de exemplo para carregar um VHD no Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD no Azure, você deve seguir [preparar um VHD do Windows ou VHDX para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Examine [o plano para a migração para Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [Managed disks](managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizar a VM de origem usando o Sysprep

Se você ainda não fez isso, precisará de Sysprep a VM antes de carregar o VHD no Azure. O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para obter detalhes sobre o Sysprep, consulte a [visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador têm suporte pelo Sysprep. Para obter mais informações, consulte [suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se você planeja executar o Sysprep antes de carregar o VHD no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Inicie sessão na máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Altere o diretório para%WINDIR%\system32\sysprep e execute `sysprep.exe`.
3. Na caixa de diálogo **ferramenta de preparação do sistema** , selecione entrar no **OOBE (experiência inicial do sistema)** e verifique se a caixa de seleção **generalizar** está habilitada.
4. Para **Opções de desligamento**, selecione **desligar**.
5. Selecione **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep é concluído, ele desliga a máquina virtual. Não reinicie a VM.


## <a name="upload-the-vhd"></a>Carregar o VHD 

Agora você pode carregar um VHD diretamente em um disco gerenciado. Para obter instruções, consulte [carregar um VHD no Azure usando Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Depois que o VHD for carregado no disco gerenciado, você precisará usar [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) para obter o disco gerenciado.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Criar a imagem
Crie uma imagem gerenciada do seu disco de gerenciamento generalizado do sistema operacional. Substitua os seguintes valores por suas próprias informações.

Primeiro, defina algumas variáveis:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Crie a imagem usando seu disco gerenciado.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Crie a imagem.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Crie a VM

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem. Este exemplo cria uma VM chamada *myVM* a partir de *MyImage*, no *MyResource*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Passos seguintes

Entre em sua nova máquina virtual. Para obter mais informações, consulte [como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

