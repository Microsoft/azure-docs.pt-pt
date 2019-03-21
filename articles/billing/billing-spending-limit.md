---
title: Compreender o limite de gastos do Azure | Documentos da Microsoft
description: Descreve como funciona o limite de gastos do Azure e como removê-lo
services: ''
documentationcenter: ''
author: genlin
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: c234f75ec752ec79e9f2a051f79e91bdde81de15
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845377"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Compreender o limite e como removê-lo de gastos do Azure

Limite no Azure de gastos existe para evitar gastos ao longo do seu montante de crédito. Todos os clientes novos que se inscrevam para a avaliação ou ofertas que incluem créditos ao longo de vários meses tem o limite de gastos ativado por predefinição. O limite de gastos é US $0. Não pode ser alterado. O limite de gastos não está disponível para tipos de subscrição como subscrições Pay As You Go e planos de compromisso. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>O que acontece quando atinjo o limite de gastos?

Quando os resultados de utilização em custos de esgotar os montantes mensais incluídos na sua subscrição, os serviços que implementou estão desativados para o resto nesse período de faturação. 

Por exemplo, quando gastar todo o crédito incluído na sua subscrição, serviços Cloud que implementou são removidos de produção e máquinas virtuais do Azure são paradas e desalocar. Os dados nas suas contas de armazenamento estão disponíveis de modo só de leitura.

No início do período de faturação seguinte, se a sua oferta de subscrição incluem créditos ao longo de vários meses, a sua subscrição deve ser novamente ativada automaticamente. Em seguida, pode voltar a implementar os serviços Cloud e ter acesso total às contas de armazenamento e bases de dados.

Iremos enviar notificações por e-mail quando atingir o limite de gastos da sua subscrição. Inicie sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions), e verá notificações sobre subscrições que atingiram o limite de gastos.

Se tiver uma avaliação gratuita e atingir o limite de gastos, pode [Atualize para pay as you go](billing-upgrade-azure-subscription.md) para remover o limite de gastos e a subscrição automaticamente ativado novamente.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de contas

Pode remover o limite de gastos a qualquer momento, desde que exista um método de pagamento válido associado à sua subscrição. Para ofertas que utilizam crédito ao longo de vários meses, também pode reativar o limite de gastos no início do período de faturação seguinte.

Para remover o seu limite de gastos, siga estes passos:

1. Inicie sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma subscrição. Se a subscrição estiver desativada, porque foi atingido o limite de gastos, clique na notificação: "A subscrição atingiu o Limite de Gastos e foi desativada para evitar gastos". Caso contrário, clique em **remover limite de gastos** no **estado da subscrição** área.
1. Selecione uma opção que seja apropriada para si.

![Selecionar uma opção para remover limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opção|Efeito|
|-------|-----|
|Remover o limite de gastos indefinidamente|Remove o limite de gastos sem que se volte a ativar automaticamente no início do próximo período de cobrança.|
|Remover o limite de gastos para o período de faturação atual|Remove o limite de gastos para que se volte a ativar automaticamente no início do próximo período de cobrança.|

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Por que motivo devo remover o limite de gastos?

O limite de gastos pode evitar que implemente ou utilize determinados de terceiros e os serviços da Microsoft. Estes são os cenários em que deve remover o limite de gastos da sua subscrição.

* Planeja implantar imagens de serviços primários como Oracle e serviços como os serviços de DevOps do Azure. Este cenário causa que exceda o limite de gastos quase imediatamente e faz com que a sua subscrição seja desativada.
* Tem serviços que não podem ser interrompidos.
* Tem serviços e recursos com definições como endereços IP virtuais que não quer perder. Estas definições serão perdidas quando os serviços e recursos são desalocados.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Como ativar o limite de gastos depois de ser removido

Esta funcionalidade está disponível apenas quando foi removido o limite de gastos indefinidamente. Altere-o para ser ativada automaticamente no início do período de faturação seguinte.

1. Inicie sessão para o [Centro de contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolher **ativar o limite no próximo período de cobrança de gastos \<data do período de faturação de início\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Como posso definir um limite de gastos personalizado?

Limites de gastos personalizadas não estão disponíveis.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>O limite de gastos impedir que todos os encargos do Azure?

[Alguns serviços externos publicaram no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não pode ser utilizado com os créditos de subscrição e pode custos separados, mesmo quando o limite de gastos está definido. Os exemplos incluem licenças do Visual Studio, Azure Active Directory premium, planos de suporte e a maioria dos terceiros dos serviços. Quando aprovisiona um novo serviço externo, é apresentado um aviso para informá-lo que os serviços são cobrados separadamente:

![Aviso de compra do Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
