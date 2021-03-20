---
title: Adicione uma declaração de apoio interno a um laboratório em Azure DevTest Labs
description: Saiba como publicar uma declaração de apoio interno a um laboratório em Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c9920e6fe7fbfe2a8d0aeacb896150b342981b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480478"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Adicione uma declaração de apoio interno a um laboratório em Azure DevTest Labs

A Azure DevTest Labs permite personalizar o seu laboratório com uma declaração de suporte interna que fornece aos utilizadores informações de apoio sobre o laboratório. Por exemplo, pode fornecer informações de contacto para que um utilizador saiba como alcançar o suporte interno quando precisa de ajuda na resolução de problemas ou no acesso a recursos no laboratório. Também pode fornecer links para sites internos ou FAQs que a sua equipa pode aceder antes de contactar o suporte.

Uma declaração de suporte interno destina-se a permitir que você publique informações de laboratório que normalmente não mudam com muita frequência. Para notificar os utilizadores sobre informações de laboratório que são de natureza mais temporária – como atualizações recentes às políticas de laboratório – ver [post anúncio em laboratório](devtest-lab-announcements.md).

Pode desativar ou editar facilmente uma declaração de suporte depois de deixar de ser aplicável.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Passos para adicionar uma declaração de apoio a um laboratório existente

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista. (O seu laboratório pode já estar no Painel de Instrumentos em **Todos os Recursos**).
1. Na lista de laboratórios, selecione o laboratório no qual pretende adicionar uma declaração de apoio.  
1. Na área de **visão geral** do laboratório, selecione **Configuração e políticas**.  

    ![Botão de configuração e políticas](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. À esquerda em **DEFINIÇÕES,** selecione **Suporte Interno**.

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Para criar uma mensagem de suporte interna para os utilizadores deste laboratório, desatado ativado para **sim**.

1. No campo **de mensagens De Apoio,** insira a declaração de suporte interna que pretende apresentar aos utilizadores do seu laboratório. A mensagem de apoio aceita Markdown. Ao introduzir o texto da mensagem, pode ver a área **de Pré-visualização** na parte inferior do ecrã para ver como a mensagem aparece para os utilizadores.

    ![Ecrã de suporte interno para criar a mensagem.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. **Selecione Guardar** uma vez que a sua declaração de suporte esteja pronta para publicar.

Quando já não pretender mostrar esta mensagem de apoio aos utilizadores do laboratório, volte à página **de suporte Interna** e desemote **ativado** para **o nº**.

## <a name="steps-for-users-to-view-the-support-message"></a>Passos para os utilizadores visualizarem a mensagem de suporte

1. A partir do [portal Azure,](https://go.microsoft.com/fwlink/p/?LinkID=525040)selecione um laboratório.

1. Na área de **visão geral** do laboratório, selecione **suporte interno**.  

    ![Botão de suporte interno](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Se for publicada uma mensagem de suporte, o utilizador pode vê-la a partir do painel de suporte Interno.

    ![Painel de suporte interno mostrando mensagem de apoio postada](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
* As declarações de suporte interno são normalmente usadas para fornecer informações de suporte que não mudam com frequência. Também pode aprender a [publicar um anúncio num laboratório para](devtest-lab-announcements.md) informar os utilizadores de alterações temporárias ou atualizações para o laboratório.
* [Definir políticas e horários](devtest-lab-set-lab-policy.md) fornece informações sobre como pode aplicar outras restrições e convenções através da sua subscrição, utilizando políticas personalizadas.