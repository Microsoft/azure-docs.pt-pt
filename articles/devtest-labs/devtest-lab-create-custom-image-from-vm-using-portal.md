---
title: Crie uma imagem personalizada da Azure DevTest Labs a partir de um VM | Microsoft Docs
description: Saiba como criar uma imagem personalizada em Azure DevTest Labs a partir de um VM provisionado usando o portal Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad45ed6eb7f97e14ec0ca0bb89efb2967c90fc16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87277032"
---
# <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada a partir de uma VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Você pode criar uma imagem personalizada a partir de um VM provisionado, e depois usar essa imagem personalizada para criar VMs idênticos. Os seguintes passos ilustram como criar uma imagem personalizada a partir de um VM:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. No painel principal do laboratório, selecione **As minhas máquinas virtuais.**
 
1. No painel **de máquinas virtuais My,** selecione o VM a partir do qual pretende criar a imagem personalizada.

1. No painel de gestão do VM, selecione **Criar imagem personalizada** em **OPERAÇÕES**.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Crie um item de menu de imagem personalizado":::
1. No painel **de imagem personalizado,** insira um nome e descrição para a sua imagem personalizada. Esta informação é apresentada na lista de bases quando cria um VM. A imagem personalizada incluirá o disco DE e todos os discos de dados ligados à máquina virtual.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Criar página de imagem personalizada":::
1. Selecione se o sysprep foi executado no VM. Se o sysprep não foi executado no VM, especifique se deseja que o sysprep seja executado no VM quando a imagem personalizada for criada.
1. Selecione **OK** quando terminar para criar a imagem personalizada.

    Após alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um utilizador de laboratório quer criar um novo VM, a imagem está disponível na lista de imagens base.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="imagem personalizada disponível na lista de imagens base":::

## <a name="related-blog-posts"></a>Publicações de blogs relacionadas

- [Imagens ou fórmulas personalizadas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicione um VM ao seu laboratório](devtest-lab-add-vm.md)
