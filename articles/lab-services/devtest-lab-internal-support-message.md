---
title: Adicione uma declaração de apoio interno a um laboratório em Azure DevTest Labs
description: Saiba como publicar uma declaração de apoio interno a um laboratório em Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76170359"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adicione uma declaração de apoio interno a um laboratório em Azure DevTest Labs

A Azure DevTest Labs permite personalizar o seu laboratório com uma declaração de suporte interno que fornece aos utilizadores informações de apoio sobre o laboratório. Por exemplo, pode fornecer informações de contacto para que um utilizador saiba como alcançar o suporte interno quando precisa de ajuda para resolver problemas ou aceder a recursos no laboratório. Também pode fornecer links para sites internos ou PERGUNTAS A que a sua equipa pode aceder antes de contactar o suporte.

Uma declaração de apoio interno destina-se a permitir-lhe publicar informações de laboratório que normalmente não mudam com muita frequência. Para notificar os utilizadores sobre informações de laboratório mais temporárias – como atualizações recentes às políticas de laboratório – consulte o [anúncio do Post num laboratório.](devtest-lab-announcements.md)

Pode facilmente desativar ou editar uma declaração de suporte depois de já não ser aplicável.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Passos para adicionar uma declaração de apoio a um laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista. (O seu laboratório pode já estar no Dashboard em **Todos os Recursos).**
1. Da lista de laboratórios, selecione o laboratório no qual pretende adicionar uma declaração de apoio.  
1. Na área de **visão geral** do laboratório, selecione **Configuração e políticas**.  

    ![Botão de configuração e políticas](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. À esquerda em **DEFINIÇÕES,** selecione **suporte interno**.

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Para criar uma mensagem de suporte interno para os utilizadores neste laboratório, detete ativado para **Sim**.

1. No campo de **mensagens de suporte,** introduza a declaração de suporte interno que pretende apresentar aos utilizadores do laboratório. A mensagem de apoio aceita o Markdown. Ao introduzir o texto de mensagem, pode ver a área **de Pré-visualização** na parte inferior do ecrã para ver como a mensagem aparece para os utilizadores.

    ![Ecrã de suporte interno para criar a mensagem.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selecione **Guardar** assim que a sua declaração de suporte estiver pronta para publicar.

Quando já não quiser mostrar esta mensagem de suporte aos utilizadores do laboratório, volte à página de **suporte interno** e desemque **ativado** para **O**.

## <a name="steps-for-users-to-view-the-support-message"></a>Passos para os utilizadores verem a mensagem de suporte

1. A partir do [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)selecione um laboratório.

1. Na área de **visão geral** do laboratório, selecione suporte **interno**.  

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se for publicada uma mensagem de suporte, o utilizador pode vê-la a partir do painel de suporte interno.

    ![Painel de suporte interno mostrando mensagem de apoio postada](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* As declarações de apoio interno são normalmente usadas para fornecer informações de suporte que não mudam com frequência. Também pode aprender a [publicar um anúncio num laboratório](devtest-lab-announcements.md) para informar os utilizadores de alterações temporárias ou atualizações para o laboratório.
* [Definir políticas e horários](devtest-lab-set-lab-policy.md) fornece informações sobre como pode aplicar outras restrições e convenções em toda a sua subscrição usando políticas personalizadas.