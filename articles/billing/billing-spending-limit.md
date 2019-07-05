---
title: Limite de gastos do Azure | Documentos da Microsoft
description: Este artigo descreve como um limite de gastos do Azure funciona e como removê-lo.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490600"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos no Azure impede que os gastos ao longo do seu montante de crédito. Todos os clientes novos que se inscrevam para uma versão de avaliação do Azure ou ofertas que incluem créditos ao longo de vários meses tem o limite de gastos ativado por predefinição. O limite de gastos é US $0. Não pode ser alterado. O limite de gastos não está disponível para subscrições em alguns planos esses planos com preços pay as you go e planos de alocação. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atingir um limite de gastos

Quando os resultados de utilização em custos de esgotar os montantes mensais incluídos na sua subscrição do Azure, os serviços que implementou estão desativados para o resto nesse período de faturação.

Por exemplo, quando gastar todo o crédito incluído na sua subscrição, recursos do Azure que implementou são removidos de produção e máquinas virtuais do Azure são paradas e desalocar. Os dados nas suas contas de armazenamento estão disponíveis como só de leitura.

No início do período de faturação seguinte, se a sua oferta de subscrição incluem créditos ao longo de vários meses, a sua subscrição é novamente ativada automaticamente. Em seguida, pode voltar a implementar os recursos do Azure e ter acesso total às contas de armazenamento e bases de dados.

O Azure envia o e-mail notificações quando atingir os gastos limitam-se para a sua subscrição. Início de sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions) para ver as notificações sobre subscrições que atingiram o limite de gastos.

Se tiver uma subscrição de avaliação gratuita e atingir o limite de gastos, pode atualizar para um plano com [pay as you go](billing-upgrade-azure-subscription.md) preços para remover os gastos limitar e ativar automaticamente a subscrição.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de contas

Pode remover o limite de gastos em qualquer altura, desde que existe um método de pagamento válido associado à sua subscrição do Azure. Para ofertas que utilizam crédito ao longo de vários meses, também pode ativar o limite de gastos no início do período de faturação seguinte.

Para remover o seu limite de gastos, siga estes passos:

1. Início de sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma subscrição. Se a subscrição está desativada devido a que está a ser atingido o limite de gastos, clique na notificação: **Subscrição atingiu o limite de gastos e foi desativada para evitar gastos.** Caso contrário, clique em **remover limite de gastos** no **estado da subscrição** área.
1. Selecione uma opção que seja apropriada para si.

![Selecionar uma opção para remover limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opção | Efeito |
| --- | --- |
| Remover o limite de gastos indefinidamente | Remove o limite de gastos sem que se volte a ativar automaticamente no início do próximo período de cobrança. |
| Remover o limite de gastos para o período de faturação atual | Remove o limite de gastos para que se volte a ativar automaticamente no início do próximo período de cobrança. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Por que talvez queira remover o limite de gastos

O limite de gastos pode evitar que implemente ou utilize determinados de terceiros e os serviços da Microsoft. Aqui estão as situações em que deve remover o limite de gastos na sua subscrição.

-  Planeja implantar imagens de serviços primários como Oracle e serviços como os serviços de DevOps do Azure. Esta situação faz com que a que exceda o limite de gastos quase imediatamente e faz com que a sua subscrição seja desativada.
- Tem serviços que pretende que não sejam interrompidos.
- Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Estas definições serão perdidas quando os serviços e recursos são desalocados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos depois de remover

Esta funcionalidade está disponível apenas quando foi removido o limite de gastos indefinidamente. Altere-o para ser ativada automaticamente no início do período de faturação seguinte.

1. Início de sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolher **ativar o limite no próximo período de cobrança de gastos \<data do período de faturação de início\>**

## <a name="custom-spending-limit"></a>Limite de gastos de personalizado

Limites de gastos personalizadas não estão disponíveis.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Um limite de gastos não impede que todas as despesas

[Alguns serviços externos publicaram no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não pode ser utilizado com os créditos de subscrição e pode custos separados, mesmo quando o limite de gastos está definido. Os exemplos incluem licenças do Visual Studio, Azure Active Directory premium, planos de suporte e a maioria dos terceiros dos serviços. Quando aprovisiona um novo serviço externo, é apresentado um aviso para informá-lo que os serviços são cobrados separadamente:

![Aviso de compra do Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes
- Atualize para um plano com [pay as you go](billing-upgrade-azure-subscription.md) preços.
