---
title: Detalhes da política de pagamento - Azure Marketplace
description: Detalhes sobre as políticas de pagamento, incluindo horários e recuperação.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.date: 04/24/2020
ms.openlocfilehash: d72a80298be96d32547737a98e5279da0c5d8ef0
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324833"
---
# <a name="payout-policy-details"></a>Detalhes da política de dividendos

Este artigo discute o processo de pagamento da Microsoft, o calendário de pagamentos, onde encontrar o estado de um pagamento, e a política de recuperação.

## <a name="where-to-find-upcoming-payouts"></a>Onde encontrar os próximos pagamentos

No Partner Center, selecione **Payout** no canto superior direito do portal:

![Ilustra o ícone Payout no canto superior direito do portal Partner Center.](./media/payout-overview.png)

> [!TIP]
> Nem todas as funções da conta têm acesso a informação de pagamento. Para mais [informações, consulte Funções e permissões para aceder ao relatório de pagamento](./payout-summary-overview.md#roles-and-permissions).

## <a name="payment-schedules"></a>Horários de pagamento

As seguintes secções descrevem o nosso processo de pagamentos.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Transações do Acordo de Empresa após 1 de maio de 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Atualização para o nosso modelo de pagamento de editores de marketplace comercial

A partir de 1 de maio de 2020, estamos a atualizar a nossa política de pagamentos relativa a produtos adquiridos no Azure Marketplace ou AppSource por clientes com um Acordo Empresarial da Microsoft. Quando um cliente comprar um produto da Azure Marketplace ou appSource utilizando o seu atual Microsoft Enterprise Agreement para transações após 1 de maio de 2020, começaremos a emitir pagamentos no próximo ciclo de pagamentos de 30 dias após a fatura do cliente. As transações em que um cliente utiliza um cartão de crédito são inalteradas e continuarão a ter um período de detenção de 30 dias antes do pagamento. Estas tabelas mostram detalhes sobre o calendário de pagamentos.

> [!NOTE]
> Consulte [o Processo de não pagamento do cliente](#process-for-customer-non-payment) abaixo para as ações que tomamos se o cliente não pagar, mas já lhe emitimos um pagamento.

| Evento  | Date  | Visibilidade do parceiro: Relatório de pagamento do Partner Center  |  Visibilidade do parceiro: Partner Center analytics\* |
| --- | --- | --- | --- |
| Transação ou mês de utilização | 8/1/2020 – 8/31/2020 | N/D | **Relatório de utilização**: novo consumo mostrado (refrescado a cada quatro horas)<br>**Relatório de**encomendas : N/A |
| Fim do prazo (mês) | 8/31/2020 | N/D | **Relatório de utilização**: consumo final de mês mostrado<br>**Relatório de**encomendas : N/A |
| Ordem gerada | 9/3/2020 – 9/7/2020 | N/D | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Calcular o ganho de pagamento | 9/4/2020 – 9/10/2020 | Marcado como **não processado** no histórico de transações no painel de pagamento | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Pagamento mensal | 10/5/2020 | Marcado como Próximo na história **da** transação no painel de pagamento | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Data de pagamento | 10/15/2020 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos do Painel de Pagamentos | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Fatura do cliente recolhida | 12/1/2020 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos do Painel de Pagamentos | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE  |
|  |  |  |  |

\*Os relatórios de utilização e ordem estão acessíveis na secção Analisar no Centro parceiro.

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Clientes que pagam com cartão de crédito ou fatura

Todas as compras com cartão de crédito ou fatura mensal têm um período de detenção de 30 dias para garantir que os fundos são apurados e não há cobranças ou suspeitas de fraude.

| Evento  | Date  | Visibilidade do parceiro: Relatório de pagamento do Partner Center  |  Visibilidade do parceiro: Partner Center analytics\*  |
| --- | --- | --- | --- |
| Transação ou mês de utilização | 8/1/2019 - 8/31/2019 | N/D | **Relatório de utilização**: novo consumo mostrado (refrescado a cada quatro horas)<br>**Relatório de**encomendas : N/A |
| Fim do prazo (mês) | 8/31/2019 | N/D | **Relatório de utilização**: consumo final de mês mostrado<br>**Relatório de**encomendas : N/A |
| Ordem gerada | 9/3/2019 – 9/7/2019 | N/D | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Fatura do cliente recolhida | 9/7/2019 – 9/10/2019 | N/D | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Calcular o pagamento | 9/8/2019 -9/12/2019 | Marcado como **não processado** no histórico de transações no painel de pagamento | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Pagamento mensal | 11/5/2019\* | Marcado como **O Próximo** no Histórico de Transações no Painel de Pagamentos | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Data de pagamento | 11/15/2019 | Marcado como **Enviado** no Histórico de Transações e na secção Pagamentos no Painel de Pagamentos | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
|  |  |  |  |

\*Os relatórios de utilização e ordem estão acessíveis na secção Analisar no Centro parceiro.

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>Transações do Contrato de Empresa antes de 1 de maio de 2020

Todas as compras que ocorrem antes desta data são processadas e pagas de acordo com o calendário abaixo, depois de a Microsoft ter recolhido o pagamento dos clientes e processado a taxa de mercado.

| Evento  | Date  | Visibilidade do parceiro: Relatório de pagamento do Partner Center  |  Visibilidade do parceiro: Partner Center analytics\*  |
| --- | --- | --- | --- |
| Transação ou mês de utilização | 8/1/2019 – 8/31/2019 | N/D | **Relatório de utilização**: novo consumo mostrado (refrescado a cada quatro horas)<br>**Relatório de**encomendas : N/A |
| Fim do prazo (mês) | 8/31/2019 | N/D | **Relatório de utilização**: consumo final de mês mostrado<br>**Relatório de**encomendas : N/A |
| Ordem gerada | 9/3/2019 – 9/7/2019 | N/D | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Fatura do cliente recolhida | 12/1/2019 | N/D | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Calcular o pagamento | 12/5/2019 –12/7/2019 | Marcado como **não processado** no histórico de transações no painel de pagamento | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Pagamento mensal | 1/5/2019 | Marcado como **O Próximo** na história da transação no painel de pagamentos | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
| Data de pagamento | 1/15/2019 | Marcado como **Enviado** no Histórico de Transações e na secção pagamentos no Painel de Pagamentos | **Relatório de utilização**: consumo mostrado com OrderID/OrderLineItemID<br>**Relatório do pedido**: encomendas de clientes mostradas como ATIVE |
|  |  |  |  |

\*Os relatórios de utilização e ordem estão acessíveis na secção Analisar no Centro parceiro.

## <a name="process-for-customer-non-payment"></a>Processo de não pagamento de clientes

Em raras ocasiões, a Microsoft não consegue cobrar pagamentos aos clientes pelas suas compras no mercado comercial. Quando um cliente não paga à Microsoft de acordo com o seu calendário de faturação, iniciamos o processo de cobranças. Este processo demora aproximadamente quatro meses e envolve uma comunicação persistente da Microsoft. Se o pagamento não for recebido até ao final deste processo, a Microsoft anula os fundos como incobráveis.

De acordo com o processo de pagamento aqui articulado, a Microsoft pode já ter pago fundos a editores (você) que são, em última análise, incobráveis. Portanto, temos um processo de conciliação destes montantes. Para garantir que tem avisado que o seu (já recebido) pagamento pode ser reconciliado, será notificado quando um cliente estiver no processo de cobranças e as compras são suscetíveis de ser amortizadas.

A Microsoft recuperará quaisquer pagamentos já pagos utilizando um dos seguintes métodos: (1) A Microsoft pode subtrair os montantes não pagos de pagamentos futuros; por exemplo, se $1.000 em pagamentos forem considerados incobráveis e anulados, os seus pagamentos futuros serão retidos até que os $1.000 sejam recuperados, ou (2) a Microsoft pode solicitar um reembolso ou editores de fatura para quaisquer valores não cobrados.

Segue-se um exemplo de calendário:

| Evento | Data aproximada | Visibilidade do parceiro |
| --- | --- | --- |
| Data de pagamento de exemplo | 10/15/2020 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos no Painel de Pagamentos |
| <font color="red">Se o cliente não pagar à Microsoft</font> | 12/2/2020 – 12/5/2020 | Sem mudanças, como acima |
| Cliente recebe primeiro e-mail de pagamento em atraso | 12/6/2020 | Nenhum |
| Cliente recebe e-mails regulares de urgência crescente | 12/7/2020 – 1/31/2021 | Nenhum |
| Editor é notificado write-off é provável | 1/7/2021 | E-mail notificação enviada ao editor que o seu cliente ainda não enviou o pagamento. O ID de transação e o valor em dólares estão incluídos. |
| Cliente recebe aviso de rescisão | 2/1/2021 | Nenhum |
| Fim do processo de recolha / os fundos são anulados | 2/15/2021 | E-mail notificação enviada ao editor de que os fundos foram anulados. O ID de transação e o valor em dólares estão incluídos. |
| O pagamento é deduzido | 3/1/2021 | Editor verá transação negativa em Declaração de Pagamento do Partner Center |
| O pagamento é retido | 3/15/2021 | Os pagamentos futuros serão mostrados na Declaração de Pagamento do Centro Parceiro. A editora não receberá o pagamento até que o saldo não seja mais negativo.  |
|||

## <a name="next-step"></a>Próximo passo

- [Detalhes fiscais](./tax-details-paid-transactions.md)
