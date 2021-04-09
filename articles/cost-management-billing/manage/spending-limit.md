---
title: Limite de gastos do Azure
description: Este artigo descreve como funciona um limite de gastos do Azure e como o pode remover.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 47f1a1ed182420852682e908c7cd8b5e094bc085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932561"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos no Azure impede que gaste mais do que o montante do seu crédito. Todos os novos clientes que se inscrevam numa conta gratuita do Azure ou em tipos de subscrição que incluam créditos ao longo de vários meses terão o limite de gastos ativado por predefinição. O limite de despesas é igual ao montante do crédito. Não podes mudar o limite de gastos. Por exemplo, se se tiver inscrito na conta gratuita do Azure, o seu limite de gastos será de 200 $ e não poderá ser alterado para 500 $. No entanto, pode remover o limite de gastos. Desta forma, deixa de ter um limite ou tem um limite igual à quantidade de crédito. Tal impede que faça a maioria dos tipos de gastos. O limite de gastos não está disponível para as subscrições com planos de alocação ou planos com preços pay as you go. Veja a [lista completa de tipos de subscrição do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atingir um limite de gastos

Quando a utilização resulta em encargos que esgotam o limite de gastos, os serviços que implementou são desativados para o resto desse período de faturação.

Por exemplo, quando gasta todo o crédito incluído com a sua conta gratuita do Azure, os recursos do Azure que implementou são removidos da produção e as máquinas virtuais do Azure são paradas e desalocadas. Os dados nas contas de armazenamento estão disponíveis como só de leitura.

Se o seu tipo de subscrição incluir créditos ao longo de vários meses, a subscrição será reativada automaticamente no início do próximo período de faturação. Em seguida, poderá reimplementar os recursos do Azure e ter acesso total às contas de armazenamento e às bases de dados.

O Azure envia notificações por e-mail quando atinge o limite de gastos. Inicie sessão no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) para ver as notificações sobre as subscrições que atingiram o limite de gastos.

Se se tiver inscrito numa conta gratuita do Azure e atingir o limite de gastos, poderá atualizar para os preços [pay as you go](upgrade-azure-subscription.md) para remover o limite de gastos e reativar automaticamente a subscrição.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Remover o limite de gastos no portal do Azure

Pode remover o limite de gastos a qualquer momento, desde que exista um método de pagamento válido associado à subscrição do Azure. Para os tipos de subscrição que têm crédito ao longo de vários meses, como o Visual Studio Enterprise e o Visual Studio Professional, pode optar por remover o limite de gastos indefinidamente ou apenas para o período de faturação atual. Se escolher apenas o período de faturação atual, o limite de gastos será automaticamente ativado no início do período de faturação seguinte.

Se tiver uma conta gratuita do Azure, veja [Atualizar a sua subscrição do Azure](upgrade-azure-subscription.md) para remover o limite de gastos. Caso contrário, siga estes passos para remover o limite de gastos:

<a id="remove"></a>

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa para Cost Management + Faturação ](./media/spending-limit/search-bar.png)

1. Na lista **As minhas subscrições**, selecione a sua subscrição. Por exemplo, *Visual Studio Enterprise*.

   ![Captura de ecrã que mostra a grelha de As minhas subscrições na descrição geral](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Se não vir algumas das suas subscrições do Visual Studio aqui, tal poderá dever-se ao facto de ter alterado o diretório de uma subscrição em determinado momento. No caso destas subscrições, tem de alterar o diretório para o diretório original (o diretório no qual inicialmente se inscreveu). Em seguida, repita o passo 2.

1. Na descrição geral de Subscrição, clique na faixa laranja para remover o limite de gastos.

    ![Captura de ecrã que mostra a faixa para remover o limite de gastos](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Escolha se pretende remover o limite de gastos indefinidamente ou apenas para o período de faturação atual.

      ![Captura de ecrã que mostra o painel para remover o limite de gastos](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Opção | Efeito |
      | --- | --- |
      | Remover o limite de gastos indefinidamente | O limite de gastos não volta a ativar automaticamente no início do próximo período de faturação. No entanto, pode voltar a ativá-lo manualmente a qualquer momento. |
      | Remover o limite de gastos para o período de faturação atual | O limite de gastos volta a ativar automaticamente no início do próximo período de faturação. |


1. Clique em **Selecionar método de pagamento** para escolher um método de pagamento para a sua subscrição. Este passará a ser o método de pagamento ativo para a sua subscrição.

1. Clique em **Concluir**.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Qual o interesse de remover o limite de gastos?

O limite de gastos pode evitar que implemente ou utilize determinados serviços da Microsoft e de terceiros. Veja a seguir as situações em que deve remover o limite de gastos da subscrição.

-  Planeia implementar imagens de terceiros, como o Oracle, ou serviços como os Serviços DevOps do Azure. Esta situação faz com que atinja o limite de gastos quase imediatamente e faz com que a subscrição seja desativada.
- Tem serviços que não quer que sejam interrompidos. Quando atingir o limite de gastos, os recursos do Azure que implementou são removidos de produção e as máquinas virtuais do Azure são paradas e desalocadas. Se tiver serviços que não quer que sejam interrompidos, terá de remover o limite de gastos.
- Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Estas definições serão perdidas quando atingir o limite de gastos e os serviços e recursos forem desalocados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos após a remoção

Esta funcionalidade está disponível apenas quando o limite de gastos tiver sido removido indefinidamente para tipos de subscrição que incluem créditos ao longo de vários meses. Pode utilizar esta funcionalidade para ativar o limite de gastos automaticamente no início do próximo período de faturação.


1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.
1. Procure **Cost Management + Faturação**.

    ![Captura de ecrã que mostra a pesquisa para Cost Management + Faturação ](./media/spending-limit/search-bar.png)

1. Na lista **As minhas subscrições**, selecione a sua subscrição. Por exemplo, *Visual Studio Enterprise*.

   ![Captura de ecrã que mostra a grelha de As minhas subscrições na descrição geral](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Se não vir algumas das suas subscrições do Visual Studio aqui, tal poderá dever-se ao facto de ter alterado o diretório de uma subscrição em determinado momento. No caso destas subscrições, tem de alterar o diretório para o diretório original (o diretório no qual inicialmente se inscreveu). Em seguida, repita o passo 2.

1. Na descrição geral da Subscrição, clique na faixa na parte superior da página para voltar a ativar o limite de gastos.

## <a name="custom-spending-limit"></a>Limite de gastos personalizado

Os limites de gastos personalizados não estão disponíveis.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>O limite de gastos não impede todos os encargos

[Alguns serviços externos publicados no Azure Marketplace](../understand/understand-azure-marketplace-charges.md) não podem ser utilizados com os créditos da subscrição e podem incorrer em encargos separados mesmo quando o limite de gastos está definido. Os exemplos incluem as licenças do Visual Studio, Azure Active Directory Premium, planos de suporte e a maior parte dos serviços de terceiros. Quando aprovisiona um novo serviço externo, é apresentado um aviso a indicar que os serviços são faturados separadamente:

![Aviso de compra no Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Atualizar para um plano com preços [pay as you go](upgrade-azure-subscription.md).
