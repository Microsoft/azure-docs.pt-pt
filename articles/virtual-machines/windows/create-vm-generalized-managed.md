---
title: Criar VM a partir de uma imagem gerida no Azure | Documentos da Microsoft
description: Crie uma máquina virtual do Windows a partir de uma imagem gerida generalizada com o Azure PowerShell ou o portal do Azure, no modelo de implementação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 57fbab4194f6cd232e1462ecea9a07d104c6cb51
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205737"
---
# <a name="create-a-vm-from-a-managed-image"></a>Criar uma VM a partir de uma imagem gerida

Pode criar várias máquinas virtuais (VMs) a partir de uma VM gerida do Azure com o portal do Azure ou do PowerShell de imagem. Uma imagem VM gerida contém as informações necessárias para criar uma VM, incluindo os discos de SO e dados. Os discos rígidos virtuais (VHDs) que formam a imagem, incluindo os discos de SO e qualquer discos de dados, são armazenados como discos geridos. 

Antes de criar uma nova VM, precisará [crie uma imagem VM gerida](capture-image-resource.md) para utilizar como imagem de origem e conceder acesso de leitura na imagem para qualquer utilizador que deve ter acesso à imagem. 


## <a name="use-the-portal"></a>Utilizar o portal

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No menu da esquerda, selecione **todos os recursos**. Pode ordenar os recursos por **tipo** para localizar facilmente as suas imagens.
3. Selecione a imagem que pretende utilizar na lista. A imagem **descrição geral** é aberta a página.
4. Selecione **Create VM** no menu.
5. Introduza as informações da máquina virtual. O nome de utilizador e palavra-passe introduzidos aqui serão utilizadas para iniciar sessão na máquina virtual. Quando terminar, selecione **OK**. Pode criar a nova VM no grupo de recursos existente ou escolha **criar novo** para criar um novo grupo de recursos para armazenar a VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 
7. Sob **configurações**, fazer as alterações necessárias e selecione **OK**. 
8. Na página Resumo, deverá ver seu nome de imagem listado como um **imagem privada**. Selecione **Ok** para iniciar a implementação da máquina virtual.


## <a name="use-powershell"></a>Utilizar o PowerShell

Pode utilizar o PowerShell para criar uma VM a partir de uma imagem usando o parâmetro simplificado definido para o [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet. A imagem tem de estar no mesmo grupo de recursos onde irá criar a VM.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

O parâmetro de simplificada definido para [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) requer apenas que forneça um nome, grupo de recursos e o nome da imagem para criar uma VM a partir de uma imagem. Novo-AzVm utilizará o valor do **-nome** parâmetro como o nome de todos os recursos que ele cria automaticamente. Neste exemplo, podemos fornecer nomes mais detalhados para cada um dos recursos, mas permitir que o cmdlet criá-los automaticamente. Também pode criar recursos com antecedência, como a rede virtual e passar o nome do recurso para o cmdlet. Novo-AzVm irá utilizar os recursos existentes, se ele pode encontrá-los pelo respetivo nome.

O exemplo seguinte cria uma VM com o nome *myVMFromImage*, na *myResourceGroup* grupo de recursos, da imagem com o nome *myImage*. 


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



## <a name="next-steps"></a>Passos Seguintes
[Criar e gerir VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

