---
title: Criar um VM a partir de um VHD generalizado carregado
description: Faça o upload de um VHD generalizado para a Azure e use-o para criar novos VMs, no modelo de implementação do Gestor de Recursos.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 9d5add1349650663b226023b9f46868f242ac4f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87265216"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e utilizá-lo para criar VMs novas no Azure

Este artigo acompanha-o usando o PowerShell para carregar um VHD de um VM generalizado para Azure, criar uma imagem a partir do VHD, e criar um novo VM a partir dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização no local ou de outra nuvem. A utilização [de Discos Geridos](managed-disks-overview.md) para o novo VM simplifica a gestão de VM e proporciona uma melhor disponibilidade quando o VM é colocado num conjunto de disponibilidade. 

Para obter um script de amostra, consulte [o script sample para carregar um VHD para Azure e criar um novo VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de enviar qualquer VHD para Azure, deve seguir [Preparar um VHD ou VHDX do Windows para fazer o upload para Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Plano de revisão [da migração para Discos Geridos](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a sua migração para [Discos Geridos.](managed-disks-overview.md)

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalize a fonte VM utilizando o Sysprep

Se ainda não o fez, precisa de Sysprep o VM antes de enviar o VHD para Azure. O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para mais detalhes sobre a Sysprep, consulte a [Visão Geral da Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Certifique-se de que as funções do servidor em funcionamento na máquina são suportadas pela Sysprep. Para obter mais informações, consulte [o Suporte Sysprep para funções de servidor.](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se planeia executar o Sysprep antes de enviar o seu VHD para Azure pela primeira vez, certifique-se de que [preparou o seu VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Inicie sessão na máquina virtual do Windows.
2. Abra a janela da Linha de Comandos como administrador. Mude o diretório para %windir%\system32\sysprep e, em seguida, corra `sysprep.exe` .
3. Na caixa de diálogo de ferramentas de preparação do **sistema,** selecione **Enter System Out-of-Box Experience (OOBE) e certifique-se**de que a caixa de **verificação Generalize** está ativada.
4. Para **opções de encerramento**, selecione **Shutdown**.
5. Selecione **OK**.
   
    ![Iniciar Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando a Sysprep termina, desliga a máquina virtual. Não reinicie a VM.


## <a name="upload-the-vhd"></a>Faça o upload do VHD 

Agora pode enviar um VHD diretamente para um disco gerido. Para obter instruções, consulte [o Upload a VHD to Azure utilizando a Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



Uma vez que o VHD é carregado para o disco gerido, você precisa usar [Get-AzDisk](/powershell/module/az.compute/get-azdisk) para obter o disco gerido.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Criar a imagem
Crie uma imagem gerida a partir do seu disco gerido por SISTEMA Generalizado. Substitua os seguintes valores pela sua própria informação.

Primeiro, desa estalem algumas variáveis:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Crie a imagem utilizando o seu disco gerido.

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

## <a name="create-the-vm"></a>Criar a VM

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem. Este exemplo cria um VM chamado *myVM* da *myImage*, no *myResourceGroup*.


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

Inscreva-se na sua nova máquina virtual. Para obter mais informações, consulte [Como ligar e iniciar sessão numa máquina virtual Azure que executa o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
