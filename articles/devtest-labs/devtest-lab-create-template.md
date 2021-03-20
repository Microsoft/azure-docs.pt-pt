---
title: Crie uma imagem personalizada da Azure DevTest Labs a partir de um ficheiro VHD | Microsoft Docs
description: Saiba como criar uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289404"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Crie uma imagem personalizada a partir de um ficheiro VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos acompanham-no através da criação de uma imagem personalizada a partir de um ficheiro VHD utilizando o portal Azure:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. No painel principal do laboratório, **selecione Configuração e políticas**. 

1. No painel **de configuração e políticas,** selecione **Imagens Personalizadas**.

1. No painel **de imagens personalizado,** selecione **+Adicionar**.

    ![Adicionar imagem personalizada](./media/devtest-lab-create-template/add-custom-image.png)

1. Insira o nome da imagem personalizada. Este nome é apresentado na lista de imagens base ao criar um VM.

1. Introduza a descrição da imagem personalizada. Esta descrição é apresentada na lista de imagens base ao criar um VM.

1. Para **o tipo DE**, selecione **Windows** ou **Linux**.

    - Se selecionar o **Windows**, especifique através da caixa de verificação se o *sysprep* foi executado na máquina. 
    - Se selecionar **o Linux,** especifique através da caixa de verificação se a *deprovisionamento* foi executada na máquina. 

1. Selecione um **VHD** no menu suspenso. Este é o VHD que será usado para criar a nova imagem personalizada. Se necessário, selecione para **carregar um VHD utilizando o PowerShell**.

1. Também pode introduzir um nome de plano, oferta de plano e planejar editor se a imagem usada para criar a imagem personalizada não for uma imagem licenciada (publicada pela Microsoft).

   - **Nome do plano:** Insira o nome da imagem marketplace (SKU) a partir da qual esta imagem personalizada é criada 
   - **Oferta do plano:** Introduza o produto (oferta) da imagem marketplace a partir da qual esta imagem personalizada é criada 
   - **Editor do plano:** Insira o editor da imagem marketplace a partir da qual esta imagem personalizada é criada

   > [!NOTE]
   > Se a imagem que está a usar para criar uma imagem personalizada **não** é uma imagem licenciada, então estes campos estão vazios e podem ser preenchidos se quiser. Se a imagem **é** uma imagem licenciada, então os campos são auto-preenchidos com a informação do plano. Se tentar alterá-las neste caso, é apresentada uma mensagem de aviso.
   >
   >

1. Selecione **OK** para criar a imagem personalizada.

Após alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um utilizador de laboratório quer criar um novo VM, a imagem está disponível na lista de imagens base.

![Imagem personalizada disponível na lista de imagens base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Publicações de blogs relacionadas

- [Imagens ou fórmulas personalizadas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicione um VM ao seu laboratório](./devtest-lab-add-vm.md)
