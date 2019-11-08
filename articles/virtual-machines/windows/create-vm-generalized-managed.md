---
title: Criar VM de uma imagem gerenciada no Azure | Microsoft Docs
description: Crie uma máquina virtual do Windows a partir de uma imagem gerenciada generalizada usando Azure PowerShell ou a portal do Azure, no modelo de implantação do Gerenciador de recursos.
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
ms.openlocfilehash: 016b53660499b86de13855cfa994c84d6d9bc702
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749499"
---
# <a name="create-a-vm-from-a-managed-image"></a>Criar uma VM com base em uma imagem gerenciada

Você pode criar várias VMs (máquinas virtuais) de uma imagem de VM gerenciada do Azure usando o portal do Azure ou o PowerShell. Uma imagem de VM gerenciada contém as informações necessárias para criar uma VM, incluindo o sistema operacional e os discos de dados. Os VHDs (discos rígidos virtuais) que compõem a imagem, incluindo os discos do sistema operacional e os discos de dados, são armazenados como discos gerenciados. 

Antes de criar uma nova VM, você precisará [criar uma imagem de VM gerenciada](capture-image-resource.md) para usar como a imagem de origem e conceder acesso de leitura na imagem a qualquer usuário que deve ter acesso à imagem. 


## <a name="use-the-portal"></a>Utilizar o portal

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **todos os recursos**. Você pode classificar os recursos por **tipo** para localizar facilmente suas imagens.
3. Selecione a imagem que você deseja usar na lista. A página **visão geral** da imagem é aberta.
4. Selecione **criar VM** no menu.
5. Introduza as informações da máquina virtual. O nome de usuário e a senha inseridos aqui serão usados para fazer logon na máquina virtual. Ao concluir, selecione **OK**. Você pode criar a nova VM em um grupo de recursos existente ou escolher **criar nova** para criar um novo grupo de recursos para armazenar a VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Visualizar todos** ou altere o filtro **Tipo de disco suportado**. 
7. Em **configurações**, faça as alterações conforme necessário e selecione **OK**. 
8. Na página Resumo, você deve ver o nome da imagem listado como uma **imagem privada**. Selecione **OK** para iniciar a implantação da máquina virtual.


## <a name="use-powershell"></a>Utilizar o PowerShell

Você pode usar o PowerShell para criar uma VM de uma imagem usando o parâmetro simplificado definido para o cmdlet [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) . A imagem precisa estar no mesmo grupo de recursos em que você criará a VM.

 

O parâmetro simplificado definido para [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) requer apenas que você forneça um nome, um grupo de recursos e um nome de imagem para criar uma VM a partir de uma imagem. New-AzVm usará o valor do parâmetro **-Name** como o nome de todos os recursos que ele cria automaticamente. Neste exemplo, fornecemos nomes mais detalhados para cada um dos recursos, mas permitem que o cmdlet os crie automaticamente. Você também pode criar recursos com antecedência, como a rede virtual, e passar o nome do recurso para o cmdlet. New-AzVm usará os recursos existentes se puder encontrá-los por seu nome.

O exemplo a seguir cria uma VM chamada *myVMFromImage*, no grupo de recursos *MyResource* Group, da imagem chamada *MyImage*. 


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
[Criar e gerenciar VMs do Windows com o módulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

