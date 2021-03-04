---
title: Compreender a fatura do seu Contrato de Cliente da Microsoft no Azure
description: Saiba como ler e compreender a sua fatura de um Contrato de Cliente da Microsoft no Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 996c974473c53833dd26abae2fb61585999f33b4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050448"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termos na sua fatura do Contrato de Cliente da Microsoft

Este artigo aplica-se à conta de faturação do Azure de um Contrato de Cliente da Microsoft. [Verifique se tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

A sua fatura disponibiliza um resumo dos seus custos e instruções para o pagamento. Está disponível para transferência em .pdf (Portable Document Format) a partir do [portal do Azure](https://portal.azure.com/) ou pode ser enviada por e-mail. Para obter mais informações, veja [Ver e transferir a sua fatura do Microsoft Azure](download-azure-invoice.md).

Veja o vídeo da fatura do [Microsoft Customer Agreement](https://www.youtube.com/watch?v=e2LGZZ7GubA) para saber mais sobre a sua fatura e como analisar os encargos.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Período de faturação

A faturação é mensal. Pode saber em que dia do mês recebe faturas ao verificar a *data da fatura* nas propriedades do perfil de faturação no [portal do Azure](https://portal.azure.com/). As cobranças que ocorrem entre o fim do período de faturação e a data da fatura são incluídas na fatura do próximo mês, pois estão no próximo período de faturação. As datas de início e fim do período de faturação para cada fatura são listadas no PDF da fatura acima do **Resumo de Faturação**.

## <a name="invoice-terms-and-descriptions"></a>Termos e descrições da fatura

As secções seguintes apresentam uma lista de termos importantes que vê na fatura e fornecem descrições de cada termo.

### <a name="invoice-summary"></a>Resumo da fatura

O **Resumo da Fatura** está na parte superior da primeira página e mostra informações sobre o seu perfil de faturação e de que forma paga.

![Secção Resumo da fatura](./media/mca-understand-your-invoice/invoicesummary.png)

| Termo | Descrição |
| --- | --- |
| Vendido a |Endereço da sua entidade legal, que se encontra nas propriedades da conta de faturação|
| Faturar a |Endereço de faturação do perfil de faturação que recebe a fatura, encontrado nas propriedades do perfil de faturação|
| Perfil de Faturação |O nome do perfil de faturação que recebe a fatura |
| Nota de compra –  número |Um número de ordem de compra opcional, atribuído por si para controlo |
| Número da fatura |Um número de fatura exclusivo gerado pela Microsoft utilizado para fins de controlo |
| Data da fatura |Data em que a fatura é gerada, normalmente cinco a 12 dias após o fim do Ciclo de faturação. Pode verificar a data da fatura nas propriedades do perfil de faturação.|
| Termos de pagamento |Como paga pela sua fatura da Microsoft. *30 dias de calendário* significa que paga dentro de 30 dias a partir da data da fatura. |

### <a name="billing-summary"></a>Resumo de faturação

O **Resumo de Faturação** mostra as cobranças em relação ao perfil de faturação desde o período de faturação anterior, todos os créditos que foram aplicados, os impostos e o valor total devido.

![Secção Resumo de faturação](./media/mca-understand-your-invoice/billingsummary.png)

| Termo | Descrição |
| --- | --- |
| Cobranças|Número total de cobranças da Microsoft para este perfil de faturação desde o último período de faturação |
| Créditos |Créditos recebidos de devoluções |
| Créditos do Azure aplicados | Os créditos do Azure que são aplicados automaticamente a cobranças do Azure em cada período de faturação |
| Subtotal |O valor devido antes de impostos |
| Impostos |O tipo e o montante de impostos que paga, consoante o país/região do seu perfil de faturação. Se não tiver de pagar impostos, não verá o imposto na sua fatura. |
| Poupança total estimada |O valor total estimado que poupou dos descontos efetivos. Se aplicável, as tarifas de desconto efetivas são listadas abaixo dos itens de linha de compra na secção Detalhes por Fatura. |

### <a name="invoice-sections"></a>Secções de faturação

Para cada secção de faturação sob o seu perfil de faturação, verá as cobranças, a quantidade de créditos do Azure aplicados, os impostos e o valor total devido.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Detalhes por secção de fatura

Os detalhes mostram o custo de cada secção de faturação dividida por encomenda de produto. Em cada encomenda de produto, o custo é dividido pelo tipo de serviço. Pode encontrar cobranças diárias para os seus produtos e serviços no portal do Azure e um CSV de utilização e cobranças do Azure. Para saber mais, veja [Understand the charges on your invoice for a Microsoft Customer Agreement](review-customer-agreement-bill.md) (Compreender as cobranças na sua fatura de um Contrato de Cliente da Microsoft).

O valor total devido por cada família de serviços é calculado ao subtrair *créditos do Azure* a partir de *Créditos/cobranças* e ao adicionar *Impostos*:


![Detalhes por secção de fatura](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Termo |Descrição |
| --- | --- |
| Preço unitário | O preço unitário efetivo do serviço (em moeda de preço) que serve para taxar a utilização. Este valor é exclusivo para um produto, uma família de serviços, um medidor e uma oferta. |
| Quantidade | A quantidade comprada ou consumida durante o período de faturação |
| Cobranças/Créditos | Quantidade líquida de cobranças após os créditos/reembolsos serem aplicados |
| Crédito do Azure | A quantidade de créditos do Azure aplicados às Cobranças/Créditos|
| Taxa de imposto | Taxa(s) de imposto consoante o país/região |
| Montante dos impostos | Valor dos impostos aplicados à compra com base na taxa de imposto |
| Total | O valor total devido em relação à compra |

### <a name="how-to-pay"></a>Como pagar

Na parte inferior da fatura, existem instruções para pagar a sua fatura. Pode pagar por cheque, transferência bancária ou online. Se pagar online, pode usar um cartão de crédito ou créditos do Azure, se aplicável.

### <a name="publisher-information"></a>Informações do Publicador

Se tiver serviços de terceiros na sua fatura, o nome e o endereço de cada publicador está listado na parte inferior da fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- [Compreender os custos na sua fatura do perfil de faturação](review-customer-agreement-bill.md)
- [Como obter a faturação e os dados de utilização diária do Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Ver os preços do Azure da sua organização](../manage/ea-pricing.md)
- [Visualizar documentos fiscais para o seu perfil de faturação](mca-download-tax-document.md)
