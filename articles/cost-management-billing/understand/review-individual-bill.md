---
title: Examine sua fatura do Azure individual
description: Saiba como entender seu uso de faturas e recursos e verificar encargos para sua assinatura individual do Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987349"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Tutorial: examinar sua fatura do Azure individual

Este artigo ajuda você a entender e revisar sua fatura do Azure. Para cada período de cobrança, você normalmente recebe uma fatura por email. A fatura é uma representação da sua fatura do Azure. As mesmas informações de custo na fatura estão disponíveis na portal do Azure. Neste tutorial, você comparará sua fatura com o arquivo de uso diário detalhado e com a análise de custo no portal do Azure.

Este tutorial se aplica somente aos clientes do Azure com uma assinatura individual. As assinaturas individuais comuns são aquelas com tarifas pagas conforme o uso adquiridas diretamente do site do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Comparar encargos faturados com o arquivo de uso
> * Comparar encargos e uso na análise de custo

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter uma conta paga de cobrança *do programa do Microsoft Online Services* . A conta é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, se você tiver uma [conta com tarifas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou se for um [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

As notas fiscais para [contas gratuitas do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) são criadas somente quando o valor de crédito mensal é excedido.

Deve ser mais de 30 dias a partir do dia em que você se inscreveu no Azure. O Azure cobra-o no final do período da fatura.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="compare-invoiced-charges-with-usage-file"></a>Comparar encargos faturados com o arquivo de uso

<a name="charges"></a>

A primeira etapa para comparar o uso e os custos é baixar seus arquivos de fatura e uso. O ficheiro CSV de utilização detalhada mostra os custos por período de faturação e por utilização diária. Ele não inclui nenhuma informação de imposto. Para baixar os arquivos, você deve ser um administrador da conta ou ter a função de proprietário.

No portal do Azure, digite *assinaturas* na caixa de pesquisa e clique em [assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Navegar para assinaturas](./media/review-individual-bill/navigate-subscriptions.png)

Na lista de assinaturas, clique na assinatura.

Em **cobrança**, clique em **notas fiscais**.

Na lista de faturas, procure aquele que você deseja baixar e clique no símbolo de download. Talvez seja necessário alterar o período de tempo para exibir as faturas mais antigas. Pode levar alguns minutos para gerar o arquivo de detalhes de uso e a fatura.

![Captura de ecrã que mostra os períodos de faturação, a opção de transferência e os custos totais de cada período de faturação](./media/review-individual-bill/download-invoice.png)

Na janela baixar uso + encargos, clique em **baixar CSV** e **Baixe a fatura**.

![Captura de ecrã que mostra a página Transferir fatura e utilização](./media/review-individual-bill/usageandinvoice.png)

Se não estiver **disponível** , há várias razões pelas quais você não vê os detalhes de uso ou uma fatura:

- Passaram menos de 30 dias desde o dia em que subscreveu o Azure.
- Não há uso para o período de cobrança.
- Uma fatura ainda não é gerada. Aguarde até ao fim do período de faturação.
- Não tem permissão para ver faturas. Talvez você não veja as faturas antigas, a menos que você seja o administrador da conta. Para saber mais sobre como obter acesso a informações de faturação, veja [Gerir acesso às funções de utilização de faturação do Azure](../manage/manage-billing-access.md).
- Se tiver uma Avaliação Gratuita ou um montante de crédito mensal com a sua subscrição que não excedeu, não receberá nenhuma fatura, a menos que tenha um Contrato de Cliente da Microsoft.

Em seguida, examine os encargos. Sua fatura mostra valores para impostos e seus encargos de uso.

![Exemplo de fatura do Azure](./media/review-individual-bill/invoice-usage-charge.png)

Abra o arquivo de uso CSV que você baixou. No final do arquivo, some o valor de todos os itens na coluna *custo* .

![Exemplo de arquivo de uso com custo somado](./media/review-individual-bill/usage-file-usage-charges.png)

 O valor de *custo* somado deve corresponder precisamente ao custo de *encargos de uso* em sua fatura.

Os custos de utilização são apresentados ao nível do medidor. Os termos a seguir significam a mesma coisa tanto na fatura quanto no ficheiro de utilização detalhado. Por exemplo, o ciclo de faturação na fatura é o mesmo que o período de faturação mostrado no ficheiro de utilização detalhada.

| Fatura (PDF) | Utilização detalhada (CSV)|
| --- | --- |
|Ciclo de faturação | BillingPeriodStartDate BillingPeriodEndDate |
|Nome |Categoria de Medidor |
|Tipo |Subcategoria do Medidor |
|Recurso |MeterName |
|Região |MeterRegion |
|Consumido | Quantidade |
|Incluído |Quantidade Incluída |
|A Cobrar |Quantidade de Utilização Excedente |
|Tarifa | EffectivePrice|
| Valor | Custo |

A seção **encargos de uso** de sua fatura mostra o valor total (custo) para cada medidor que foi consumido durante o período de cobrança. Por exemplo, a imagem a seguir mostra um encargo de uso para o serviço de armazenamento do Azure para o recurso de *discos P10* .

![Custos de utilização da fatura](./media/review-individual-bill/invoice-usage-charges.png)

No arquivo de uso do CSV, filtre por *medidorname* para o recurso correspondente mostrado na fatura. Em seguida, some o valor de *custo* para os itens na coluna. Veja a seguir um exemplo.

![Valor de arquivo de uso somado para Medidorname](./media/review-individual-bill/usage-file-usage-charge-resource.png)

O valor de *custo* somado deve corresponder precisamente ao custo de *encargos de uso* do recurso individual cobrado em sua fatura.

Para obter mais informações, consulte [entender sua fatura do Azure](understand-invoice.md) e [entender o uso detalhado do Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Comparar encargos e uso na análise de custo

A análise de custo no portal do Azure também pode ajudá-lo a verificar seus encargos. Para obter uma visão geral rápida do uso faturado e dos encargos, selecione sua assinatura na [página assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. Em seguida, clique em **análise de custo** e, na lista exibições, clique em **detalhes da fatura**.

![Exemplo mostrando a seleção de detalhes da fatura](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Em seguida, na lista intervalo de datas, selecione um período de tempo para a fatura. Adicione um filtro para número de fatura e, em seguida, selecione o InvoiceNumber que corresponde ao de sua fatura. A análise de custo mostra os detalhes de custo dos itens faturados.

![Exemplo mostrando detalhes de custos faturados na análise de custo](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Os custos mostrados na análise de custo devem corresponder precisamente ao custo de *encargos de uso* do recurso individual cobrado em sua fatura.

![Custos de utilização da fatura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Serviços externos faturados separadamente

Os serviços externos ou os custos do marketplacet dizem respeito aos recursos que foram criados por fornecedores de software de terceiros. Estes recursos estão disponíveis para utilização no Azure Marketplace. Por exemplo, uma Firewall Barracuda é um recurso do Azure Marketplace oferecido por terceiros. Todos os custos da firewall e os medidores correspondentes aparecem como custos de serviços externos.

Os custos de serviços externos são faturados separadamente. Os custos não aparecem na fatura do Azure. Para saber mais, veja [Compreender os custos de serviços externos do Azure](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Recursos faturados por medidores de utilização

O Azure não é fatura diretamente com base no custo do recurso. Os custos de um recurso são calculados com um ou mais medidores. Os medidores são utilizados para monitorizar a utilização de um recurso durante o seu tempo de vida. Estes medidores são utilizados para calcular a fatura.

Quando você cria um único recurso do Azure, como uma máquina virtual, ele tem uma ou mais instâncias de medidor criadas. Os medidores são utilizados para controlar a utilização do recurso ao longo do tempo. Cada medidor emite registos de utilização que são utilizados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para controlar a utilização:

- Horas de Computação
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferências de Dados de Saída
- Disco Gerido Standard
- Operações de Discos Geridos Standard
- E/S Padrão – Disco
- E/S Padrão – Leitura de Blobs de Blocos
- E/S Padrão – Escrita de Blobs de Blocos
- E/S Padrão – Eliminação de Blobs de Blocos

Uma vez criada a VM, cada medidor começa a emitir registos de utilização. Esta utilização e o preço do medidor são monitorizados no sistema de medição do Azure.

Você pode ver os medidores que foram usados para calcular sua fatura no arquivo CSV de uso.

## <a name="payment"></a>Pagar a fatura

Se você configurar um cartão de crédito como seu método de pagamento, o pagamento será cobrado automaticamente dentro de 10 dias após o término do período de cobrança. No extrator do cartão de crédito, o item de linha indicará **MSFT Azure**.

Para alterar o cartão de crédito utilizado, veja [Adicionar, atualizar ou remover um cartão de crédito do Azure](../manage/change-credit-card.md).

Se [pagar por fatura](../manage/pay-by-invoice.md), envie o pagamento para a localização indicada na parte inferior da fatura.

Para verificar o estado do pagamento, [crie um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Comparar encargos faturados com o arquivo de uso
> * Comparar encargos e uso na análise de custo

Conclua o guia de início rápido para começar a usar a análise de custo.

> [!div class="nextstepaction"]
> [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)
