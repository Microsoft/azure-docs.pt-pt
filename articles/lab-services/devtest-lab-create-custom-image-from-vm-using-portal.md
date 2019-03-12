---
title: Criar uma imagem personalizada do Azure DevTest Labs a partir de uma VM | Documentos da Microsoft
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs a partir de uma VM aprovisionada com o portal do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550093"
---
# <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada a partir de uma VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Pode criar uma imagem personalizada a partir de uma VM aprovisionada e, depois, utilizar essa imagem personalizada para criar VMs idênticas. Os passos seguintes mostram como criar uma imagem personalizada a partir de uma VM:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel de principal do laboratório, selecione **minhas máquinas virtuais**.
 
1. Sobre o **minhas máquinas virtuais** painel, selecione a VM a partir do qual pretende criar a imagem personalizada.

1. No painel de gestão da VM, selecione **criar imagem personalizada (VHD)**.

    ![Criar item de menu de imagem personalizada](./media/devtest-lab-create-template/create-custom-image.png)

1. Sobre o **Custom image** painel, introduza um nome e descrição para a sua imagem personalizada. Estas informações são apresentadas na lista de bases de quando cria uma VM. A imagem personalizada inclui o disco do SO e todos os discos de dados anexados à máquina virtual.

    ![Criar o painel de imagem personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecione se o sysprep foi executado na VM. Se o sysprep não foi executado na VM, especifique se pretende que o sysprep para ser executado na VM quando é criada a imagem personalizada.

1. Selecione **OK** quando terminar de criar a imagem personalizada.

Após alguns minutos, a imagem personalizada é criada e é armazenada no interior da conta de armazenamento do laboratório. Quando um utilizador de laboratório quiser criar uma nova VM, a imagem está disponível na lista de imagens de base.

![Imagem personalizada disponível na lista de imagens de base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionados

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre o Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos Seguintes

- [Adicionar uma VM para o laboratório](devtest-lab-add-vm.md)
