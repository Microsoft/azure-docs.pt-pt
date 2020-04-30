---
title: Tutorial - Crie imagens VM personalizadas com A PowerShell Azure
description: Neste tutorial, vai aprender a utilizar o Azure PowerShell para criar uma imagem de máquina virtual personalizada no Azure
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 108ff8d89771217ed2833f2a47aa52ff05aa2f13
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82100384"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com o Azure PowerShell

As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para bootstrap implementações e garantir consistência em vários VMs. Neste tutorial, cria a sua própria imagem personalizada de uma máquina virtual Azure utilizando o PowerShell. Saiba como:

> [!div class="checklist"]
> * Executar o Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Listar todas as imagens na sua subscrição
> * Eliminar uma imagem

Na pré-visualização pública, temos o serviço [Azure VM Image Builder.](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) Basta descrever as suas personalizações num modelo, e tratará dos passos de criação de imagem neste artigo. [Experimente o Azure Image Builder (pré-visualização)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Antes de começar

Os passos abaixo detalham como tornar uma VM existente numa imagem personalizada reutilizável que pode utilizar para criar novas instâncias da VM.

Para concluir o exemplo neste tutorial, tem de ter uma máquina virtual existente. Se necessário, esta amostra de [guião](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma para si. Quando trabalhar no tutorial, substitua o grupo de recursos e os nomes da VM sempre que preciso.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um [https://shell.azure.com/powershell](https://shell.azure.com/powershell)separado separado browser, indo para . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="prepare-vm"></a>Preparar a VM

Para criar uma imagem de uma máquina virtual, é necessário preparar a fonte VM generalizando-a, negociando e marcando-a como generalizada com o Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM do Windows com o Sysprep

O Sysprep remove todas as suas informações de conta pessoal, entre outras coisas, e prepara a máquina para ser utilizada como uma imagem. Para mais detalhes sobre o Sysprep, veja [Como utilizar o Sysprep: uma Introdução](https://technet.microsoft.com/library/bb457073.aspx).


1. Ligue à máquina virtual.
2. Abra a janela da Linha de Comandos como administrador. Mude o diretório para *%windir%\system32\sysprep,* e depois executar `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.
4. Em **Opções de Encerramento**, selecione **Encerramento** e, em seguida, clique em **OK**.
5. Quando o Sysprep estiver concluído, encerra a máquina virtual. **Não reinicie a VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desaloque e marque a VM como generalizada

Para criar uma imagem, a VM tem de ser desalocada e marcada como generalizada no Azure.

Deslocar o VM utilizando [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Detete o estado `-Generalized` da máquina virtual para utilizar o [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>Criar a imagem

Agora pode criar uma imagem do VM utilizando [new-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) e [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). O exemplo seguinte cria uma imagem designada *myImage* a partir de uma VM designada *myVM*.

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

Agora que tem uma imagem, pode criar uma ou mais VMs novas a partir da imagem. Criar uma VM a partir de uma imagem personalizada é semelhante à criação de uma VM com uma imagem do Marketplace. Quando utiliza uma imagem do Marketplace, tem de fornecer as informações sobre a imagem, o fornecedor da imagem, a oferta, o SKU e a versão. Utilizando o parâmetro simplificado definido para o cmdlet [New-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) basta fornecer o nome da imagem personalizada desde que esteja no mesmo grupo de recursos. Se planeia criar VM num grupo de recursos diferente, forneça o Id de Recurso da imagem para o parâmetro -ImageName.

Este exemplo cria um VM chamado *myVMfromImage* a partir da imagem *myImage,* no *myResourceGroup*.


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

Recomendamos que limite o número de implementações simultâneas a 20 VMs de uma única imagem. Se estiver a planear implementações em larga escala e simultâneas de mais de 20 VMs a partir da mesma imagem personalizada, deve utilizar uma Galeria de [Imagem Partilhada](shared-image-galleries.md) com múltiplas réplicas de imagem. 


## <a name="image-management"></a>Gestão das imagens 

Seguem-se alguns exemplos de tarefas comuns de imagens geridas e como concluí-las com o PowerShell.

Liste todas as imagens por nome.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Elimine uma imagem. Este exemplo elimina a imagem chamada *myImage* do *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma imagem de VM personalizada. Aprendeu a:

> [!div class="checklist"]
> * Executar o Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM a partir de uma imagem personalizada
> * Listar todas as imagens na sua subscrição
> * Eliminar uma imagem

Avance para o próximo tutorial para aprender como criar máquinas virtuais altamente disponíveis.

> [!div class="nextstepaction"]
> [Criar VMs de elevada disponibilidade](tutorial-availability-sets.md)



