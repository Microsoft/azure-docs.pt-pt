---
title: Compreender a fatura de contrato de cliente da Microsoft | Documentos da Microsoft
description: Saiba como ler e compreender a sua fatura MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: e5dc8ac0716e194dd0949e2e270346ee338a792a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839734"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Compreender os termos na sua fatura de contrato de cliente da Microsoft

Este artigo aplica-se a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se tem acesso a um contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

A fatura fornece um resumo dos seus custos e as instruções de pagamento. Está disponível para download no formato de documento (. pdf) portáteis do [portal do Azure](https://portal.azure.com/) ou podem ser enviados por e-mail. Para obter mais informações, consulte [View e o download do Microsoft Azure nota fiscal](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Termos de nota fiscal e descrições

As secções seguintes listam os termos importantes que vê na sua fatura e as descrições para cada termo.

### <a name="invoice-summary"></a>Resumo da fatura

O **resumo da fatura** está no topo da página de primeiro e mostra informações sobre o perfil de faturação e como pagar.

![Secção de resumo de nota fiscal](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termo | Descrição |
| --- | --- |
| Vendido a |Endereço da sua entidade legal, encontrado nas propriedades da conta de faturação|
| Faturar a |Endereço do perfil de faturação, receber a nota fiscal, encontrada nas propriedades de perfil de faturação de faturação|
| Perfil de Faturação |O nome do perfil de faturação, receber a nota fiscal |
| P.O. número |Um número de ordem de compra opcional, atribuído por si para o controlo de |
| Número da nota fiscal |Um número de nota fiscal exclusivo, gerados pelo Microsoft utilizado para efeitos de controlo |
| Data da fatura |Data em que a nota fiscal é gerada, normalmente, cinco a 12 dias após o final do ciclo de faturação. Pode verificar a data da nota fiscal nas propriedades do perfil de faturação.|
| Condições de pagamento |Como pagar à sua fatura da Microsoft. *Líquido 30 dias* significa que paga pela verificação ou conectar transferência dentro de 30 dias após a data da nota fiscal. |

### <a name="billing-summary"></a>Resumo de faturação

O **resumo de faturação** mostra os custos em relação a faturação desde o período de faturação anterior, quaisquer créditos que foram aplicados, impostos e a quantidade total devida.

![Secção de resumo de faturação](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termo | Descrição |
| --- | --- |
| Encargos|Número total de cobranças da Microsoft para este perfil de faturação desde o último período de faturação |
| Ficha técnica |Créditos que recebeu da devolve |
| Aplicadas os créditos do Azure | Créditos do Azure que são automaticamente aplicados aos custos do Azure cada período de faturação |
| Subtotal |O valor do pré-imposto de devido |
| Impostos |O tipo e a quantidade de imposto sobre o que paga, dependendo do país do seu perfil de faturação. Se não precisa de pagar impostos sobre, em seguida, não verá imposto na sua fatura. |
| Poupança estimada de total |A quantidade total estimada que guardou a partir de descontos em vigor. Se aplicável, taxas de desconto em vigor estão listadas abaixo os itens de linha de compra em detalhes pela secção de nota fiscal. |

### <a name="invoice-sections"></a>Secções de fatura

Para cada secção de notas fiscais em seu perfil de faturação, verá os custos, o montante de créditos do Azure aplicadas, impostos e a quantia total devida.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Detalhes pela secção de nota fiscal

Os detalhes mostram o custo para cada secção de nota fiscal dividido por ordem de produto. Dentro da ordem de cada produto, o custo é dividido pelo tipo de serviço. Encontrará Cobranças diária para seus produtos e serviços no portal do Azure e utilização do Azure e custos de CSV. Para saber mais, veja [compreender os encargos na sua fatura para um contrato de cliente da Microsoft](billing-mca-understand-your-bill.md).

O montante total devido para cada família de serviços é calculada ao subtrair *créditos do Azure* de *créditos/custos* e adicionando *imposto*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Detalhes pela secção de nota fiscal](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termo |Descrição |
| --- | --- |
| Preço unitário | O preço de unidade em vigor a partir do serviço (em moeda de preços) que é utilizado para a taxa de utilização. Este é exclusivo para um produto, a família de serviços, o medidor e a oferta. |
| . De defeitos | Quantidade adquiridos ou consumido durante o período de faturação |
| Custos/créditos | Valor líquido de custos, depois de serem aplicados créditos/reembolsos |
| Crédito do Azure | O montante de créditos do Azure aplicados custos de e/ou créditos|
| Taxa de tributação | Imposto sobre vendas rate(s) dependendo do país |
| Valor do imposto | Quantidade de imposto sobre aplicada para comprar com base na taxa de tributação |
| Total | O montante total devido para a compra |

### <a name="how-to-pay"></a>Como pagar

Na parte inferior da nota fiscal, existem instruções de pagamento de sua fatura. Pode pagar por verificação, durante a transmissão, ou online. Se optar pelo pagamento online, pode utilizar um cartão de crédito/débito ou créditos do Azure, se aplicável.

### <a name="publisher-information"></a>Informações do publicador

Se tiver serviços de terceiros na sua fatura, o nome e endereço de cada publicador está listado na parte inferior da sua fatura.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos Seguintes

- [Compreender os encargos na fatura de seu perfil de faturação](billing-mca-understand-your-bill.md)
- [Como obter a faturação e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Veja os preços do Azure da sua organização](billing-ea-pricing.md)
- [Ver documentos de imposto para o seu perfil de faturação](billing-mca-download-tax-document.md)
