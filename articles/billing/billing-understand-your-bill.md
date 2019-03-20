---
title: Compreender a sua fatura do Azure | Documentos da Microsoft
description: Saiba como ler e compreender a utilização e faturação para a sua subscrição do Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 9ba52951565c910138a5a49ecc97ad5748411e6d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842316"
---
# <a name="understand-your-microsoft-azure-bill"></a>Compreender a sua fatura do Microsoft Azure
Para compreender a fatura do Azure, compare a fatura com o ficheiro de utilização diária detalhadas e os relatórios de gestão de custos no portal do Azure.

Este artigo não se aplica aos clientes do Azure com um Enterprise Agreement (os clientes com EA). Se estiver num cliente EA, consulte [compreender a sua fatura para clientes do Azure com um Enterprise Agreement](billing-understand-your-bill-ea.md).

Este artigo não se aplica aos clientes do Azure com um contrato de cliente da Microsoft. Se tiver uma conta de cobrança para um contrato de cliente da Microsoft, consulte [compreender os encargos do Azure na sua fatura de contrato de cliente da Microsoft](billing-mca-understand-your-bill.md).

Para obter uma explicação de como funciona a faturação do programa de fornecedor de soluções de Cloud do Azure (Azure CSP), incluindo o veja de ciclo, preços e utilização, faturação [descrição geral de faturação do Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Reveja os seus custos

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Se existe uma cobrança na sua fatura que pretender obter mais informações sobre, pode comparar utilização e os custos com o ficheiro de utilização ou com o portal do Azure.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opção 1: Comparar a utilização e os custos com o ficheiro de utilização

O ficheiro CSV de utilização detalhada mostra os encargos por período de faturação e utilização diária. Para obter o ficheiro, consulte o artigo [obter a faturação da nota fiscal e diário de dados de utilização do Azure](billing-download-azure-invoice-daily-usage-date.md).

As cobranças de utilização são apresentadas no nível do medidor. Os seguintes termos significa que a mesma coisa de nota fiscal e o ficheiro de utilização detalhada. Por exemplo, o ciclo de faturação da nota fiscal é o mesmo que o período de faturação mostrado no ficheiro de utilização detalhada.

 | Nota fiscal (PDF) | Utilização detalhada (CSV)|
 | --- | --- |
|Ciclo de faturação | Período de Faturação |
 |Name |Categoria do Medidor |
 |Type |Subcategoria do medidor |
 |Recurso |Nome do Medidor |
 |Região |Região do Medidor |
 |Consumido |Quantidade Consumida |
 |Incluída |Quantidade Incluída |
 |A Cobrar |Quantidade de Utilização Excedente |

O **os custos de utilização** secção da sua fatura tem o valor total para cada contador que foi consumida durante seu período de faturação. Por exemplo, a captura de ecrã seguinte mostra um custo de utilização para o serviço de agendador do Azure.

![Custos de utilização de nota fiscal](./media/billing-understand-your-bill/1.png)

O **instrução** secção da sua utilização detalhada CSV mostra o valor cobrado pelo mesmo. Ambas as *consumida* quantidade e *valor* corresponder a nota fiscal.

![Custos de utilização CSV](./media/billing-understand-your-bill/2.png)

Para ver uma divisão diária desta cobrança, vá para o **utilização diária** secção de CSV. Filtrar por *agendador* sob *categoria do medidor*. Pode ver quais os dias foi utilizado o medidor e qual a quantidade consumida. O *Resource* e *grupo de recursos* informações também estão listadas para comparação. O *consumida* valores devem adicionar a cópia de segurança para o que é apresentado da nota fiscal.

![Secção utilização diária o CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os *consumida* resumem-se com o *taxa* partir do **instrução** secção.

Para saber mais, consulte:

- [Compreender a fatura do Azure](billing-understand-your-invoice.md)
- [Compreender a utilização detalhada do Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: Comparar a utilização e custos no portal do Azure

O portal do Azure pode também ajudar a verificar os encargos. Para obter uma visão geral da sua utilização faturada e a cobrança, ver os gráficos de gestão de custos.

1. No portal do Azure, aceda a [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a sua subscrição > **análise de custo**.
1. Filtrar por **Timespan**.
1. Para continuar o exemplo anterior, verá um custo de utilização para o serviço de agendador do Azure.

   ![Vista de análise de custo no portal do Azure](./media/billing-understand-your-bill/4.png)

1. Selecione essa linha para ver a divisão de custo diário.

   ![Vista de histórico de custos no portal do Azure](./media/billing-understand-your-bill/5.png)

Para obter mais informações, consulte [evitar custos inesperados com a faturação do Azure e a gestão de custos](billing-getting-started.md#costs).

## <a name="external"></a>Serviços externos são faturados juntamente com

Serviços externos ou encargos do marketplace, são para os recursos que foram criados por fornecedores de software de terceiros. Esses recursos estão disponíveis para utilização no Azure marketplace. Por exemplo, uma Firewall de Barracuda é um recurso do Azure marketplace oferecido por terceiros. Todas as cobranças para o firewall e de seu correspondentes medidores são como os encargos de serviços externos.

Encargos de serviços externos são faturados em separado. Os encargos não aparecem na sua fatura do Azure. Para obter mais informações, consulte [compreender os encargos de serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Cobrado pelos medidores de utilização de recursos

Azure não diretamente de fatura com base no recurso de custos. Cobrança para um recurso é calculada com os medidores de um ou mais. Medidores são usados para controlar a utilização de um recurso durante seu ciclo de vida. Destes medidores, em seguida, são utilizados para calcular a fatura.

Por exemplo, quando cria um único recurso do Azure, como uma máquina virtual, tem uma ou mais instâncias de medidor criadas. Medidores são usados para controlar a utilização do recurso ao longo do tempo. Cada medidor emite os registos de utilização que são utilizados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina de virtual (VM) criada no Azure pode ter os seguintes medidores criados para controlar a sua utilização:

- Horas de Computação
- Horas de endereço IP
- Transferência de Dados de Entrada
- Transferências de Dados de Saída
- Disco gerido Standard
- Operações de disco gerido Standard
- E/s padrão-disco
- Leitura de BLOBs de blocos de e/s padrão
- Escrita de BLOBs de blocos de e/s padrão
- Eliminação de BLOBs de blocos de e/s de padrão

Quando a VM é criada, a cada medidor começa a emissão de registos de utilização. Esta utilização e o preço do medidor é controlado no Azure, sistema de medição.

## <a name="payment"></a>Pagar a sua fatura

Se configurar um cartão de crédito ou um cartão de débito como o método de pagamento, o pagamento é cobrado automaticamente dentro de 10 dias após terminar o período de faturação. Na sua declaração de cartão de crédito, o item de linha diria **do Azure da MSFT**.

Para alterar o cartão de crédito ou débito, que é cobrado, consulte [adicionar, atualizar ou remover um cartão de crédito ou débito para o Azure](billing-how-to-change-credit-card.md).

Se [pagar por fatura](billing-how-to-pay-by-invoice.md), envie o pagamento para a localização listado na parte inferior da sua fatura.

Para verificar o estado de seu pagamento [criar um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Dicas para a gestão de custos

- Estimar os custos ao utilizar o:
  - [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Custo total da Calculadora de propriedade](https://aka.ms/azure-tco-calculator)
  - [Informações para cada serviço detalhadas de preços](https://azure.microsoft.com/pricing/)
- [Reveja a utilização e custos regularmente no portal do Azure](billing-getting-started.md#costs).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Saiba mais

- [Obtenha sua fatura do Azure e dados de utilização diária](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na sua fatura do Microsoft Azure](billing-understand-your-invoice.md)
- [Compreender os termos na utilização de detalhadas do Microsoft Azure](billing-understand-your-usage.md)
- [Gestão de custos de portal do Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Evitar custos inesperados com a faturação do Azure e a gestão de custos](billing-getting-started.md#costs)
