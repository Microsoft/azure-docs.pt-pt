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
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114440"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos no Azure impede que gaste mais do que o montante do seu crédito. Todos os novos clientes que se inscrevam para uma avaliação gratuita do Azure ou ofertas que incluam créditos ao longo de vários meses terão o limite de gastos ativado por predefinição. O limite de gastos é de 0 $ e não pode ser alterado. Por exemplo, não pode alterar o limite de gastos para 100 $. No entanto, pode remover o limite de gastos. Desta fora, deixa de ter um limite ou tem um limite igual a zero que o impede de fazer a maioria dos tipos de gastos. O limite de gastos não está disponível para as subscrições em alguns planos, como os planos de alocação e os planos com preços pay as you go. Veja a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atingir um limite de gastos

Quando a utilização resulta em encargos que esgotam os montantes mensais incluídos com a subscrição do Azure, os serviços que implementou são desativados para o resto desse período de faturação.

Por exemplo, quando gasta todo o crédito incluído com a subscrição, os recursos do Azure que implementou são removidos da produção e as máquinas virtuais do Azure são paradas e desalocadas. Os dados nas contas de armazenamento estão disponíveis como só de leitura.

No início do próximo período de faturação, se a oferta de subscrição incluir créditos ao longo de vários meses, a subscrição será reativada automaticamente. Em seguida, poderá reimplementar os recursos do Azure e ter acesso total às contas de armazenamento e às bases de dados.

O Azure envia notificações por e-mail quando atinge o limite de gastos da subscrição. Inicie sessão no [Centro de Contas](https://account.windowsazure.com/Subscriptions) para ver as notificações sobre as subscrições que atingiram o limite de gastos.

Se tiver uma subscrição de avaliação gratuita e atingir o limite de gastos, poderá atualizar para um plano com preços [pay as you go](billing-upgrade-azure-subscription.md) para remover o limite de gastos e ativar automaticamente a subscrição.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de Contas

Pode remover o limite de gastos a qualquer momento, desde que exista um método de pagamento válido associado à subscrição do Azure. Para as ofertas que utilizam crédito ao longo de vários meses, também pode ativar o limite de gastos no início do próximo período de faturação.

Para remover o limite de gastos, siga estes passos:

1. Inicie sessão no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma subscrição. Se a subscrição estiver desativada, porque foi atingido o limite de gastos, clique na notificação: **A subscrição atingiu o Limite de Gastos e foi desativada para evitar gastos.** Se não for esse o caso, clique em **Remover limite de gastos** na área **ESTADO DA SUBSCRIÇÃO**.
1. Selecione uma opção que seja apropriada para si.

![Selecionar uma opção para remover o limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opção | Efeito |
| --- | --- |
| Remover o limite de gastos indefinidamente | Remove o limite de gastos sem que se volte a ativar automaticamente no início do próximo período de faturação. |
| Remover o limite de gastos para o período de faturação atual | Remove o limite de gastos para que se volte a ativar automaticamente no início do próximo período de faturação. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Qual o interesse de remover o limite de gastos?

O limite de gastos pode evitar que implemente ou utilize determinados serviços da Microsoft e de terceiros. Veja a seguir as situações em que deve remover o limite de gastos da subscrição.

-  Planeia implementar imagens originais como o Oracle e serviços como os Serviços DevOps do Azure. Esta situação faz com que exceda o limite de gastos quase imediatamente e faz com que a subscrição seja desativada.
- Tem serviços que não quer que sejam interrompidos.
- Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Estas definições serão perdidas quando os serviços e recursos forem desalojados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos após a remoção

Esta funcionalidade está disponível apenas quando o limite de gastos tiver sido removido indefinidamente. Altere esta funcionalidade para que seja ativada automaticamente no início do próximo período de faturação.

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
