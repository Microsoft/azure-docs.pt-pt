---
title: Configure Configurar definições de imagem do Azure Marketplace em Laboratórios Azure DevTest
description: Configure quais imagens do Azure Marketplace podem ser usadas na criação de um VM em Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169559"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configure Configurar definições de imagem do Azure Marketplace em Laboratórios Azure DevTest
A DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace, dependendo de como configuraste imagens do Azure Marketplace para serem usadas no seu laboratório. Este artigo mostra-lhe como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas na criação de VMs num laboratório. Isto garante que a sua equipa só tem acesso às imagens do Marketplace de que necessitam. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecione quais imagens do Azure Marketplace são permitidas ao criar um VM
1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado. 
4. Na lâmina do laboratório, selecione **Configuração e políticas**.
5. Na **configuração** do laboratório e na lâmina de políticas sob **bases de máquinas virtuais,** selecione imagens do **Mercado.**
6. Especifique se deseja que todas as imagens qualificadas do Azure Marketplace estejam disponíveis para utilização como base de um novo VM. Se selecionar **Sim,** então todas as imagens do Azure Marketplace que satisfaçam todos os seguintes critérios são permitidas em laboratório:
   
   * A imagem cria um único VM, **e**
   * A imagem utiliza o Gestor de Recursos Azure para fornecer VMs, **e**
   * A imagem não requer a compra de um plano de licenciamento extra
     
     Se não quiser que sejam permitidas imagens ou se pretender especificar quais as imagens que podem ser utilizadas, selecione **No**.
     
     ![Opção para permitir que todas as imagens do Marketplace sejam usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se selecionar **Não** ao passo anterior, as **imagens permitidas/Selecione todas as** caixas de verificação ativadas. 
   Pode utilizar esta opção juntamente com a caixa de pesquisa para selecionar ou desseleccionar rapidamente todos os itens apresentados na lista.
   * Selecione as imagens do Azure Marketplace que pretende permitir para a criação de VM individualmente, verificando a caixa de verificação correspondente de cada imagem.
   * Selecione nada da lista se não quiser permitir que quaisquer imagens do Azure Marketplace sejam usadas em laboratório.
   
     ![Pode especificar quais imagens do Azure Marketplace podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de configurar como as imagens do Azure Marketplace são permitidas ao criar um VM, o próximo passo é [adicionar um VM ao seu laboratório](devtest-lab-add-vm.md).

