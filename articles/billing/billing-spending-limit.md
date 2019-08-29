---
title: Limite de gastos do Azure | Microsoft Docs
description: Este artigo descreve como funciona um limite de gastos do Azure e como removê-lo.
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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114440"
---
# <a name="azure-spending-limit"></a>Limite de gastos do Azure

O limite de gastos no Azure evita gastar em seu valor de crédito. Todos os novos clientes que se inscreverem para uma avaliação do Azure ou ofertas que incluem créditos em vários meses têm o limite de gastos ativado por padrão. O limite de gastos é de $0 e não pode ser alterado. Por exemplo, você não pode alterar o limite de gastos para $100. No entanto, você pode remover o limite de gastos. Portanto, você não tem limite ou tem um limite de zero que impede a maioria dos tipos de gastos. O limite de gastos não está disponível para assinaturas em alguns planos que contenham planos de compromisso e planos com preços pagos conforme o uso. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Atingir um limite de gastos

Quando o uso resulta em encargos que esgotam os valores mensais incluídos na sua assinatura do Azure, os serviços implantados são desabilitados para o restante desse período de cobrança.

Por exemplo, quando você passa todo o crédito incluído com sua assinatura, os recursos do Azure implantados são removidos da produção e as máquinas virtuais do Azure são interrompidas e desalocadas. Os dados em suas contas de armazenamento estão disponíveis como somente leitura.

No início do próximo período de cobrança, se sua oferta de assinatura incluir créditos em vários meses, sua assinatura será reabilitada automaticamente. Em seguida, você pode reimplantar os recursos do Azure e ter acesso completo às suas contas de armazenamento e bancos de dados.

O Azure envia notificações por email quando você atinge o limite de gastos para sua assinatura. Entre no centro de [contas](https://account.windowsazure.com/Subscriptions) para ver as notificações sobre assinaturas que atingiram o limite de gastos.

Se você tiver uma assinatura de avaliação gratuita e alcançar o limite de gastos, poderá atualizar para um plano com preço [pago conforme o uso](billing-upgrade-azure-subscription.md) para remover o limite de gastos e habilitar automaticamente a assinatura.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no centro de contas

Você pode remover o limite de gastos a qualquer momento, contanto que haja um método de pagamento válido associado à sua assinatura do Azure. Para ofertas que têm crédito em vários meses, você também pode habilitar o limite de gastos no início do próximo período de cobrança.

Para remover o seu limite de gastos, siga estes passos:

1. Entre no centro de [contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma subscrição. Se a assinatura estiver desabilitada porque o limite de gastos foi atingido, clique na notificação: **A assinatura atingiu o limite de gastos e foi desabilitada para evitar encargos.** Caso contrário, clique em **remover limite de gastos** na área **status da assinatura** .
1. Selecione uma opção que seja apropriada para si.

![Selecionando uma opção para remover o limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

| Opção | Efeito |
| --- | --- |
| Remover o limite de gastos indefinidamente | Remove o limite de gastos sem que se volte a ativar automaticamente no início do próximo período de cobrança. |
| Remover o limite de gastos para o período de faturação atual | Remove o limite de gastos para que se volte a ativar automaticamente no início do próximo período de cobrança. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Por que você talvez queira remover o limite de gastos

O limite de gastos pode impedi-lo de implantar ou usar determinados serviços de terceiros e da Microsoft. Aqui estão as situações em que você deve remover o limite de gastos em sua assinatura.

-  Você planeja implantar imagens de terceiros, como Oracle e serviços, como Azure DevOps Services. Essa situação faz com que você exceda o limite de gastos quase imediatamente e faz com que sua assinatura seja desabilitada.
- Você tem serviços que não deseja que sejam interrompidos.
- Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Essas configurações são perdidas quando os serviços e os recursos são desalocados.

## <a name="turn-on-the-spending-limit-after-removing"></a>Ativar o limite de gastos após a remoção

Esse recurso está disponível somente quando o limite de gastos foi removido indefinidamente. Altere-o para ativar automaticamente no início do próximo período de cobrança.

1. Entre no centro de [contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolha **Ativar limite de gastos na próxima data de início \<do período de cobrança do\> período de cobrança**

## <a name="custom-spending-limit"></a>Limite de gastos personalizado

Os limites de gastos personalizados não estão disponíveis.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Um limite de gastos não impede todos os encargos

[Alguns serviços externos publicados no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não podem ser usados com seus créditos de assinatura e podem incorrer em encargos separados mesmo quando o limite de gastos é definido. Os exemplos incluem licenças do Visual Studio, Azure Active Directory Premium, planos de suporte e a maioria dos serviços de marca de terceiros. Quando você provisiona um novo serviço externo, um aviso é mostrado para permitir que você saiba que os serviços são cobrados separadamente:

![Aviso de compra do Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes
- Atualize para um plano com o preço [pago conforme o uso](billing-upgrade-azure-subscription.md) .
