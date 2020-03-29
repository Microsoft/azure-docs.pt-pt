---
title: Criar VM a partir de uma imagem gerida em Azure
description: Crie uma máquina virtual Windows a partir de uma imagem gerida generalizada utilizando o Azure PowerShell ou o portal Azure, no modelo de implementação do Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: de59edc2e2c702993efd6187a590264d9aac16a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74841936"
---
# <a name="create-a-vm-from-a-managed-image"></a>Create a VM from a managed image (Criar uma VM a partir de uma imagem personalizada)

Pode criar várias máquinas virtuais (VMs) a partir de uma imagem VM gerida pelo Azure utilizando o portal Azure ou powerShell. Uma imagem VM gerida contém as informações necessárias para criar um VM, incluindo o OS e os discos de dados. Os discos rígidos virtuais (VHDs) que compõem a imagem, incluindo os discos OS e quaisquer discos de dados, são armazenados como discos geridos. 

Antes de criar um novo VM, terá de [criar uma imagem VM gerida](capture-image-resource.md) para usar como imagem de origem e conceder acesso lido na imagem a qualquer utilizador que deva ter acesso à imagem. 


## <a name="use-the-portal"></a>Utilizar o portal

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar uma imagem gerida. Procure e selecione **Imagens**.
3. Selecione a imagem que pretende utilizar na lista. A página **de visão geral** da imagem abre.
4. Selecione **Criar VM** a partir do menu.
5. Introduza as informações da máquina virtual. O nome de utilizador e a palavra-passe aqui introduzidos serão utilizados para iniciar sessão na máquina virtual. Quando estiver concluído, selecione **OK**. Pode criar o novo VM num grupo de recursos existente, ou escolher criar **novos** para criar um novo grupo de recursos para armazenar o VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 
7. Em **Definições,** faça alterações conforme necessário e selecione **OK**. 
8. Na página de resumo, deve ver o seu nome de imagem listado como uma **imagem privada**. Selecione **Ok** para iniciar a implantação da máquina virtual.


## <a name="use-powershell"></a>Utilizar o PowerShell

Pode utilizar o PowerShell para criar um VM a partir de uma imagem utilizando o parâmetro simplificado definido para o cmdlet [New-AzVm.](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) A imagem precisa de estar no mesmo grupo de recursos onde vai criar o VM.

 

O parâmetro simplificado definido para [o New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) apenas requer que forneça um nome, grupo de recursos e nome de imagem para criar um VM a partir de uma imagem. O New-AzVm usará o valor do parâmetro **-Nome** como nome de todos os recursos que cria automaticamente. Neste exemplo, fornecemos nomes mais detalhados para cada um dos recursos, mas deixamos que o cmdlet os crie automaticamente. Também pode criar recursos previamente, como a rede virtual, e passar o nome do recurso para o cmdlet. A New-AzVm utilizará os recursos existentes se os encontrar pelo seu nome.

O exemplo seguinte cria um VM chamado *myVMFromImage,* no grupo de recursos *myResourceGroup,* a partir da imagem chamada *myImage*. 


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



## <a name="next-steps"></a>Passos seguintes
[Crie e gerencie VMs do Windows com o módulo PowerShell Azure](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

