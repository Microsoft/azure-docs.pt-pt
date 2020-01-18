---
title: Adicionar uma instrução de suporte interno a um laboratório no Azure DevTest Labs
description: Saiba como postar uma declaração de suporte interna em um laboratório no Azure DevTest Labs
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170359"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma instrução de suporte interno a um laboratório no Azure DevTest Labs

Azure DevTest Labs permite que você personalize seu laboratório com uma declaração de suporte interna que fornece aos usuários informações de suporte sobre o laboratório. Por exemplo, você pode fornecer informações de contato para que um usuário saiba como acessar o suporte interno quando precisar de ajuda com a solução de problemas ou o acesso a recursos no laboratório. Você também pode fornecer links para sites internos ou perguntas frequentes que sua equipe pode acessar antes de entrar em contato com o suporte.

Uma declaração de suporte interna destina-se a permitir que você poste informações de laboratório que normalmente não são alteradas com muita frequência. Para notificar os usuários sobre as informações de laboratório que são mais temporárias por natureza, como atualizações recentes para políticas de laboratório, consulte [postar comunicado em um laboratório](devtest-lab-announcements.md).

Você pode facilmente desabilitar ou editar uma instrução de suporte depois que ela não for mais aplicável.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Etapas para adicionar uma instrução de suporte a um laboratório existente

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista. (Seu laboratório já pode ser mostrado no painel em **todos os recursos**).
1. Na lista de laboratórios, selecione o laboratório no qual você deseja adicionar uma instrução de suporte.  
1. Na área **visão geral** do laboratório, selecione **configuração e políticas**.  

    ![Botão configuração e políticas](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. À esquerda em **configurações**, selecione **suporte interno**.

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Para criar uma mensagem de suporte interno para os usuários neste laboratório, defina habilitado como **Sim**.

1. No campo **mensagem de suporte** , insira a instrução de suporte interna que você deseja apresentar aos usuários do laboratório. A mensagem de suporte aceita a redução. Ao inserir o texto da mensagem, você pode exibir a área de **Visualização** na parte inferior da tela para ver como a mensagem aparece para os usuários.

    ![Tela de suporte interno para criar a mensagem.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selecione **salvar** quando sua declaração de suporte estiver pronta para ser postada.

Quando você não quiser mais mostrar essa mensagem de suporte aos usuários do laboratório, retorne à página de **suporte interna** e defina **habilitado** como **não**.

## <a name="steps-for-users-to-view-the-support-message"></a>Etapas para os usuários exibirem a mensagem de suporte

1. No [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório.

1. Na área **visão geral** do laboratório, selecione **suporte interno**.  

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se uma mensagem de suporte for postada, o usuário poderá exibi-la no painel de suporte interno.

    ![Painel de suporte interno mostrando mensagem de suporte postada](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* As instruções de suporte interno normalmente são usadas para fornecer informações de suporte que não são alteradas com frequência. Você também pode aprender a [postar um comunicado em um laboratório](devtest-lab-announcements.md) para informar os usuários sobre alterações temporárias ou atualizações no laboratório.
* [Definir políticas e agendas](devtest-lab-set-lab-policy.md) fornece informações sobre como você pode aplicar outras restrições e convenções em sua assinatura usando políticas personalizadas.