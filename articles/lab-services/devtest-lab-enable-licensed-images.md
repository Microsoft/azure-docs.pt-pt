---
title: Ativar uma imagem licenciada em seu laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como ativar uma imagem licenciada no Azure DevTest Labs no portal do Azure
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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532750"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Ativar uma imagem licenciada em seu laboratório no Azure DevTest Labs

No Azure DevTest Labs, uma imagem licenciada é uma que inclua os termos e condições – normalmente a partir de uma aplicação de terceiros – que tem de ser aceite antes da imagem está acessível a utilizadores no laboratório. As secções seguintes descrevem como trabalhar com imagens licenciadas para que fiquem disponíveis para utilizar para a criação de máquinas virtuais.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Determinar se uma imagem licenciada está disponível para utilizadores
A primeira etapa para permitir que os utilizadores criar VMs a partir de uma imagem licenciada é certificar-se de que os termos e condições foram aceites para a imagem licenciada. Os passos seguintes mostram como pode ver o estado da oferta de uma imagem licenciada e, se necessário, aceitar os termos e condições.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.

1. Na lista de laboratórios, selecione o laboratório pretendido.  

1. No painel esquerdo abaixo **configurações**, selecione **Konfigurace a zásady**.

1. No painel esquerdo abaixo **BASES de máquina VIRTUAL**, selecione **imagens do Marketplace**. 

    ![Item de menu de imagens do Marketplace](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    É apresentada uma lista de todas as imagens do marketplace disponível, incluindo o **oferecem estado** para cada imagem.

    ![Lista de imagens do marketplace que mostra o estado da oferta para cada imagem](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Uma imagem licenciada mostra um Estado de oferta de 
    
    - **Termos aceites:** a imagem licenciada está disponível para os utilizadores a criar VMs. 
    - **É precisa uma revisão de termos:** a imagem licenciada não está atualmente disponível para os utilizadores. Os termos e condições da licença tem de ser aceite antes dos utilizadores de laboratório podem utilizá-lo para criar VMs. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Disponibilizar uma imagem licenciada aos utilizadores de laboratório
Para certificar-se de que uma imagem licenciada está disponível para utilizadores de laboratório, proprietário de um laboratório com permissões de administrador tem primeiro de ser aceitar os termos e condições para essa imagem licenciada. Ativar a implementação programática para a subscrição associada uma imagem licenciada automaticamente aceita os termos legais e declarações de privacidade para essa imagem. [Trabalhar com imagens do Marketplace no Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) fornece informações adicionais sobre a implementação programática de imagens do marketplace.

Pode ativar a implementação programática para uma imagem licenciada através dos seguintes passos:

1. Na [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), aceda à lista de **imagens do Marketplace**.

1. Identificar uma imagem licenciada para o qual pretende que os utilizadores têm acesso, mas cujos termos não foram aceites. Por exemplo, poderá ver uma máquina de Virtual de ciência de dados que mostra o estado **termos aceites** ou **é precisa uma revisão de termos**.

    ![Configurar a janela de implementação programática](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > VMs de ciência de dados são imagens de Máquina Virtual do Azure, pré-instaladas, configuradas e testadas com várias ferramentas populares que são frequentemente utilizadas para análise de dados, aprendizagem e formação em IA. [Introdução ao Azure dados de Máquina Virtual de ciência para Linux e Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) fornece uma grande quantidade de informações sobre as DSVMs.
   >
   >

1. Na **Estado oferecem** coluna para a imagem, selecione **é precisa uma revisão de termos**.

1. Na janela Configurar implementação programática, selecione **ativar**.

    ![Configurar a janela de implementação programática](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Poderá ver várias subscrições listadas na janela de configurar a implementação programática. Certifique-se de que pretende ativar a implementação programática apenas para a subscrição pretendida.
   >
   >


1. Selecione **Guardar**. 

    Na lista de imagens do marketplace, imagem que mostra a agora **termos aceites** e está disponível para os utilizadores criar máquinas virtuais.

> [!NOTE]
> Os utilizadores podem criar uma imagem personalizada a partir de uma imagem licenciada. Ver [criar uma imagem personalizada de um ficheiro VHD](devtest-lab-create-template.md) para obter mais informações.
>
>


## <a name="related-blog-posts"></a>Postagens de blogs relacionados

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre o Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma imagem personalizada a partir de uma VM](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Criar uma imagem personalizada a partir de um ficheiro VHD](devtest-lab-create-template.md)
- [Adicionar uma VM para o laboratório](devtest-lab-add-vm.md)