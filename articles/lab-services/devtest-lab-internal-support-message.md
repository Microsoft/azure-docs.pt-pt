---
title: Adicionar uma instrução de suporte interno a um laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como publicar uma declaração de suporte interno para um laboratório no Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: deb98c2c633200ab4be1d763a94fd2a04979a3b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562343"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adicionar uma instrução de suporte interno a um laboratório no Azure DevTest Labs

Os laboratórios DevTest do Azure permite-lhe personalizar o seu laboratório com uma declaração de suporte interno que fornece aos utilizadores obter informações sobre o laboratório. Por exemplo, pode fornecer informações de contacto para que um utilizador sabe como contactar o suporte interno quando precisarem de ajuda com a resolução de problemas ou aceder aos recursos no laboratório. Também pode fornecer ligações para sites internos ou FAQs sobre a sua equipa pode aceder antes de contactar o suporte.

Destina-se uma declaração de suporte interno para permitir que publique informações de laboratório que normalmente não é alterado com muita frequência. Para notificar os utilizadores sobre as informações de laboratório que é mais por natureza – como recentes atualizações de políticas de laboratório – consulte o artigo [anúncio de mensagem num laboratório](devtest-lab-announcements.md).

Pode facilmente desativar ou editar uma declaração de suporte, depois de já não é aplicável.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Passos para adicionar uma declaração de suporte a um laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista. (Seu laboratório já pode ser mostrado no Dashboard sob **todos os recursos**).
1. Na lista de laboratórios, selecione o laboratório em que pretende adicionar uma declaração de suporte.  
1. O laboratório **descrição geral** área, selecione **Konfigurace a zásady**.  

    ![Botão de configuração e políticas](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. À esquerda sob **configurações**, selecione **suporte interno**.

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Para criar uma mensagem de suporte interno para os utilizadores neste laboratório, defina Enabled como **Sim**.

1. Na **mensagem de suporte** , insira a declaração de suporte interno que pretende apresentar aos seus utilizadores de laboratório. A mensagem de suporte aceita Markdown. Como introduzir o texto da mensagem, pode ver o **pré-visualização** área na parte inferior do ecrã para ver como a mensagem é apresentada aos utilizadores.

    ![Ecrã de suporte interno para criar a mensagem.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Selecione **guardar** assim que a sua declaração de suporte está pronta para ser publicado.

Quando já não pretende mostrar esta mensagem de suporte aos utilizadores de laboratório, volte para o **suporte interno** página e defina **ativado** para **não**.

## <a name="steps-for-users-to-view-the-support-message"></a>Passos para os utilizadores ver a mensagem de suporte

1. Partir do [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), selecione um laboratório.

1. O laboratório **descrição geral** área, selecione **suporte interno**.  

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se uma mensagem de suporte é publicada, o utilizador pode vê-lo a partir do painel de suporte interno.

    ![Painel de suporte interno que mostra a mensagem de suporte publicada](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
* Declarações de suporte interno são normalmente utilizadas para fornecer informações de suporte que não que mudam frequentemente. Também pode saber como [publicar um anúncio a um laboratório](devtest-lab-announcements.md) para informar os utilizadores de temporária as alterações ou atualizações para o laboratório.
* [Definir políticas e agendas](devtest-lab-set-lab-policy.md) fornece informações sobre como pode aplicar outras restrições e convenções em sua assinatura ao utilizar políticas personalizadas.