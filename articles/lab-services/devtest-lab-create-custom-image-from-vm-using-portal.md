---
title: Crie uma imagem personalizada do Azure DevTest Labs a partir de um VM [ Microsoft Docs
description: Saiba como criar uma imagem personalizada em Azure DevTest Labs a partir de um VM provisionado usando o portal Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60868504"
---
# <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada a partir de uma VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Você pode criar uma imagem personalizada a partir de um VM provisionado, e depois usar essa imagem personalizada para criar VMs idênticos. Os seguintes passos ilustram como criar uma imagem personalizada a partir de um VM:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. No painel principal do laboratório, selecione **As minhas máquinas virtuais.**
 
1. No painel **My virtual machines,** selecione o VM a partir do qual pretende criar a imagem personalizada.

1. No painel de gestão da VM, selecione **Criar imagem personalizada (VHD)**.

    ![Criar item de menu de imagem personalizado](./media/devtest-lab-create-template/create-custom-image.png)

1. No painel de **imagens Personalizado,** introduza um nome e descrição para a sua imagem personalizada. Esta informação é apresentada na lista de bases quando cria um VM. A imagem personalizada incluirá o disco OS e todos os discos de dados ligados à máquina virtual.

    ![Criar painel de imagem personalizado](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecione se a sysprep foi executada no VM. Se o sysprep não foi executado no VM, especifique se você quer que o sysprep seja executado no VM quando a imagem personalizada for criada.

1. Selecione **OK** quando terminar para criar a imagem personalizada.

Após alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um utilizador de laboratório quer criar um novo VM, a imagem está disponível na lista de imagens base.

![Imagem personalizada disponível na lista de imagens base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Posts de blog relacionados

- [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre laboratórios Azure DevTest](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicione um VM ao seu laboratório](devtest-lab-add-vm.md)
