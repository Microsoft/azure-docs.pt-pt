---
title: Rever a fatura da subscrição do Azure individual
description: Saiba como compreender a fatura e a utilização de recursos e verificar os custos na subscrição individual do Azure, incluindo pay as you go.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: banders
ms.openlocfilehash: 95af762e0ff1986f9d1395e787c73b3a886a7a2e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653286"
---
# <a name="tutorial-review-your-individual-azure-subscription-bill"></a>Tutorial: Rever a fatura da subscrição do Azure individual

Este artigo ajuda-o a compreender e rever a fatura da sua subscrição do Azure, incluindo pay as you go e Visual Studio. Normalmente, recebe uma fatura por e-mail para cada período de faturação. A fatura é uma representação da conta do Azure. Estão disponíveis no portal do Azure as mesmas informações de custos descritas na fatura. Neste tutorial, vai comparar a fatura com o ficheiro detalhado de utilização diária e com a análise de custos no portal do Azure.

Este tutorial aplica-se apenas aos clientes do Azure com uma subscrição individual. As subscrições individuais comuns do Azure são as subscrições com tarifas pay as you go compradas diretamente no site do Azure.

Se precisar de ajuda para compreender custos inesperados, veja [Analisar custos inesperados](analyze-unexpected-charges.md). Em alternativa, se precisar de cancelar a sua subscrição do Azure, veja [Cancelar a sua subscrição do Azure](../manage/cancel-azure-subscription.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Comparar os custos faturados com o ficheiro de utilização
> * Comparar os custos e a utilização na análise de custos

## <a name="prerequisites"></a>Pré-requisitos

Tem de ter uma conta de faturação do *Programa Microsoft Online Services* paga. A conta é criada quando se inscreve no Azure através do site do Azure. Por exemplo, se tiver uma [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou se for um [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

As faturas das [Contas Gratuitas do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) só são criadas quando o valor mensal do crédito é excedido.

Passaram mais de 30 dias desde o dia em que subscreveu o Azure. O Azure cobra-o no final do período da fatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="compare-billed-charges-with-your-usage-file"></a>Comparar os custos faturados com o ficheiro de utilização

<a name="charges"></a>

O primeiro passo para comparar a utilização e os custos é transferir a fatura e os ficheiros de utilização. O ficheiro CSV de utilização detalhada mostra os custos por período de faturação e por utilização diária. Não inclui informações de impostos. Para transferir os ficheiros, tem de ser um administrador de conta ou ter a função de Proprietário.

No portal do Azure, escreva *subscrições* na caixa de pesquisa e, em seguida, clique em [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

[![Navegar para as subscrições](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

Na lista de subscrições, clique na subscrição.

Em **Faturação**, clique em **Faturas**.

Na lista de faturas, procure a fatura que quer transferir e, em seguida, clique no símbolo de transferência. Pode ser necessário alterar o período de tempo para ver faturas mais antigas. Pode demorar alguns minutos a gerar a fatura e o ficheiro dos detalhes da utilização.

![Captura de ecrã que mostra os períodos de faturação, a opção de transferência e os custos totais de cada período de faturação](./media/review-individual-bill/download-invoice.png)

Na janela Transferir Utilização + Custos, clique em **Transferir csv** e **Transferir fatura**.

![Captura de ecrã que mostra a página Transferir fatura e utilização](./media/review-individual-bill/usageandinvoice.png)

Se for apresentada a mensagem**Não disponível**, existem vários motivos pelos quais não vê os detalhes da utilização ou uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.
- Não existe utilização referente ao período de faturação.
- Ainda não foi gerada uma fatura. Aguarde até ao fim do período de faturação.
- Não tem permissão para ver faturas. É possível que não veja faturas antigas a menos que seja o Administrador de Conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](../manage/manage-billing-access.md).
- Se tiver uma Avaliação Gratuita ou um montante de crédito mensal com a sua subscrição que não excedeu, não receberá nenhuma fatura, a menos que tenha um Contrato de Cliente da Microsoft.

Depois, analise os custos. A fatura mostra os valores dos impostos e os custos de utilização.

![Exemplo de Fatura do Azure](./media/review-individual-bill/invoice-usage-charge.png)

Abra o ficheiro CSV de utilização que transferiu. No fim do ficheiro, some o valor de todos os itens na coluna *Custo*.

![Exemplo de ficheiro de utilização com o custo somado](./media/review-individual-bill/usage-file-usage-charges.png)

 O valor somado do *Custo* deve corresponder exatamente ao valor dos *custos de utilização* na fatura.

Os custos de utilização são apresentados ao nível do medidor. Os termos a seguir significam a mesma coisa tanto na fatura quanto no ficheiro de utilização detalhado. Por exemplo, o ciclo de faturação na fatura é o mesmo que o período de faturação mostrado no ficheiro de utilização detalhada.

| Fatura (PDF) | Utilização detalhada (CSV)|
| --- | --- |
|Ciclo de faturação | BillingPeriodStartDate BillingPeriodEndDate |
|Nome |Categoria do Medidor |
|Tipo |Subcategoria do Medidor |
|Recurso |MeterName |
|Região |MeterRegion |
|Consumido | Quantidade |
|Incluídos |Quantidade Incluída |
|A Cobrar |Quantidade de Utilização Excedente |
|Tarifa | EffectivePrice|
| Valor | Custo |

A secção **Custos de Utilização** da fatura mostra o valor total (custo) de cada medidor que foi consumido durante o período de faturação. Por exemplo, a imagem a seguir mostra os custos de utilização do serviço de Armazenamento do Microsoft Azure para o recurso de *Discos P10*.

![Custos de utilização da fatura](./media/review-individual-bill/invoice-usage-charges.png)

No ficheiro CSV de utilização, filtre por *MeterName* para o Recurso correspondente mostrado na fatura. Em seguida, some o valor do *Custo* dos itens na coluna. Eis um exemplo focado no nome do medidor (discos P10) que corresponde ao mesmo item de linha na fatura.

![Valor somado do ficheiro de utilização para MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

O valor somado do *Custo* deve corresponder exatamente ao valor dos *custos de utilização* do recurso individual cobrado na fatura.

Para obter mais informações, veja [Compreender a fatura do Azure](understand-invoice.md) e [Compreender a utilização detalhada do Azure](understand-usage.md).

## <a name="compare-billed-charges-and-usage-in-cost-analysis"></a>Comparar os custos faturados e a utilização na análise de custos

A análise de custos no portal do Azure também pode ajudar a verificar os custos. Para obter uma descrição geral rápida da utilização e dos custos faturados, selecione a subscrição na [página Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. Em seguida, clique em **Análise de custos** e, na lista de vistas, clique nos **Detalhes da fatura**.

![Exemplo a mostrar a seleção dos Detalhes da fatura](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Em seguida, na lista de intervalos de datas, selecione um período de tempo para a fatura. Adicione um filtro para o número da fatura e, em seguida, selecione o Número da Fatura que corresponde ao número presente na fatura. A análise de custos apresenta os detalhes do custo dos itens faturados.

![Exemplo que mostra os detalhes dos custos faturados na análise de custos](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Os custos mostrados na análise de custos devem corresponder exatamente ao valor dos *custos de utilização* do recurso individual cobrado na fatura.

![Custos de utilização da fatura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-marketplace-services-are-billed-separately"></a>Os serviços do Marketplace externos são faturados separadamente

<a name="external"></a>

Os serviços externos ou os custos do marketplacet dizem respeito aos recursos que foram criados por fornecedores de software de terceiros. Estes recursos estão disponíveis para utilização no Azure Marketplace. Por exemplo, uma Firewall Barracuda é um recurso do Azure Marketplace oferecido por terceiros. Todos os custos da firewall e os medidores correspondentes aparecem como custos de serviços externos.

Os custos de serviços externos são faturados separadamente. Os custos não aparecem na fatura do Azure. Para saber mais, veja [Compreender os custos de serviços externos do Azure](understand-azure-marketplace-charges.md).

### <a name="resources-are-billed-by-usage-meters"></a>Os recursos são faturados por medidores de utilização

O Azure não é fatura diretamente com base no custo do recurso. Os custos de um recurso são calculados com um ou mais medidores. Os medidores são utilizados para monitorizar a utilização de um recurso durante o seu tempo de vida. Estes medidores são utilizados para calcular a fatura.

Quando cria um único recurso do Azure, como uma máquina virtual, são criadas uma ou mais instâncias de medidor. Os medidores são utilizados para controlar a utilização do recurso ao longo do tempo. Cada medidor emite registos de utilização que são utilizados pelo Azure para calcular a fatura.

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

Pode ver os medidores que foram utilizados para calcular a faturação no ficheiro CSV de utilização, como no exemplo anterior.

## <a name="pay-your-bill"></a>Pagar a sua fatura

<a name="payment"></a>

Se configurar um cartão de crédito como o método de pagamento, o pagamento será cobrado automaticamente dentro de 10 dias após o fim do período de faturação. No extrator do cartão de crédito, o item de linha indicará **MSFT Azure**.

Para alterar o cartão de crédito utilizado, veja [Adicionar, atualizar ou remover um cartão de crédito do Azure](../manage/change-credit-card.md).

Se [pagar por fatura](../manage/pay-by-invoice.md), envie o pagamento para a localização indicada na parte inferior da fatura.

Para verificar o estado do pagamento, [crie um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Comparar os custos faturados com o ficheiro de utilização
> * Comparar os custos e a utilização na análise de custos

Conclua o início rápido para começar a utilizar a análise de custos.

> [!div class="nextstepaction"]
> [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
