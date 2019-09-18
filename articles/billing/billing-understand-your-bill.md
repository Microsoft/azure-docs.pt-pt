---
title: Compreender a fatura do Azure
description: Saiba como ler e compreender a utilização e faturação da subscrição do Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: banders
ms.openlocfilehash: 51143644a62a77a61c4540d9f2ad3dce401c496b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "68610128"
---
# <a name="understand-your-microsoft-azure-bill"></a>Compreender a fatura do Microsoft Azure
Para compreender a fatura do Azure, compare-a ao ficheiro de utilização diária detalhada e aos relatórios de gestão de custos no portal do Azure.

Este artigo não se aplica aos seguintes clientes:
- Clientes do Azure com um Contrato Enterprise (clientes EA). Se for cliente EA, veja [Compreender a fatura dos clientes do Azure com Contrato Enterprise](billing-understand-your-bill-ea.md).
- Clientes do Azure com um [Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement). Se tiver um Contrato de Cliente da Microsoft, veja [Compreender os custos do Azure na fatura do Contrato de Cliente da Microsoft](billing-mca-understand-your-bill.md).

Para obter uma explicação do funcionamento da faturação no programa Fornecedor de Solução Cloud do Azure (Azure CSP), incluindo o ciclo de faturação, os preços e a utilização, veja [Azure CSP Billing Overview](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/) (Descrição Geral da Faturação do Azure CSP).

## <a name="charges"></a>Rever os custos

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Se desejar obter mais informações sobre um custo na fatura, poderá comparar a utilização e os custos com o ficheiro de utilização ou com o portal do Azure.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opção 1: Comparar a utilização e os custos com o ficheiro de utilização

O ficheiro CSV de utilização detalhada mostra os custos por período de faturação e por utilização diária. Para transferir ou ver o ficheiro, veja [Obter a faturação e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md).

Os custos de utilização são apresentados ao nível do medidor. Os termos a seguir significam a mesma coisa tanto na fatura quanto no ficheiro de utilização detalhado. Por exemplo, o ciclo de faturação na fatura é o mesmo que o período de faturação mostrado no ficheiro de utilização detalhada.

 | Fatura (PDF) | Utilização detalhada (CSV)|
 | --- | --- |
|Ciclo de faturação | Período de Faturação |
 |Nome |Categoria do Medidor |
 |Tipo |Subcategoria do Medidor |
 |Recurso |Nome do Medidor |
 |Região |Região do Medidor |
 |Consumido |Quantidade Consumida |
 |Incluída |Quantidade Incluída |
 |A Cobrar |Quantidade de Utilização Excedente |

A secção **Custos de Utilização** da fatura mostra o valor total de cada medidor que foi consumido durante o período de faturação. Por exemplo, a imagem a seguir mostra os custos de utilização do serviço Azure Scheduler.

![Custos de utilização da fatura](./media/billing-understand-your-bill/1.png)

A secção **Extrato** do CSV de utilização detalhada mostra o mesmo custo. Tanto a quantidade *Consumida* como o *Valor* correspondem à fatura.

![Custos de utilização do CSV](./media/billing-understand-your-bill/2.png)

Para ver uma análise diária do custo, vá para a secção **Utilização Diária** do ficheiro CSV. Filtre por *Scheduler* em *Categoria de Medidor*. Pode ver em que dias o medidor foi utilizado e quanto foi consumido. As informações *Recurso* e *Grupo de recursos* também são mostradas para fins de comparação. Os valores de *Consumido* devem totalizar e corresponder ao que é mostrado na fatura.

![Secção Utilização Diária no CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os valores de *Consumido* pelo valor de *Taxa* da secção **Extrato**.

Para saber mais, consulte:

- [Compreender a fatura do Azure](billing-understand-your-invoice.md)
- [Compreender a fatura detalhada do Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: Comparar a utilização e os custos no portal do Azure

O portal do Azure também pode o ajudar a verificar os custos. Para obter uma breve descrição geral da utilização e dos custos faturados, veja os gráficos de gestão de custos.

1. No portal do Azure, aceda a [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a subscrição > **Análise de custos**.
1. Filtrar por **Período de tempo**.
1. Para continuar com o exemplo anterior, verá um custo de utilização do serviço Azure Scheduler.

   ![Vista Análise de custos no portal do Azure](./media/billing-understand-your-bill/4.png)

1. Selecione a linha que mostra o custo para ver a discriminação diária dos custos.

   ![Vista Histórico dos custos no portal do Azure](./media/billing-understand-your-bill/5.png)

Para saber mais, veja [Evitar custos inesperados com a faturação e a gestão de custos do Azure](billing-getting-started.md#costs).

## <a name="external"></a>Serviços externos faturados separadamente

Os serviços externos ou os custos do marketplacet dizem respeito aos recursos que foram criados por fornecedores de software de terceiros. Estes recursos estão disponíveis para utilização no Azure Marketplace. Por exemplo, uma Firewall Barracuda é um recurso do Azure Marketplace oferecido por terceiros. Todos os custos da firewall e os medidores correspondentes aparecem como custos de serviços externos.

Os custos de serviços externos são faturados separadamente. Os custos não aparecem na fatura do Azure. Para saber mais, veja [Compreender os custos de serviços externos do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Recursos faturados por medidores de utilização

O Azure não é fatura diretamente com base no custo do recurso. Os custos de um recurso são calculados com um ou mais medidores. Os medidores são utilizados para monitorizar a utilização de um recurso durante o seu tempo de vida. Estes medidores são utilizados para calcular a fatura.

Por exemplo, quando cria um único recurso do Azure, como uma máquina virtual, são criadas uma ou mais instâncias de medidor. Os medidores são utilizados para controlar a utilização do recurso ao longo do tempo. Cada medidor emite registos de utilização que são utilizados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para controlar a utilização:

- Horas de Computação
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferência de Dados de Saída
- Disco Gerido Standard
- Operações de Discos Geridos Standard
- E/S Padrão – Disco
- E/S Padrão – Leitura de Blobs de Blocos
- E/S Padrão – Escrita de Blobs de Blocos
- E/S Padrão – Eliminação de Blobs de Blocos

Uma vez criada a VM, cada medidor começa a emitir registos de utilização. Esta utilização e o preço do medidor são monitorizados no sistema de medição do Azure.

## <a name="payment"></a>Pagar a fatura

Se configurar um cartão de crédito como o método de pagamento, o pagamento será cobrado automaticamente dentro de 10 dias após o fim do período de faturação. No extrator do cartão de crédito, o item de linha indicará **MSFT Azure**.

Para alterar o cartão de crédito utilizado, veja [Adicionar, atualizar ou remover um cartão de crédito do Azure](billing-how-to-change-credit-card.md).

Se [pagar por fatura](billing-how-to-pay-by-invoice.md), envie o pagamento para a localização indicada na parte inferior da fatura.

Para verificar o estado do pagamento, [crie um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Sugestões para a gestão dos custos

- Estime os custos com:
  - [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Calculadora do custo total de posse (TCO)](https://aka.ms/azure-tco-calculator)
  - [Informações detalhadas dos preços de cada serviço](https://azure.microsoft.com/pricing/)
- [Analise a utilização e os custos regularmente no portal do Azure](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Saiba mais

- [Obter a faturação e os dados de utilização diária do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Compreender os termos na fatura do Microsoft Azure](billing-understand-your-invoice.md)
- [Compreender os termos na utilização diária do Microsoft Azure](billing-understand-your-usage.md)
- [Gestão de custos do portal do Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Evitar custos inesperados com a faturação e a gestão de custos do Azure](billing-getting-started.md#costs)
