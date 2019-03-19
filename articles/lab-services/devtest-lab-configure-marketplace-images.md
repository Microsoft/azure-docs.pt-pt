---
title: Configurar definições de imagem do Azure Marketplace no Azure DevTest Labs | Documentos da Microsoft
description: Configurar quais imagens do Azure Marketplace podem ser utilizadas ao criar uma VM no Azure DevTest Labs
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: d0375713c4881c0b73b91fc07bda3ceac2dbc620
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093495"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Configurar definições de imagem do Azure Marketplace no Azure DevTest Labs
DevTest Labs dá suporte a criar VMs com base nas imagens do Azure Marketplace, dependendo de como tiver configurado as imagens do Azure Marketplace, a ser utilizado no seu laboratório. Este artigo mostra como especificar que, se houver, imagens do Azure Marketplace podem ser utilizadas durante a criação de VMs num laboratório. Isto garante que sua equipe tem acesso apenas às imagens do Marketplace que precisam. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Selecione as imagens do Azure Marketplace são permitidas quando criar uma VM
1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido. 
4. No painel do laboratório, selecione **Konfigurace a zásady**.
5. Do laboratório **Konfigurace a zásady** painel em **Bases de Máquina Virtual**, selecione **imagens do Marketplace**.
6. Especifique se pretende que as imagens do Azure Marketplace qualificadas para estar disponível para utilização como base de uma nova VM. Se selecionou **Sim**, em seguida, todos os no Azure Marketplace imagens que cumprem os seguintes critérios são permitidos no laboratório:
   
   * A imagem cria uma única VM, **e**
   * A imagem utiliza o Azure Resource Manager para aprovisionar VMs, **e**
   * A imagem, não precisa de comprar um plano de licenciamento extra
     
     Não se quiser nenhuma imagem seja permitida ou pretender especificar quais imagens pode ser utilizado, selecione **não**.
     
     ![Opção para permitir que todas as imagens do Marketplace ser utilizado como imagens de base para as VMs](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Se selecionou **não** para o passo anterior, o **permitidas imagens/Selecionar tudo** caixa de verificação está ativada. 
   Pode utilizar esta opção juntamente com a caixa de pesquisa para rapidamente selecionar ou desmarcar todos os itens apresentados na lista.
   * Selecione as imagens do Azure Marketplace para permitir que para a criação da VM individualmente, marcando a caixa de seleção correspondente de cada imagem.
   * Selecione nada na lista se não pretender permitir que quaisquer imagens do Azure Marketplace a ser utilizado no laboratório.
   
     ![Pode especificar quais imagens do Azure Marketplace podem ser utilizadas como imagens de base para as VMs](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
Assim que tiver configurado como imagens do Azure Marketplace são permitidas durante a criação de uma VM, a próxima etapa é [adicionar uma VM para o laboratório](devtest-lab-add-vm.md).

