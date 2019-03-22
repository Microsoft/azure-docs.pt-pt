---
title: Tutorial - Criar imagens de VM personalizadas com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar uma imagem de máquina virtual personalizada no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 74087a6d1ce00293c968837e72c636847081e39e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285989"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com o Azure PowerShell

As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser utilizadas para iniciar as implementações e assegurar a consistência entre várias VMs. Neste tutorial, vai criar sua própria imagem personalizada de uma máquina virtual do Azure com o PowerShell. Saiba como:

> [!div class="checklist"]
> * Executar o Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Listar todas as imagens na sua subscrição
> * Eliminar uma imagem

## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se for preciso, este [script de exemplo](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar um para si. Quando trabalhar no tutorial, substitua o grupo de recursos e os nomes da VM sempre que preciso.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="prepare-vm"></a>Preparar a VM

Para criar uma imagem de uma máquina virtual, terá de preparar a VM de origem ao generalizar ele, a desalocar e, em seguida, marcando-o como generalizado com o Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM do Windows com o Sysprep

O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para obter detalhes sobre o Sysprep, consulte [como utilizar Sysprep: Uma introdução](https://technet.microsoft.com/library/bb457073.aspx).


1. Ligue à máquina virtual.
2. Abra a janela da Linha de Comandos como administrador. Altere o diretório para *%windir%\system32\sysprep*e, em seguida, execute `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.
4. Em **Opções de Encerramento**, selecione **Encerramento** e, em seguida, clique em **OK**.
5. Quando o Sysprep estiver concluído, encerra a máquina virtual. **Não reinicie a VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desaloque e marque a VM como generalizada

Para criar uma imagem, a VM tem de ser desalocada e marcada como generalizada no Azure.

Desaloque a VM com [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Definir o estado da máquina virtual para o `-Generalized` usando [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Criar a imagem

Agora, pode criar uma imagem da VM utilizando [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) e [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). O exemplo seguinte cria uma imagem designada *myImage* a partir de uma VM designada *myVM*.

Obtenha a máquina virtual. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Crie a configuração da imagem.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Crie a imagem.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Criar VMs a partir da imagem

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem. Criar uma VM a partir de uma imagem personalizada é semelhante à criação de uma VM com uma imagem do Marketplace. Quando utiliza uma imagem do Marketplace, tem de fornecer as informações sobre a imagem, o fornecedor da imagem, a oferta, o SKU e a versão. Utilizar o parâmetro simplificado definido para o [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet, só precisa fornecer o nome da imagem personalizada, desde que ele está no mesmo grupo de recursos. 

Este exemplo cria uma VM com o nome *myVMfromImage* partir do *myImage* , além de imagens *myResourceGroup*.


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Gestão das imagens 

Seguem-se alguns exemplos de tarefas comuns de imagens geridas e como concluí-las com o PowerShell.

Liste todas as imagens por nome.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Elimine uma imagem. Este exemplo elimina a imagem denominada *myImage* partir do *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou uma imagem de VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Executar o Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Listar todas as imagens na sua subscrição
> * Eliminar uma imagem

Avance para o próximo tutorial para saber mais sobre como criar máquinas virtuais de elevada disponibilidade.

> [!div class="nextstepaction"]
> [Criar VMs de elevada disponibilidade](tutorial-availability-sets.md)



