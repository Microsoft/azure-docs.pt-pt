---
title: Limite de gastos do Azure | Microsoft Docs
description: Este artigo descreve como funciona um limite de gastos do Azure e como o pode remover.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 97e6e04bf7fab3a48f1ce0552e1b653b208be77b
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375438"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos no Azure impede que gaste mais do que o montante do seu crédito. Todos os novos clientes que se inscrevam numa conta gratuita do Azure ou em tipos de subscrição que incluam créditos ao longo de vários meses terão o limite de gastos ativado por predefinição. O limite de gastos é igual à quantidade de crédito e não pode ser alterado. Por exemplo, se se tiver inscrito na conta gratuita do Azure, o seu limite de gastos será de 200 $ e não poderá ser alterado para 500 $. No entanto, pode remover o limite de gastos. Desta forma, deixa de ter um limite ou tem um limite igual à quantidade de crédito. Tal impede que faça a maioria dos tipos de gastos. O limite de gastos não está disponível para as subscrições com planos de alocação ou planos com preços pay as you go. Veja a [lista completa de tipos de subscrição do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atingir um limite de gastos

Quando a utilização resulta em encargos que esgotam o limite de gastos, os serviços que implementou são desativados para o resto desse período de faturação.

Por exemplo, quando gasta todo o crédito incluído com a sua conta gratuita do Azure, os recursos do Azure que implementou são removidos da produção e as máquinas virtuais do Azure são paradas e desalocadas. Os dados nas contas de armazenamento estão disponíveis como só de leitura.

Se o seu tipo de subscrição incluir créditos ao longo de vários meses, a subscrição será reativada automaticamente no início do próximo período de faturação. Em seguida, poderá reimplementar os recursos do Azure e ter acesso total às contas de armazenamento e às bases de dados.

O Azure envia notificações por e-mail quando atinge o limite de gastos. Inicie sessão no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) para ver as notificações sobre as subscrições que atingiram o limite de gastos.

Se se tiver inscrito numa conta gratuita do Azure e atingir o limite de gastos, poderá atualizar para os preços [pay as you go](billing-upgrade-azure-subscription.md) para remover o limite de gastos e reativar automaticamente a subscrição.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>Remover o limite de gastos no portal do Azure

Pode remover o limite de gastos a qualquer momento, desde que exista um método de pagamento válido associado à subscrição do Azure. Para os tipos de subscrição que utilizam crédito ao longo de vários meses, como o Visual Studio Enterprise e o Visual Studio Professional, também pode ativar o limite de gastos no início do próximo período de faturação.

Para remover o limite de gastos, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione uma subscrição. Se o limite de gastos da subscrição for atingido, esta será desativada.
1. Selecione **Remover limite de gastos** na parte superior da página.
1. Selecione uma opção que seja apropriada para si.

![Selecionar uma opção para remover o limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opção | Efeito |
| --- | --- |
| Remover o limite de gastos indefinidamente | Remove o limite de gastos sem que se volte a ativar automaticamente no início do próximo período de faturação. |
| Remover o limite de gastos para o período de faturação atual | Remove o limite de gastos para que se volte a ativar automaticamente no início do próximo período de faturação. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Qual o interesse de remover o limite de gastos?

O limite de gastos pode evitar que implemente ou utilize determinados serviços da Microsoft e de terceiros. Veja a seguir as situações em que deve remover o limite de gastos da subscrição.

-  Planeia implementar imagens originais como o Oracle ou serviços como os Serviços DevOps do Azure. Esta situação faz com que atinja o limite de gastos quase imediatamente e faz com que a subscrição seja desativada.
- Tem serviços que não quer que sejam interrompidos. Quando atingir o limite de gastos, os recursos do Azure que implementou são removidos de produção e as máquinas virtuais do Azure são paradas e desalocadas. Se tiver serviços que não quer que sejam interrompidos, terá de remover o limite de gastos.
- Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Estas definições serão perdidas quando atingir o limite de gastos e os serviços e recursos forem desalocados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos após a remoção

Esta funcionalidade está disponível apenas quando o limite de gastos tiver sido removido indefinidamente para tipos de subscrição que incluem créditos ao longo de vários meses. Pode utilizar esta funcionalidade para ativar o limite de gastos automaticamente no início do próximo período de faturação.

1. Inicie sessão no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolha **Ativar limite de gastos no próximo período de faturação\<data de início do período de faturação\>**

## <a name="custom-spending-limit"></a>Limite de gastos personalizado

Os limites de gastos personalizados não estão disponíveis.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>O limite de gastos não impede todos os encargos

[Alguns serviços externos publicados no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não podem ser utilizados com os créditos da subscrição e podem incorrer em encargos separados mesmo quando o limite de gastos está definido. Os exemplos incluem as licenças do Visual Studio, Azure Active Directory Premium, planos de suporte e a maior parte dos serviços de terceiros. Quando aprovisiona um novo serviço externo, é apresentado um aviso a indicar que os serviços são faturados separadamente:

![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Atualizar para um plano com preços [pay as you go](billing-upgrade-azure-subscription.md).
