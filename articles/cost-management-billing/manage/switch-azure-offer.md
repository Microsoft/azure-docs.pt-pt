---
title: Alterar a oferta da subscrição do Azure
description: Saiba como alterar a sua subscrição Azure e mude para uma oferta diferente.
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: banders
ms.openlocfilehash: dd8040effc5972d86e620793e437f5b185e12603
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685468"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Alterar a subscrição do Azure para outra oferta

Como cliente com uma [subscrição pay-as-you-go,](https://azure.microsoft.com/offers/ms-azr-0003p/) pode mudar a sua subscrição Azure para outra oferta no portal Azure. Por exemplo, pode utilizar esta funcionalidade para tirar partido dos [créditos mensais dos subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Quer apenas atualizar a Avaliação Gratuita?** Veja [Atualizar a subscrição](upgrade-azure-subscription.md).

## <a name="whats-supported"></a>O que é suportado:

Pode mudar de uma subscrição pay-as-you-go para:

- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Para outras alterações na oferta, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="switch-subscription-offer"></a>Mudar oferta de subscrição

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para **Subscrições** e, em seguida, selecione a sua subscrição pay-as-you-go.
1. No topo da página, selecione **Switch Offer**. A opção só está disponível se tiver uma subscrição pay-as-you-go e tiver concluído o seu primeiro período de faturação.  
    :::image type="content" source="./media/switch-azure-offer/switch-offer.png" alt-text="ALTImage mostrando detalhes de subscrição com a opção Switch OfferTEXT" lightbox="./media/switch-azure-offer/switch-offer.png" :::
1. Selecione a oferta que deseja na lista de ofertas para as quais a sua subscrição pode ser comutada. Esta lista varia de acordo com as associações da sua conta. Se não aparecer nada, veja a [lista de ofertas disponíveis para as quais pode mudar](#whats-supported) e confirme que tem as associações adequadas. Em seguida, selecione **Seguinte**.
    :::image type="content" source="./media/switch-azure-offer/select-offer.png" alt-text="Selecionar a oferta para a qual quer mudar" lightbox="./media/switch-azure-offer/select-offer.png" :::
    Consoante a oferta para qual está a mudar, pode ver uma nota sobre o efeito da mudança. Leia cuidadosamente a lista e siga as instruções antes de continuar. Também pode ter de verificar o seu número de telefone.
1. Depois de rever quaisquer notas ou verificar o seu número de telefone, selecione **Switch Offer**.
1. A subscrição foi mudada para a nova oferta.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
As secções a seguir respondem às perguntas mais frequentes.

### <a name="what-is-an-azure-offer"></a>O que é uma oferta do Azure?

Uma oferta do Azure é o *tipo* da subscrição do Azure que possui. Por exemplo, [uma subscrição com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure no Open](https://azure.microsoft.com/offers/ms-azr-0111p/) e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são todas ofertas do Azure. Cada oferta tem [termos](https://azure.microsoft.com/support/legal/offer-details/) diferentes e algumas têm benefícios especiais. A oferta da sua subscrição é mostrada na página de detalhes da subscrição.

:::image type="content" source="./media/switch-azure-offer/subscription-details.png" alt-text="Página de detalhes da subscrição mostrando o tipo de oferta" lightbox="./media/switch-azure-offer/subscription-details.png" :::

### <a name="why-dont-i-see-the-button"></a>Por que não vejo o botão?

Pode não ver a opção **Oferta switch** se:

* Não tenha uma [subscrição com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/). Atualmente, só as subscrições pay as you go podem ser convertidas noutra oferta.
  * Se tiver uma [Avaliação Gratuita](https://azure.microsoft.com/free/), saiba como [atualizar para Pay As You Go](upgrade-azure-subscription.md).
  * Para mudar de oferta noutra subscrição, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Ainda esteja no primeiro período de faturação; para poder mudar de oferta, tem de aguardar pelo final do primeiro período de faturação.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Por que vejo “Não existe nenhuma oferta disponível na sua região ou país no momento”?

* Talvez não esteja qualificado para uma mudança de oferta. Verifique a [lista de ofertas disponíveis para as quais pode mudar](#whats-supported) e confirme que ativou os benefícios corretos com o Visual Studio ou o BizSpark.
* Algumas ofertas podem não estar disponíveis em todos os países/regiões.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que é afetado no serviço e na faturação com a mudança nas ofertas do Azure?

Aqui estão os detalhes do que acontece quando troca as ofertas do Azure.

#### <a name="no-service-downtime"></a>Nenhum tempo de inatividade do serviço

Não há nenhum tempo de inatividade do serviço para nenhum utilizador associado à subscrição. No entanto, a oferta para a qual muda pode ter restrições. Por exemplo, algumas ofertas proíbem a utilização em produção, como tal, precisaria de mover os recursos de produção para outra subscrição.

#### <a name="quota-increases-are-reset"></a>Os aumentos de quota são repostos

Quando muda de oferta, são repostos todos os [aumentos de limites ou quotas acima do limite predefinido](../../azure-portal/supportability/resource-manager-core-quotas-request.md). Não há nenhum tempo de inatividade do serviço, mesmo que tenha mais recursos do que o limite predefinido. Por exemplo, está a utilizar 200 núcleos na subscrição, a mudança de oferta repõe a quota dos núcleos de volta para o valor predefinido de 20 núcleos. As VMs que utilizam os 200 núcleos não são afetadas e continuarão a ser executadas. No entanto, se não fizer outro pedido de aumento de quota, não poderá aprovisionar mais nenhum núcleo.

#### <a name="billing"></a>Faturação

No dia em que mudar, uma fatura será gerada para todos os custos pendentes. Em seguida, a subscrição é faturada de acordo com os termos de preço da nova oferta. As datas de faturação da subscrição são alteradas para a data em que mudou as ofertas. Os dados de utilização e de faturação antes da mudança da oferta não são mantidos, por isso, recomendamos que transfira uma cópia antes de mudar.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Posso migrar de uma subscrição com taxas pay as you go para o Fornecedor de Soluções Cloud (CSP) ou para o Contrato Enterprise (EA)?

* Para migrar para o CSP, veja [Transfer Azure subscriptions between subscribers and CSPs](transfer-subscriptions-subscribers-csp.md) (Transferir subscrições do Azure entre subscritores e CSPs).
* Para migrar para o EA, peça ao Administrador de Inscrição para adicionar a sua conta ao EA. Siga as instruções no e-mail de convite para que as suas assinaturas se desametam sob a inscrição da EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Posso migrar dados e serviços para uma nova subscrição?

* Para migrar os recursos diretamente para a nova subscrição, veja [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Para transferir a propriedade de uma subscrição do Azure e todo o conteúdo para outra pessoa, veja [Transferir a propriedade de uma subscrição do Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
