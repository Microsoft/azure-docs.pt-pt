---
title: Criar VM a partir de uma imagem gerida em Azure
description: Crie uma máquina virtual Windows a partir de uma imagem gerida generalizada utilizando o Azure PowerShell ou o portal.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: cefe427a7a6f7a46c838147f3d699423c9f19c7b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201897"
---
# <a name="create-a-vm-from-a-managed-image"></a>Create a VM from a managed image (Criar uma VM a partir de uma imagem personalizada)

Pode criar várias máquinas virtuais (VMs) a partir de uma imagem VM gerida pelo Azure utilizando o portal Azure ou PowerShell. Uma imagem VM gerida contém as informações necessárias para criar um VM, incluindo o SO e discos de dados. Os discos rígidos virtuais (VHDs) que compõem a imagem, incluindo os discos OS e quaisquer discos de dados, são armazenados como discos geridos. 

Antes de criar um novo VM, terá de [criar uma imagem VM gerida](capture-image-resource.md) para usar como imagem de origem e conceder acesso à imagem a qualquer utilizador que tenha acesso à imagem. 

Uma imagem gerida suporta até 20 implementações simultâneas. A tentativa de criar mais de 20 VMs simultaneamente, a partir da mesma imagem gerida, pode resultar em intervalos de provisão devido às limitações de desempenho de armazenamento de um único VHD. Para criar mais de 20 VMs simultaneamente, utilize uma imagem [das Galerias de Imagem Partilhada](shared-image-galleries.md) configurada com 1 réplica para cada 20 implementações VM simultâneas.

## <a name="use-the-portal"></a>Utilizar o portal

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar uma imagem gerida. Procure e selecione **Imagens.**
3. Selecione a imagem que pretende utilizar na lista. A página **de visão geral** abre.
4. Selecione **Criar VM** a partir do menu.
5. Introduza as informações da máquina virtual. O nome de utilizador e a palavra-passe aqui introduzidos serão utilizados para iniciar sessão na máquina virtual. Quando estiver concluído, selecione **OK**. Pode criar o novo VM num grupo de recursos existente, ou escolher **Criar novo** para criar um novo grupo de recursos para armazenar o VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 
7. Em **Definições**, faça as alterações conforme necessário e selecione **OK**. 
8. Na página do resumo, deverá ver o nome da sua imagem listado como **uma imagem privada**. Selecione **Ok** para iniciar a colocação da máquina virtual.


## <a name="use-powershell"></a>Utilizar o PowerShell

Pode utilizar o PowerShell para criar um VM a partir de uma imagem utilizando o parâmetro simplificado definido para o cmdlet [New-AzVm.](/powershell/module/az.compute/new-azvm) A imagem precisa de estar no mesmo grupo de recursos onde vai criar o VM.

 

O parâmetro simplificado definido para [New-AzVm](/powershell/module/az.compute/new-azvm) apenas requer que forneça um nome, grupo de recursos e nome de imagem para criar um VM a partir de uma imagem. New-AzVm utilizará o valor do parâmetro **-Nome** como nome de todos os recursos que cria automaticamente. Neste exemplo, fornecemos nomes mais detalhados para cada um dos recursos, mas deixamos o cmdlet criá-los automaticamente. Também pode criar recursos previamente, como a rede virtual, e passar o nome do recurso para o cmdlet. New-AzVm utilizarão os recursos existentes se os encontrarem pelo nome.

O exemplo a seguir cria um VM chamado *myVMFromImage,* no grupo de recursos *myResourceGroup,* a partir da imagem chamada *myImage*. 


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
[Criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md)
