---
title: Gerir anomalias de faturação medidos no Partner Center | Mercado Azure
description: Saiba como a deteção automática de anomalias para faturação medido ajuda a garantir que os seus clientes são faturados corretamente para o uso medido das suas ofertas de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 2/18/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 1ff6262a75261c575082f3fc48d588c868ad0b51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092529"
---
# <a name="manage-metered-billing-anomalies-in-partner-center"></a>Gerir anomalias de faturação medidos no Partner Center

A opção de faturação personalizada está atualmente disponível para [o Software como um serviço](plan-saas-offer.md) (SaaS) oferece e [aplicações Azure](plan-azure-application-offer.md#types-of-plans) com um plano de aplicação gerido.

Se estiver a utilizar a opção de faturação medido para criar ofertas no programa de marketplace comercial que lhe permite cobrar pelo uso com base em unidades não standard, precisa de saber quando o seu cliente utilizou mais um serviço do que o esperado.

## <a name="use-the-anomaly-detection-feature"></a>Utilize a função de deteção de anomalias

A Microsoft conta consigo, o parceiro, para comunicar o uso excessivo dos seus clientes das suas ofertas de aplicações SaaS ou Azure Managed antes da Microsoft faturar o cliente. Se o uso errado for reportado, o cliente pode potencialmente receber uma fatura incorreta, minando a credibilidade da Microsoft e do parceiro.

Para ajudar a garantir que os seus clientes são faturados corretamente, utilize a funcionalidade **de deteção de Anomalias** tanto para apps SaaS como para planos de aplicações geridos pela Azure. Esta funcionalidade monitoriza o uso contra a faturação medido e prevê o valor esperado de utilização dentro do intervalo esperado. Se a utilização estiver fora do alcance esperado, é tratada como inesperada, (uma anomalia,) e receberá uma notificação de alerta na sua página de Oferta Geral no programa de marketplace comercial do Partner Center. Pode acompanhar diariamente o uso dos seus clientes para cada dimensão personalizada dos contadores que definiu.

## <a name="view-and-manage-metered-usage-anomalies"></a>Ver e gerir anomalias de utilização medidos

1. Iniciar s-in no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Analyze**.
1. Selecione o **separador anomalias de utilização medido.**

    [![Ilustra o separador de anomalias de utilização medido na página Utilização.](./media/anomaly-detection/metered-usage-anomalies.png)](./media/anomaly-detection/metered-usage-anomalies.png#lightbox)
    ***Figura 1: Separador de anomalias de utilização medidos***

1. Para quaisquer anomalias de utilização detetadas contra faturação medido, como editor será solicitado que investigue e confirme se a anomalia é verdadeira ou não. Selecione **Mark como anomalia** para confirmar o diagnóstico.

     [![Ilustra a Marca como caixa de diálogo de anomalia.](./media/anomaly-detection/mark-as-anomaly.png)](./media/anomaly-detection/mark-as-anomaly.png#lightbox)
    ***Figura 2: Marca como caixa de diálogo de anomalia***

1. Se você acredita que a anomalia de utilização por excesso que detetamos não é genuína, você pode fornecer esse feedback selecionando **Não uma anomalia** para o Centro parceiro sinalizou anomalia no uso particular de overage.

    [![Ilustra o porquê de não ser uma caixa de diálogo de anomalia.](./media/anomaly-detection/why-is-it-not-an-anomaly.png)](./media/anomaly-detection/why-is-it-not-an-anomaly.png#lightbox)
    ***Por que não é uma anomalia? caixa de diálogo***

1. Pode deslocar-se pela página para ver uma lista de anomalias não reconhecidas. A lista fornece um inventário de anomalias que não reconheceu. Pode optar por marcar qualquer uma das anomalias assinaladas pelo Centro parceiro como genuínas ou falsas.

   [![Ilustra a lista de anomalias não reconhecidas do Partner Center na página de Utilização.](./media/anomaly-detection/unacknowledged-anomalies.png)](./media/anomaly-detection/unacknowledged-anomalies.png#lightbox)
    ***Figura 4: Lista de anomalias não reconhecidas do Partner Center***

1. Você também veria um registo de ação de anomalia que mostra as ações que você tomou sobre os usos excessivos. No registo de ação, poderá ver quais os eventos de utilização por excesso de tempo marcados como genuínos ou falsos.

   [ ![ Ilustra o registo de ação da Anomalia na página De Utilização.](./media/anomaly-detection/anomaly-action-log.png)](./media/anomaly-detection/anomaly-action-log.png#lightbox) 
    ***Figura 5: Registo de ação de anomalia***

1. A análise do Partner Center não apoiará a reafirmação de eventos de utilização excessivo nos relatórios de exportação. O Partner Center permite-lhe introduzir o uso de overage corrigido para uma anomalia e os detalhes são transmitidos às equipas da Microsoft para investigação. Com base na investigação, a Microsoft emitirá reembolsos de crédito ao cliente sobrecarregado, conforme apropriado. Quando selecionar qualquer uma das anomalias sinalizadas, pode selecionar **Mark como anomalia** para marcar a anomalia de utilização como genuína.

   [ ![ Ilustra a Marca como uma caixa de diálogo de anomalia.](./media/anomaly-detection/new-reported-usage.png)](./media/anomaly-detection/new-reported-usage.png#lightbox) 
    ***Figura: 6: Marca como caixa de diálogo de anomalia***

A primeira vez que um uso excessivo é sinalizado como irregular no Partner Center, você receberá uma janela de 30 dias a partir desse caso para marcar a anomalia como genuína ou falsa. Após o período de 30 dias, como editora, não seria capaz de agir sobre as anomalias.

> [!IMPORTANT]
> As unidades de utilização por excesso de informação sub-declaradas não são elegíveis para reaclarar e ajustar financeiramente.

Pode ver todas as anomalias (reconhecidas ou não) durante o período de cálculo selecionado. Os vários períodos de cálculo são os últimos 30 dias, os últimos 60 dias e os últimos 90 dias.

> [!IMPORTANT]
> É-lhe pedido que aja sobre as anomalias sinalizadas no prazo de 30 dias a contar do momento em que as anomalias são reportadas pela primeira vez no Partner Center.

O filtro modal no widget permite-lhe selecionar ofertas individuais e medidores personalizados individuais.

Depois de marcar um uso excessivo como anomalia ou reconhecer um modelo que assinalou uma anomalia como genuína, não pode mudar a seleção para "Não uma Anomalia".

> [!IMPORTANT]
> Pode reenviar os usos de excesso de idade em caso de sobrecarga.

## <a name="see-also"></a>Ver também
- [Faturação medido para SaaS utilizando o serviço de medição de mercado comercial](./partner-center-portal/saas-metered-billing.md)
- [Faturação gerida com medidos de aplicação](./partner-center-portal/azure-app-metered-billing.md)
- [Serviço de deteção de anomalias da faturação limitada](./partner-center-portal/anomaly-detection-service-for-metered-billing.md)
