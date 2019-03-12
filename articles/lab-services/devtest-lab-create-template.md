---
title: Criar uma imagem personalizada do Azure DevTest Labs de um ficheiro VHD | Documentos da Microsoft
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs, de um ficheiro VHD com o portal do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550110"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Criar uma imagem personalizada a partir de um ficheiro VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo através da criação de uma imagem personalizada de um ficheiro VHD com o portal do Azure:

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel de principal do laboratório, selecione **Konfigurace a zásady**. 

1. Sobre o **Konfigurace a zásady** painel, selecione **imagens personalizadas de**.

1. Sobre o **imagens personalizadas** painel, selecione **+ adicionar**.

    ![Adicionar imagem personalizada](./media/devtest-lab-create-template/add-custom-image.png)

1. Introduza o nome da imagem personalizada. Este nome é apresentado na lista de imagens de base ao criar uma VM.

1. Introduza a descrição da imagem personalizada. Esta descrição é apresentada na lista de imagens de base ao criar uma VM.

1. Para **tipo de SO**, selecione **Windows** ou **Linux**.

    - Se selecionou **Windows**, especifique por meio da caixa de verificação se *sysprep* tiver sido executado na máquina. 
    - Se selecionou **Linux**, especifique por meio da caixa de verificação se *desaprovisionamento* tiver sido executado na máquina. 

1. Selecione um **VHD** no menu pendente. Este é o VHD que vai ser utilizado para criar a nova imagem personalizada. Se necessário, selecione para **carregar um VHD com o PowerShell**.

1. Também pode introduzir um nome do plano, a oferta de plano e o publicador do plano se a imagem utilizada para criar a imagem personalizada não é uma imagem licenciada (publicada pela Microsoft).

   - **Nome do plano:** Introduza o nome da imagem do Marketplace (SKU) a partir do qual esta imagem personalizada é criada 
   - **Plano de oferta:** Introduza o produto (oferta) da imagem do Marketplace a partir do qual esta imagem personalizada é criada 
   - **Vydavatel plánu:** Introduza o publicador da imagem do Marketplace a partir do qual esta imagem personalizada é criada

   > [!NOTE]
   > Se a imagem estiver a utilizar para criar uma imagem personalizada é **não** uma imagem licenciada, em seguida, estes campos estão vazios e podem ser preenchidas se escolher. Se a imagem **é** uma imagem licenciada, em seguida, os campos são automaticamente preenchido com as informações de plano. Se tentar altere-as em vez disso, é apresentada uma mensagem de aviso.
   >
   >

1. Selecione **OK** para criar a imagem personalizada.

Após alguns minutos, a imagem personalizada é criada e é armazenada no interior da conta de armazenamento do laboratório. Quando um utilizador de laboratório quiser criar uma nova VM, a imagem está disponível na lista de imagens de base.

![Imagem personalizada disponível na lista de imagens de base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionados

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre o Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos Seguintes

- [Adicionar uma VM para o laboratório](./devtest-lab-add-vm.md)
