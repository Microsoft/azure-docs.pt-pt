---
title: Definir configurações de imagem do Azure Marketplace no Azure DevTest Labs
description: Configure quais imagens do Azure Marketplace podem ser usadas ao criar uma VM no Azure DevTest Labs
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169559"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Definir configurações de imagem do Azure Marketplace no Azure DevTest Labs
O DevTest Labs dá suporte à criação de VMs com base em imagens do Azure Marketplace, dependendo de como você configurou as imagens do Azure Marketplace a serem usadas em seu laboratório. Este artigo mostra como especificar quais imagens do Azure Marketplace podem ser usadas ao criar VMs em um laboratório. Isso garante que sua equipe só tenha acesso às imagens do Marketplace de que precisam. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecione quais imagens do Azure Marketplace são permitidas ao criar uma VM
1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado. 
4. Na folha do laboratório, selecione **configuração e políticas**.
5. Na folha **configuração e políticas** do laboratório, em **bases da máquina virtual**, selecione **imagens do Marketplace**.
6. Especifique se você deseja que todas as imagens qualificadas do Azure Marketplace estejam disponíveis para uso como uma base de uma nova VM. Se você selecionar **Sim**, todas as imagens do Azure Marketplace que atendem a todos os critérios a seguir serão permitidas no laboratório:
   
   * A imagem cria uma única VM **e**
   * A imagem usa Azure Resource Manager para provisionar VMs **e**
   * A imagem não requer a compra de um plano de licenciamento extra
     
     Se você quiser que nenhuma imagem seja permitida ou se quiser especificar quais imagens podem ser usadas, selecione **não**.
     
     ![Opção para permitir que todas as imagens do Marketplace sejam usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se você selecionar **não** na etapa anterior, a caixa de seleção **imagens permitidas/selecionar tudo** estará habilitada. 
   Você pode usar essa opção junto com a caixa de pesquisa para selecionar ou desmarcar rapidamente todos os itens exibidos na lista.
   * Selecione as imagens do Azure Marketplace que você deseja permitir para a criação de VM individualmente, marcando a caixa de seleção correspondente de cada imagem.
   * Selecione nada na lista se não quiser permitir que nenhuma imagem do Azure Marketplace seja usada no laboratório.
   
     ![Você pode especificar quais imagens do Azure Marketplace podem ser usadas como imagens base para VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de configurar como as imagens do Azure Marketplace são permitidas ao criar uma VM, a próxima etapa é [Adicionar uma VM ao seu laboratório](devtest-lab-add-vm.md).

