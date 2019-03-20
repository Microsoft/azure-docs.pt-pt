---
title: Oferta de subscrição do Azure de alteração | Documentos da Microsoft
description: Saiba mais sobre como alterar a sua subscrição do Azure e mudar para outra oferta com o Centro de contas do Azure
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: bbdcbdc7ef288eeeb279c7e5e59baee492e1f292
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835280"
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>Alterar a sua subscrição pay as you go do Azure para outra oferta

Como um [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) cliente, pode mudar a sua subscrição do Azure para outra oferta no [Centro de contas](https://account.windowsazure.com/Subscriptions). Por exemplo, pode utilizar esta funcionalidade para aproveitar os [créditos mensais para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). 

**Quer apenas atualizar a partir da versão de avaliação gratuita?** Ver [Atualize para pay as you go](billing-upgrade-azure-subscription.md).

## <a name="whats-supported"></a>O que é suportado:

| A partir de | Para |
| --- | --- |
| "Pay As You Go" |[Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| "Pay As You Go" |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| "Pay As You Go" |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| "Pay As You Go" |[O MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| "Pay As You Go" |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| "Pay As You Go" |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Para outras alterações de oferta [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="switch-subscription-offer"></a>Mudar oferta de subscrição

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. Inicie sessão em [Centro de contas do Azure](https://account.windowsazure.com/Subscriptions).
1. Selecione a sua subscrição Pay As You Go.
1. Clique em **Mudar para outra oferta**. O botão só está disponível se estiver no Pay As You Go e se tiver terminado o seu primeiro período de faturação.

   ![Observe o botão de oferta de comutador no lado direito da página](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. **Selecione a oferta que pretende** na lista de ofertas a sua subscrição pode ser alterada para. Esta lista varia de acordo com as associações da sua conta. Se nada estiver disponível, consulte a [lista de ofertas disponíveis, pode mudar para](#whats-supported) e certifique-se de que tem as associações certas. 

   ![Selecionar uma oferta que pretende mudar para o](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. Consoante a oferta que estar a mudar para, poderá ver uma observação sobre o impacto da mudança. Percorra esta lista cuidadosamente e siga as instruções antes de continuar.

   ![Revise as anotações](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. Pode mudar o nome da sua subscrição. Por predefinição, podemos defini-lo para o novo nome de oferta. Clique em **comutador oferecem** para concluir o processo.

   ![Clique no botão verde](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. Êxito! A subscrição agora está definida para a nova oferta.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-is-an-azure-offer"></a>O que é uma oferta do Azure?

Uma oferta do Azure é o *tipo* da subscrição do Azure que tem. Por exemplo, [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure no Open](https://azure.microsoft.com/offers/ms-azr-0111p/), e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são todas as ofertas do Azure. Cada oferta tem diferentes [termos](https://azure.microsoft.com/support/legal/offer-details/) e tem alguns benefícios especiais. A oferta da sua subscrição pode ser encontrada na página de subscrição do Centro de contas. Clique no nome de oferta para obter mais detalhes.

   ![Clique na ligação de oferta no Centro de contas para obter mais detalhes](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>Por que motivo não vejo no botão?

Poderá não ver o **mudar para outra oferta** botão se:

* Não on [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/). Atualmente, só as subscrições Pay As You Go podem ser convertidas noutra oferta.
  * Se estiver num [avaliação gratuita](https://azure.microsoft.com/free/), saiba como [Atualize para pay as you go](billing-upgrade-azure-subscription.md).
  * Para mudar de oferta de uma subscrição diferente, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Ainda estiver em seu primeiro período de faturação; tem de aguardar que o primeiro período de faturação terminar antes de pode alternar ofertas.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>Por que vejo "Existem não existem ofertas disponíveis na sua região ou país neste momento"?

* Não pode ser elegível para quaisquer comutadores de oferta. Verifique os [lista de ofertas disponíveis, pode mudar para](#whats-supported) e certifique-se de que ativou os benefícios certos com o Visual Studio ou do Bizspark.
* Algumas ofertas poderão não estar disponíveis em todos os países.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>O que faz a mudar de fazer ofertas do Azure para meu serviço e de faturação?

Aqui estão os detalhes do que acontece quando alterna que o Azure oferece no Centro de contas.

#### <a name="no-service-downtime"></a>Sem períodos de indisponibilidade de serviço

Não há nenhum período de indisponibilidade de serviço para todos os utilizadores associados à subscrição. No entanto, a oferta que mudar para Talvez haja restrições. Por exemplo, algumas ofertas proíbem a utilização de produção, pelo que terá de mover os recursos de produção para outra subscrição.

#### <a name="quota-increases-are-reset"></a>Os aumentos de quota são repostos

Ao alternar ofertas, qualquer [limite ou uma quota aumenta acima do limite de predefinição](../azure-supportability/resource-manager-core-quotas-request.md) são repostos. Não é sem períodos de indisponibilidade do serviço, mesmo que tenha mais recursos além do limite de predefinição. Por exemplo, estiver a utilizar 200 núcleos na sua subscrição, em seguida, mudar ofertas repõe a quota de núcleos para o padrão de 20 núcleos. As VMs que utilizam os 200 núcleos são afetadas e continuam a ser executado. Se não fizer outro quota aumentar a pedido, no entanto, não pode aprovisionar qualquer mais núcleos.

#### <a name="billing"></a>Faturação

No dia que mudar, uma nota fiscal é gerada para todas as cobranças por liquidar. Em seguida, a sua subscrição é cobrada por termos de preços da nova oferta. Seu aniversário de faturação de subscrição é alterado para a data em que alterou ofertas. Utilização e faturação dados antes da alteração de oferta não é mantida, pelo que recomendamos que transfira uma cópia antes de mudar.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>Posso migrar de pay as you go para [fornecedor de soluções Cloud](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) ou [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?

* Para migrar para o CSP, veja [migração de subscrição do Azure pay as you go para o CSP](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp).
* Para migrar para o EA, ter o seu administrador de inscrição adicionar a sua conta para o EA. Siga instruções no e-mail de convite para ter as suas subscrições movidas abaixo da inscrição EA. Para obter mais informações, consulte [associar uma conta existente](https://ea.azure.com/helpdocs/associateExistingAccount) no portal do EA.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>Posso migrar dados e serviços para uma nova subscrição?

* Pode migrar os recursos diretamente para a nova subscrição, veja [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).
* Para transferir a propriedade de uma subscrição do Azure e tudo no mesmo para outra pessoa, consulte o artigo [transferir a propriedade de uma subscrição do Azure](billing-subscription-transfer.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
