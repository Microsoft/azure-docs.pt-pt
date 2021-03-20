---
title: Ative uma imagem licenciada no seu laboratório em Azure DevTest Labs | Microsoft Docs
description: Saiba como ativar uma imagem licenciada em Azure DevTest Labs usando o portal Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e2b6e495be2257548782143517f5a970b424860c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91276361"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Ativar uma imagem licenciada no seu laboratório em Azure DevTest Labs

Em Azure DevTest Labs, uma imagem licenciada é uma imagem que inclui termos e condições – tipicamente de terceiros – que devem ser aceites antes de a imagem ser acessível aos utilizadores em laboratório. As secções seguintes descrevem como trabalhar com imagens licenciadas para que estejam disponíveis para a criação de máquinas virtuais.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Determinar se uma imagem licenciada está disponível para os utilizadores
O primeiro passo para permitir que os utilizadores criem VMs a partir de uma imagem licenciada é garantir que os termos e condições foram aceites para a imagem licenciada. Os passos a seguir mostram como pode ver o estado da oferta de uma imagem licenciada e, se necessário, aceitar os seus termos e condições.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e, em seguida, selecione **DevTest Labs** da lista.

1. Da lista de laboratórios, selecione o laboratório desejado.  

1. No painel esquerdo em **DEFINIÇÕES,** selecione **Configuração e políticas**.

1. No painel esquerdo em **BASES DE MÁQUINA VIRTUAL,** selecione **Imagens do Mercado**. 

    ![Item do menu de imagens de mercado](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    É apresentada uma lista de todas as imagens disponíveis no mercado, incluindo o **STATUS OFERTA** para cada imagem.

    ![Lista de imagens de mercado mostrando o estado da oferta para cada imagem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Uma imagem licenciada mostra um estado de oferta de 
    
    - **Termos aceites:** a imagem licenciada está disponível para os utilizadores para criar VMs. 
    - **Revisão de termos necessária:** a imagem licenciada não está atualmente disponível para os utilizadores. Os termos e condições da licença devem ser aceites antes que os utilizadores de laboratório possam usá-la para criar VMs. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Disponibilizar uma imagem licenciada aos utilizadores de laboratório
Para garantir que uma imagem licenciada está disponível para os utilizadores de laboratório, um proprietário de laboratório com permissões de administração deve primeiro aceitar os termos e condições para essa imagem licenciada. Permitir a implementação programática para a subscrição associada a uma imagem licenciada aceita automaticamente os termos legais e declarações de privacidade para essa imagem. [Trabalhar com o Marketplace Images no Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) fornece informações adicionais sobre a implementação programática de imagens de marketplace.

Pode ativar a implementação programática para uma imagem licenciada seguindo estes passos:

1. No [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)vá à lista de **imagens do Marketplace.**

1. Identifique uma imagem licenciada para a qual pretende que os utilizadores tenham acesso, mas cujos termos não foram aceites. Por exemplo, pode ver uma Máquina Virtual de Ciência de Dados que mostra um estado de revisão de **termos aceites** ou **de termos necessários**.

    ![Screenshot que mostra imagens da Máquina Virtual de Ciência de Dados com estado de oferta de "Termos aceites" e "Revisão de Termos necessários"](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Os VMs da Data Science são imagens da Máquina Virtual Azure, pré-instaladas, configuradas e testadas com várias ferramentas populares que são geralmente usadas para análise de dados, machine learning e formação em IA. [A introdução à Azure Data Science Virtual Machine para Linux e Windows](../machine-learning/data-science-virtual-machine/overview.md) fornece uma grande quantidade de informação sobre DSVMs.
   >
   >

1. Na coluna **STATUS DA OFERTA** para a imagem, selecione **Revisão de Termos necessários**.

1. Na janela configurar a implantação programática, selecione **Ative**.

    ![Configure janela de implantação programática](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Pode ver várias subscrições listadas na janela de implantação programática Configure. Certifique-se de que está a ativar a implementação programática apenas para a subscrição pretendida.
   >
   >


1. Selecione **Guardar**. 

    Na lista de imagens de marketplace, esta imagem mostra agora **os Termos aceites** e está disponível para os utilizadores criarem máquinas virtuais.

> [!NOTE]
> Os utilizadores podem criar uma imagem personalizada a partir de uma imagem licenciada. Consulte [Criar uma imagem personalizada a partir de um ficheiro VHD](devtest-lab-create-template.md) para obter mais informações.
>
>


## <a name="related-blog-posts"></a>Publicações de blogs relacionadas

- [Imagens ou fórmulas personalizadas?](./devtest-lab-faq.md#blog-post)
- [Copiar imagens personalizadas entre Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada a partir de uma VM](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Crie uma imagem personalizada a partir de um ficheiro VHD](devtest-lab-create-template.md)
- [Adicione um VM ao seu laboratório](devtest-lab-add-vm.md)
