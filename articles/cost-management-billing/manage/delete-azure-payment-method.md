---
title: Eliminar um método de pagamento da faturação do Azure
description: Descreve como eliminar um método de pagamento utilizado por uma subscrição do Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827547"
---
# <a name="delete-an-azure-billing-payment-method"></a>Eliminar um método de pagamento da faturação do Azure

Este documento fornece instruções para ajudar a eliminar um método de pagamento, como um cartão de crédito, dos diferentes tipos de assinaturas do Azure. Pode eliminar um método de pagamento para:

- Contrato de Cliente da Microsoft (MCA)
- Programa de Subscrições Online da Microsoft (MOSP), também conhecido como pay as you go

Seja qual for o tipo de subscrição do Azure, tem de cancelá-la para que possa eliminar o seu método de pagamento associado.

Não é possível remover um método de pagamento para outros tipos de subscrição do Azure, como o Contrato de Parceiro da Microsoft e o Contrato Enterprise.

## <a name="delete-an-mca-payment-method"></a>Eliminar um método de pagamento MCA

Apenas o utilizador que criou a conta do Contrato de Cliente Microsoft pode eliminar um método de pagamento.

Para eliminar um método de pagamento de um Contrato de Cliente Microsoft, siga os seguintes passos.

1. Inicie sessão no portal do Azure em https://portal.azure.com/.
1. Navegue para **Cost Management + Faturação**.
1. Se for necessário, selecione um âmbito da faturação.
1. Na lista do menu à esquerda, em **Faturação**, selecione **Perfis de faturação**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Captura de ecrã de exemplo a mostrar os Perfis de faturação no portal do Azure" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. Na lista de perfis de faturação, selecione o perfil onde o método de pagamento é utilizado.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Imagem de exemplo a mostrar a lista de perfis de faturação" :::
1. Na lista de menu à esquerda, em **Definições**, selecione **Métodos de pagamento**.
1. Na página de métodos de pagamento do seu perfil de faturação, é mostrada uma tabela de métodos de pagamento na secção **Os seus cartões de crédito**. Procure o cartão de crédito que pretende eliminar, selecione as reticências ( **…** ) e, em seguida, selecione **Eliminar**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Exemplo a mostrar onde eliminar um cartão de crédito" :::
1. A página Eliminar um método de pagamento é apresentada. O Azure verifica se o método de pagamento está a ser utilizado.
    - Quando o método de pagamento não está em utilização, é ativada a opção **Eliminar**. Selecione-a para eliminar as informações de cartão de crédito.
    - Se o método de pagamento estiver a ser utilizado, terá de ser substituído ou desanexado. Continue a ler as seguintes secções. As mesmas explicam como **desanexar** o método de pagamento a ser utilizado pela sua subscrição.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Desanexar método de pagamento utilizado por um perfil de faturação MCA

Se o seu método de pagamento estiver a ser utilizado por um perfil de faturação MCA, verá uma mensagem semelhante ao exemplo seguinte.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Imagem de exemplo a mostrar um método de pagamento em utilização por um Contrato de Cliente Microsoft" :::

Para desanexar um método de pagamento, é necessário cumprir uma lista de condições. Se alguma das condições não se cumprir, aparecem instruções a explicar como cumprir a mesma. Também é apresentada uma ligação que o direciona para a localização em que pode resolver essa condição.

Quando todas as condições forem satisfeitas, pode desanexar o método de pagamento do perfil de faturação.

> [!NOTE]
> Quando o método de pagamento predefinido for desanexado, será colocado em estado _inativo_. Qualquer elemento eliminado neste processo será irrecuperável. Depois de um perfil de faturação ser definido como inativo, tem de se inscrever numa nova subscrição do Azure para criar novos recursos.

#### <a name="to-detach-a-payment-method"></a>Para desanexar um método de pagamento

1. Na área Eliminar um método de pagamento, selecione a ligação **Desanexar o método de pagamento atual**.
1. Se todas as condições forem cumpridas, selecione **Desanexar**. Caso contrário, avance para o passo seguinte.
1. Se a opção Desanexar estiver indisponível, uma lista de condições será mostrada. Execute as ações listadas. Selecione a ligação mostrada na área Desanexar o método de pagamento predefinido. Eis um exemplo de uma ação corretiva que explica o que tem de fazer.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Exemplo a mostrar uma ação corretiva necessária para desanexar um método de pagamento de MCA" :::
1. Ao selecionar a ligação de ação corretiva, será redirecionado para a página do Azure na qual tem de realizar uma ação. Realize a ação de correção necessária.
1. Se necessário, conclua todas as outras ações corretivas.
1. Regresse a **Gestão de Custos + Faturação** > **Perfis de faturação** > **Métodos de pagamento**. Selecione **Desanexar**. No final da página Desanexar o método de pagamento predefinido, selecione **Desanexar**.

> [!NOTE]
> - Depois de cancelar uma subscrição, podem demorar até 90 dias para a subscrição ser eliminada. Se pretender que este tempo de espera seja reduzido, abra um pedido de suporte do Azure e peça que a subscrição seja eliminada imediatamente.
> - Só pode eliminar um método de pagamento depois de liquidar todas as anteriores cobranças para um perfil de faturação. Se estiver num período de faturação ativo, tem de aguardar até ao final do período de faturação para eliminar o seu método de pagamento. **Confirme se todas as outras condições de desanexação são cumpridas enquanto aguarda o final do período de faturação**.

## <a name="delete-a-mosp-payment-method"></a>Eliminar um método de pagamento MOSP

Tem de ser um administrador de conta para eliminar um método de pagamento.

Se o seu método de pagamento estiver a ser utilizado por uma subscrição MOSP, siga os seguintes passos.

1. Inicie sessão no portal do Azure em https://portal.azure.com/.
1. Navegue para **Cost Management + Faturação**.
1. Se for necessário, selecione um âmbito da faturação.
1. Na lista de menu à esquerda, em **Faturação**, selecione **Métodos de pagamento**.
1. Na área Métodos de pagamento, selecione a _linha_ em que se encontra o seu método de pagamento. Não selecione a ligação de método de pagamento. Poderá não haver indicação visual de que selecionou o método de pagamento.
1. Selecione **Eliminar**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Exemplo a mostrar uma ação corretiva necessária para desanexar um método de pagamento de MOSP" :::
1. Na área Eliminar um método de pagamento, selecione **Eliminar** se todas as condições forem cumpridas. Se a opção Eliminar estiver indisponível, continue para o próximo passo.
1. É apresentada uma lista de condições. Execute as ações listadas. Selecione a ligação mostrada na área Eliminar um método de pagamento.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Imagem de exemplo a mostrar um método de pagamento em utilização por uma subscrição MOSP" :::
1. Ao selecionar a ligação de ação corretiva, será redirecionado para a página do Azure na qual tem de realizar uma ação. Realize a ação de correção necessária.
1. Se necessário, conclua todas as outras ações corretivas.
1. Regresse a **Gestão de Custos + Faturação** > **Perfis de faturação** > **Métodos de pagamento** e elimine o método de pagamento.

> [!NOTE]
> Depois de cancelar uma subscrição, podem demorar até 90 dias para a subscrição ser eliminada. Se pretender que este tempo de espera seja reduzido, abra um pedido de suporte do Azure e peça que a subscrição seja eliminada imediatamente.

## <a name="next-steps"></a>Passos seguintes

- Se precisar de mais informações sobre cancelar a sua subscrição do Azure, consulte [Cancelar a subscrição do Azure](cancel-azure-subscription.md).
- Para mais informações sobre adicionar ou atualizar um cartão de crédito, consulte [Adicionar ou atualizar um cartão de crédito para o Azure](change-credit-card.md).