---
title: Crie uma imagem personalizada do Azure DevTest Labs a partir de um ficheiro VHD [ Microsoft Docs
description: Saiba como criar uma imagem personalizada em Azure DevTest Labs a partir de um ficheiro VHD usando o portal Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61296175"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Criar uma imagem personalizada a partir de um ficheiro VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos passam por você através da criação de uma imagem personalizada a partir de um ficheiro VHD usando o portal Azure:

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. No painel principal do laboratório, selecione **Configuração e políticas**. 

1. No painel **de configuração e políticas,** selecione **imagens personalizadas**.

1. No painel de **imagens Personalizado,** selecione **+Adicionar**.

    ![Adicionar imagem personalizada](./media/devtest-lab-create-template/add-custom-image.png)

1. Insira o nome da imagem personalizada. Este nome é apresentado na lista de imagens base ao criar um VM.

1. Introduza a descrição da imagem personalizada. Esta descrição é apresentada na lista de imagens base ao criar um VM.

1. Para **o tipo OS,** selecione **Windows** ou **Linux**.

    - Se selecionar **o Windows,** especifique através da caixa de verificação se foi executada a *sysprep* na máquina. 
    - Se selecionar **o Linux,** especifique através da caixa de verificação se a *desprovisionamento* foi executada na máquina. 

1. Selecione um **VHD** a partir do menu suspenso. Este é o VHD que será usado para criar a nova imagem personalizada. Se necessário, selecione para **carregar um VHD utilizando powerShell**.

1. Também pode introduzir um nome de plano, oferta de planos e editor de planos se a imagem usada para criar a imagem personalizada não for uma imagem licenciada (publicada pela Microsoft).

   - Nome do **plano:** Introduza o nome da imagem marketplace (SKU) a partir da qual esta imagem personalizada é criada 
   - **Oferta de plano:** Insira o produto (oferta) da imagem marketplace a partir da qual esta imagem personalizada é criada 
   - **Editor de planos:** Insira o editor da imagem marketplace a partir da qual esta imagem personalizada é criada

   > [!NOTE]
   > Se a imagem que está a usar para criar uma imagem personalizada **não** é uma imagem licenciada, então estes campos estão vazios e podem ser preenchidos se escolher. Se a imagem **é** uma imagem licenciada, então os campos são auto-povoados com a informação do plano. Se tentar mudá-las neste caso, é apresentada uma mensagem de aviso.
   >
   >

1. Selecione **OK** para criar a imagem personalizada.

Após alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um utilizador de laboratório quer criar um novo VM, a imagem está disponível na lista de imagens base.

![Imagem personalizada disponível na lista de imagens base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Posts de blog relacionados

- [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre laboratórios Azure DevTest](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Adicione um VM ao seu laboratório](./devtest-lab-add-vm.md)
