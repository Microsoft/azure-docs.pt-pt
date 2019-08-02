---
title: Compreender a fatura do Azure
description: Saiba como ler e entender seu uso e cobrança para sua assinatura do Azure.
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
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610128"
---
# <a name="understand-your-microsoft-azure-bill"></a>Entenda sua fatura de Microsoft Azure
Para entender sua fatura do Azure, você compara sua fatura com o arquivo de uso diário detalhado e com relatórios de gerenciamento de custos no portal do Azure.

Este artigo não se aplica aos seguintes clientes:
- Clientes do Azure com um Enterprise Agreement (clientes do EA). Se você for um cliente do EA, consulte [entender sua fatura para clientes do Azure com um Enterprise Agreement](billing-understand-your-bill-ea.md).
- Clientes do Azure com um [contrato de cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement). Se você tiver um contrato com o cliente da Microsoft, consulte [entender os encargos do Azure em sua fatura do contrato de clientes da Microsoft](billing-mca-understand-your-bill.md).

Para obter uma explicação de como a cobrança funciona no programa Azure CSP (provedor de soluções na nuvem do Azure), incluindo o ciclo de cobrança, os preços e o uso, consulte [visão geral de cobrança do Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Examine seus encargos

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Se houver uma cobrança na fatura sobre a qual você deseja obter mais informações, você poderá comparar o uso e os custos com o arquivo de uso ou com o portal do Azure.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Opção 1: Comparar o uso e os custos com o arquivo de uso

O arquivo CSV de uso detalhado mostra os encargos por período de cobrança e uso diário. Para baixar ou exibir o arquivo, consulte [obter sua fatura de cobrança do Azure e dados de uso diário](billing-download-azure-invoice-daily-usage-date.md).

Os encargos de uso são exibidos no nível do medidor. Os termos a seguir significam a mesma coisa tanto na fatura quanto no arquivo de uso detalhado. Por exemplo, o ciclo de cobrança na fatura é o mesmo que o período de cobrança mostrado no arquivo de uso detalhado.

 | Fatura (PDF) | Uso Detalhado (CSV)|
 | --- | --- |
|Ciclo de faturação | Período de Faturação |
 |Nome |Categoria de Medidor |
 |Type |Subcategoria do medidor |
 |Resource |Nome de Medidor |
 |Região |Região do Medidor |
 |Consumido |Quantidade Consumida |
 |Incluída |Quantidade Incluída |
 |A Cobrar |Quantidade de Utilização Excedente |

A seção encargos de **uso** de sua fatura mostra o valor total de cada medidor que foi consumido durante o período de cobrança. Por exemplo, a imagem a seguir mostra um encargo de uso para o serviço Agendador do Azure.

![Encargos de uso da fatura](./media/billing-understand-your-bill/1.png)

A seção de **instrução** de seu CSV de uso detalhado mostra o mesmo encargo. A quantidade consumida e o *valor* correspondem à fatura.

![Encargos de uso de CSV](./media/billing-understand-your-bill/2.png)

Para ver uma análise diária do encargo, vá para a seção **uso diário** do arquivo CSV. Filtro para o Agendador na *categoria de medidor*. Você pode ver quais dias o medidor foi usado e quanto foi consumido. As informações do *grupo* de *recursos e recursos* também são mostradas para comparação. Os valores consumidos devem somar e corresponder ao que é mostrado na nota fiscal.

![Seção de uso diário no CSV](./media/billing-understand-your-bill/3.png)

Para obter o custo por dia, multiplique os valores consumidos com o valor de *taxa* da seção de **instrução** .

Para saber mais, consulte:

- [Entender sua fatura do Azure](billing-understand-your-invoice.md)
- [Entenda o uso detalhado do Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Opção 2: Compare o uso e os custos no portal do Azure

O portal do Azure também pode ajudá-lo a verificar seus encargos. Para obter uma visão geral rápida do uso faturado e dos encargos, veja os gráficos de gerenciamento de custos.

1. No portal do Azure, vá para [assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione sua assinatura > **análise de custo**.
1. Filtrar por **TimeSpan**.
1. Para continuar o exemplo anterior, você verá um encargo de uso para o serviço Agendador do Azure.

   ![Exibição de análise de custo no portal do Azure](./media/billing-understand-your-bill/4.png)

1. Selecione a linha que mostra a cobrança para ver a divisão de custo diário.

   ![Exibição do histórico de custo no portal do Azure](./media/billing-understand-your-bill/5.png)

Para saber mais, confira [evitar custos inesperados com cobrança e gerenciamento de custos do Azure](billing-getting-started.md#costs).

## <a name="external"></a>Serviços externos cobrados separadamente

Os serviços externos ou as cobranças do Marketplace são para recursos que foram criados por fornecedores de software de terceiros. Esses recursos estão disponíveis para uso no Azure Marketplace. Por exemplo, um firewall de Barracuda é um recurso do Azure Marketplace oferecido por terceiros. Todos os encargos do firewall e seus medidores correspondentes aparecem como encargos de serviço externo.

Os encargos de serviço externo são cobrados separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [entender os encargos de serviço externo do Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Recursos cobrados por medidores de uso

O Azure não é cobrado diretamente com base no custo do recurso. Os encargos de um recurso são calculados usando um ou mais medidores. Os medidores são usados para rastrear o uso de um recurso durante seu tempo de vida. Esses medidores são usados para calcular a fatura.

Por exemplo, quando você cria um único recurso do Azure, como uma máquina virtual, ele tem uma ou mais instâncias de medidor criadas. Os medidores são usados para controlar o uso do recurso ao longo do tempo. Cada medidor emite registros de uso que são usados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para controlar seu uso:

- Horas de Computação
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferências de Dados de Saída
- Disco gerenciado Standard
- Operações do Disco Gerenciado Standard
- E/s padrão-disco
- E/s padrão-leitura de blob de blocos
- E/s padrão-gravação de blob de blocos
- E/s padrão-exclusão de blob de blocos

Quando a VM é criada, cada medidor começa a emitir registros de uso. Esse uso e o preço do medidor são controlados no sistema de medição do Azure.

## <a name="payment"></a>Pague sua fatura

Se você configurar um cartão de cartão de crédito como seu método de pagamento, o pagamento será cobrado automaticamente dentro de 10 dias após o término do período de cobrança. No seu demonstrativo de cartão de crédito, o item de linha diria **MSFT Azure**.

Para alterar o cartão de crédito cobrado, consulte [Adicionar, atualizar ou remover um cartão de crédito do Azure](billing-how-to-change-credit-card.md).

Se você [pagar por fatura](billing-how-to-pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

Para verificar o status de seu pagamento, [crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Dicas para o gerenciamento de custos

- Estimar os custos usando:
  - [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Calculadora de custo total de propriedade](https://aka.ms/azure-tco-calculator)
  - [Informações detalhadas sobre preços para cada serviço](https://azure.microsoft.com/pricing/)
- [Examine o uso e os custos regularmente na portal do Azure](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato do cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Saber mais

- [Obtenha sua fatura de cobrança e dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Entenda os termos em sua fatura Microsoft Azure](billing-understand-your-invoice.md)
- [Entenda os termos em seu Microsoft Azure uso detalhado](billing-understand-your-usage.md)
- [Gerenciamento de custos portal do Azure](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Evitar custos inesperados com cobrança e gerenciamento de custos do Azure](billing-getting-started.md#costs)
