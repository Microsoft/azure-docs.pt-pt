---
title: Alterar a oferta da subscrição do Azure
description: Saiba como alterar a subscrição do Azure e mudar para uma oferta diferente com o Centro de Contas do Azure.
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: e382b4bf12b53573f3506474837093456f9b84c6
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709638"
---
# <a name="change-your-azure-subscription-to-a-different-offer"></a>Alterar a subscrição do Azure para outra oferta

Como cliente com uma [subscrição individual com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/), poderá mudar a subscrição do Azure para outra oferta no [Centro de Contas](https://account.windowsazure.com/Subscriptions). Por exemplo, pode utilizar esta funcionalidade para tirar partido dos [créditos mensais dos subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

**Quer apenas atualizar a Avaliação Gratuita?** Veja [Atualizar a subscrição](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>O que é suportado:

Pode alterar de uma subscrição individual com taxas pay as you go para:

- [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/)

> [!NOTE]
> Para outras alterações na oferta, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Mudar oferta de subscrição

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Inicie sessão no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions).
1. Selecione a sua subscrição individual com taxas pay as you go.
1. Clique em **Mudar para outra oferta**. A opção estará disponível apenas se tiver uma subscrição individual com taxas pay as you go e tiver concluído o primeiro período de faturação.

   ![Repare no botão Mudar oferta no lado direito da página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Selecione a oferta pretendida** na lista de ofertas para as quais a sua subscrição pode ser mudada. Esta lista varia de acordo com as associações da sua conta. Se não aparecer nada, veja a [lista de ofertas disponíveis para as quais pode mudar](#whats-supported) e confirme que tem as associações adequadas.

   ![Selecionar a oferta para a qual quer mudar](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Consoante a oferta para qual está a mudar, pode ver uma nota sobre o efeito da mudança. Leia cuidadosamente esta lista e siga as instruções antes de avançar.

   ![Rever as notas](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Pode mudar o nome da subscrição. Por predefinição, está definido como o novo nome da oferta. Clique em **Mudar Oferta** para concluir o processo.

   ![Clicar no botão verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Êxito! A subscrição foi mudada para a nova oferta.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
As secções a seguir respondem às perguntas mais frequentes.

### <a name="what-is-an-azure-offer"></a>O que é uma oferta do Azure?

Uma oferta do Azure é o *tipo* da subscrição do Azure que possui. Por exemplo, [uma subscrição com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure no Open](https://azure.microsoft.com/offers/ms-azr-0111p/) e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são todas ofertas do Azure. Cada oferta tem [termos](https://azure.microsoft.com/support/legal/offer-details/) diferentes e algumas têm benefícios especiais. A oferta da sua subscrição pode ser encontrada na página de subscrição do Centro de Contas. Clique no nome da oferta para obter mais detalhes.

   ![Clicar na ligação Oferta no Centro de Contas para obter mais detalhes](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Por que não vejo o botão?

Pode não ver a opção **Mudar para outra oferta** caso:

* Não tenha uma [subscrição com taxas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/). Atualmente, só as subscrições pay as you go podem ser convertidas noutra oferta.
  * Se tiver uma [Avaliação Gratuita](https://azure.microsoft.com/free/), saiba como [atualizar para Pay As You Go](billing-upgrade-azure-subscription.md).
  * Para mudar de oferta noutra subscrição, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Ainda esteja no primeiro período de faturação; para poder mudar de oferta, tem de aguardar pelo final do primeiro período de faturação.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Por que vejo “Não existe nenhuma oferta disponível na sua região ou país no momento”?

* Talvez não esteja qualificado para uma mudança de oferta. Verifique a [lista de ofertas disponíveis para as quais pode mudar](#whats-supported) e confirme que ativou os benefícios corretos com o Visual Studio ou o BizSpark.
* Algumas ofertas podem não estar disponíveis em todos os países/regiões.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que é afetado no serviço e na faturação com a mudança nas ofertas do Azure?

Seguem-se os detalhes do que acontece quando muda as ofertas do Azure no Centro de Contas.

#### <a name="no-service-downtime"></a>Nenhum tempo de inatividade do serviço

Não há nenhum tempo de inatividade do serviço para nenhum utilizador associado à subscrição. No entanto, a oferta para a qual muda pode ter restrições. Por exemplo, algumas ofertas proíbem a utilização em produção, como tal, precisaria de mover os recursos de produção para outra subscrição.

#### <a name="quota-increases-are-reset"></a>Os aumentos de quota são repostos

Quando muda de oferta, são repostos todos os [aumentos de limites ou quotas acima do limite predefinido](../azure-supportability/resource-manager-core-quotas-request.md). Não há nenhum tempo de inatividade do serviço, mesmo que tenha mais recursos do que o limite predefinido. Por exemplo, está a utilizar 200 núcleos na subscrição, a mudança de oferta repõe a quota dos núcleos de volta para o valor predefinido de 20 núcleos. As VMs que utilizam os 200 núcleos não são afetadas e continuarão a ser executadas. No entanto, se não fizer outro pedido de aumento de quota, não poderá aprovisionar mais nenhum núcleo.

#### <a name="billing"></a>Faturação

No dia em que mudar, uma fatura será gerada para todos os custos pendentes. Em seguida, a subscrição é faturada de acordo com os termos de preço da nova oferta. As datas de faturação da subscrição são alteradas para a data em que mudou as ofertas. Os dados de utilização e de faturação antes da mudança da oferta não são mantidos, por isso, recomendamos que transfira uma cópia antes de mudar.

### <a name="can-i-migrate-from-a-subscription-with-pay-as-you-go-rates-to-cloud-solution-provider-csp-or-enterprise-agreement-ea"></a>Posso migrar de uma subscrição com taxas pay as you go para o Fornecedor de Soluções Cloud (CSP) ou para o Contrato Enterprise (EA)?

* Para migrar para o CSP, veja [Azure Pay-As-You-Go Subscription Migration to CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp) (Migração da Subscrição Pay As You Go do Azure para o CSP).
* Para migrar para o EA, peça ao Administrador de Inscrição para adicionar a sua conta ao EA. Siga as instruções no e-mail de convite para que as suas subscrições sejam movidas ao abrigo da inscrição de EA. Para saber mais, veja [Associar uma Conta Existente](https://ea.azure.com/helpdocs/associateExistingAccount) no EA Portal.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Posso migrar dados e serviços para uma nova subscrição?

* Para migrar os recursos diretamente para a nova subscrição, veja [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).
* Para transferir a propriedade de uma subscrição do Azure e todo o conteúdo para outra pessoa, veja [Transferir a propriedade de uma subscrição do Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Começar a analisar os custos](../cost-management/quick-acm-cost-analysis.md)
