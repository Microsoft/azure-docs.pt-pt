---
title: Ative uma imagem licenciada no seu laboratório em Azure DevTest Labs [ Microsoft Docs
description: Saiba como ativar uma imagem licenciada em Azure DevTest Labs usando o portal Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61294340"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Ative uma imagem licenciada no seu laboratório em Azure DevTest Labs

No Azure DevTest Labs, uma imagem licenciada é uma imagem que inclui termos e condições – tipicamente de terceiros – que devem ser aceites antes de a imagem ser acessível aos utilizadores do laboratório. As seguintes secções descrevem como trabalhar com imagens licenciadas para que estejam disponíveis para serem usadas para criar máquinas virtuais.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Determinar se uma imagem licenciada está disponível para os utilizadores
O primeiro passo para permitir que os utilizadores criem VMs a partir de uma imagem licenciada é garantir que os termos e condições foram aceites para a imagem licenciada. Os seguintes passos mostram como pode ver o estado da oferta de uma imagem licenciada e, se necessário, aceitar os seus termos e condições.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. No painel esquerdo em **DEFINIÇÕES,** **selecione Configuração e políticas**.

1. No painel esquerdo em **BASES DE MÁQUINAS VIRTUAIS,** selecione **imagens do Mercado**. 

    ![Item do menu de imagens do mercado](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    É apresentada uma lista de todas as imagens de mercado disponíveis, incluindo o STATUS DE **OFERTA** para cada imagem.

    ![Lista de imagens de mercado que mostram o estado da oferta para cada imagem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Uma imagem licenciada mostra um estado de oferta de 
    
    - **Termos aceites:** a imagem licenciada está disponível para os utilizadores criarem VMs. 
    - Revisão de **termos necessária:** a imagem licenciada não está disponível para os utilizadores. Os termos e condições da licença devem ser aceites antes que os utilizadores do laboratório possam usá-la para criar VMs. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Disponibilizar uma imagem licenciada aos utilizadores de laboratório
Para garantir que uma imagem licenciada está disponível para os utilizadores de laboratório, um proprietário de laboratório com permissões de administração deve primeiro aceitar os termos e condições para essa imagem licenciada. Permitir a implementação programática para a subscrição associada a uma imagem licenciada aceita automaticamente os termos legais e as declarações de privacidade para essa imagem. [Trabalhar com imagens](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) do Marketplace no Gestor de Recursos Azure fornece informações adicionais sobre a implementação programática de imagens de marketplace.

Pode ativar a implementação programática para uma imagem licenciada seguindo estes passos:

1. No [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)vá à lista de imagens do **Marketplace.**

1. Identifique uma imagem licenciada para a qual deseja que os utilizadores tenham acesso, mas cujos termos não foram aceites. Por exemplo, pode ver uma Máquina Virtual de Ciência de Dados que mostre um estatuto de **termos aceites** ou revisão de **Termos necessários**.

    ![Configurar janela de implantação programática](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Os VMs da Ciência dos Dados são imagens da Máquina Virtual Azure, pré-instaladas, configuradas e testadas com várias ferramentas populares que são comumente usadas para análise de dados, machine learning e formação de IA. A introdução à Máquina Virtual de Ciência de [Dados Azure para Linux e Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) fornece uma grande quantidade de informação sobre DSVMs.
   >
   >

1. Na coluna **'STATUS'** DA OFERTA para a imagem, selecione **a revisão de Termos necessárias**.

1. Na janela de implantação programática configurar, selecione **Ativar**.

    ![Configurar janela de implantação programática](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Pode ver várias subscrições listadas na janela de implementação programática de Configuração. Certifique-se de que está a permitir a implementação programática apenas para a subscrição pretendida.
   >
   >


1. Selecione **Guardar**. 

    Na lista de imagens do mercado, essa imagem mostra agora **os Termos aceites** e está disponível para os utilizadores criarem máquinas virtuais.

> [!NOTE]
> Os utilizadores podem criar uma imagem personalizada a partir de uma imagem licenciada. Consulte [Criar uma imagem personalizada a partir de um ficheiro VHD](devtest-lab-create-template.md) para obter mais informações.
>
>


## <a name="related-blog-posts"></a>Posts de blog relacionados

- [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre laboratórios Azure DevTest](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada a partir de uma VM](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Criar uma imagem personalizada a partir de um ficheiro VHD](devtest-lab-create-template.md)
- [Adicione um VM ao seu laboratório](devtest-lab-add-vm.md)