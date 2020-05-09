---
title: Detalhes da política de pagamento - Azure Marketplace
description: Detalhes relativos às políticas de pagamento, incluindo horários e recuperação.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6aa40d0914237a28c7bbd32b15bf0d8d02140192
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783555"
---
# <a name="payout-policy-details"></a>Detalhes da política de pagamento

Este artigo discute o processo de pagamento da Microsoft, o calendário de pagamentos, onde encontrar o estado de um pagamento e a política de recuperação.

## <a name="where-to-find-upcoming-payouts"></a>Onde encontrar os próximos pagamentos

No Partner Center, selecione **Payout** na parte superior direita do portal:

![Ilustra o ícone do Pagamento na parte superior direita do portal Partner Center.](./media/payout-overview.png)

> [!TIP]
> Nem todas as funções de conta têm acesso a informações de pagamento. Para mais detalhes, consulte [Papéis e permissões para aceder ao relatório de pagamento](./payout-summary.md).

## <a name="payment-schedules"></a>Horários de pagamento

As seguintes secções descrevem o nosso processo de pagamentos.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Contrato empresarial transações após 1 de maio de 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Atualização para o nosso modelo de pagamento de editores do Mercado Comercial

A partir de 1 de maio de 2020, estamos a atualizar a nossa política de pagamentos relativa a produtos comprados no Azure Marketplace ou appSource por clientes com um Acordo microsoft enterprise. Quando um cliente comprar um produto do Azure Marketplace ou appSource usando o seu atual Microsoft Enterprise Agreement para transações após 1 de maio de 2020 começaremos a emitir pagamentos no próximo ciclo de pagamento sem fatura de 30 dias. As transações em que um cliente usa um cartão de crédito permanecem inalteradas e continuarão a ter um período de detenção de 30 dias antes do pagamento. Estas tabelas mostram detalhes sobre o calendário de pagamentos.

> [!NOTE]
> Consulte o [Processo para o não pagamento](#process-for-customer-non-payment) do cliente abaixo pelas ações que tomamos se o cliente não pagar, mas já lhe emitimos um pagamento.

| Evento  | Date  | Visibilidade do parceiro: Relatório de pagamento do Partner Center  |  Visibilidade do parceiro: Análise do Partner Center\* |
| --- | --- | --- | --- |
| Transação ou mês de utilização | 8/1/2020 – 8/31/2020 | N/D | **Relatório de utilização**: novo consumo mostrado (atualizado a cada quatro horas)<br>**Relatório de encomenda**: N/A |
| Fim do prazo (mês) | 8/31/2020 | N/D | **Relatório de utilização**: consumo final mensal mostrado<br>**Relatório de encomenda**: N/A |
| Ordem gerada | 9/3/2020 – 9/7/2020 | N/D | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Calcular os ganhos de pagamento | 9/4/2020 – 9/10/2020 | Marcado como **Não processado** no Histórico de Transações no Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Pagamento mensal | 10/5/2020 | Marcado como **Próximo** na História da Transação no Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Data de pagamento | 10/15/2020 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos do Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Fatura do cliente recolhida | 12/1/2020 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos do Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE  |
|  |  |  |  |

\*Os relatórios de utilização e ordem estão acessíveis na secção Análise no Centro parceiro.

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Clientes que pagam usando cartão de crédito ou fatura

Todas as compras com cartão de crédito ou fatura mensal têm um período de detenção de 30 dias para garantir que os fundos são limpos e não há cobranças ou suspeitas de fraude.

| Evento  | Date  | Visibilidade do parceiro: Relatório de pagamento do Partner Center  |  Visibilidade do parceiro: Análise do Partner Center\*  |
| --- | --- | --- | --- |
| Transação ou mês de utilização | 8/1/2019 - 8/31/2019 | N/D | **Relatório de utilização**: novo consumo mostrado (atualizado a cada quatro horas)<br>**Relatório de encomenda**: N/A |
| Fim do prazo (mês) | 8/31/2019 | N/D | **Relatório de utilização**: consumo final mensal mostrado<br>**Relatório de encomenda**: N/A |
| Ordem gerada | 9/3/2019 – 9/7/2019 | N/D | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Fatura do cliente recolhida | 9/7/2019 – 9/10/2019 | N/D | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Calcular o pagamento | 9/8/2019 -9/12/2019 | Marcado como **Não Processado** no Histórico de Transações no Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Pagamento mensal | 11/5/2019\* | Marcado como **Próximo** no Histórico de Transações no Painel de Pagamento | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Data de pagamento | 11/15/2019 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos no Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
|  |  |  |  |

\*Os relatórios de utilização e ordem estão acessíveis na secção Análise no Centro parceiro.

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>Transações do Contrato Empresarial antes de 1 de maio de 2020

Todas as compras que ocorram antes desta data são processadas e pagas de acordo com o horário abaixo, depois de a Microsoft ter cobrado o pagamento aos clientes e processado a taxa de mercado.

| Evento  | Date  | Visibilidade do parceiro: Relatório de pagamento do Partner Center  |  Visibilidade do parceiro: Análise do Partner Center\*  |
| --- | --- | --- | --- |
| Transação ou mês de utilização | 8/1/2019 – 8/31/2019 | N/D | **Relatório de utilização**: novo consumo mostrado (atualizado a cada quatro horas)<br>**Relatório de encomenda**: N/A |
| Fim do prazo (mês) | 8/31/2019 | N/D | **Relatório de utilização**: consumo final mensal mostrado<br>**Relatório de encomenda**: N/A |
| Ordem gerada | 9/3/2019 – 9/7/2019 | N/D | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Fatura do cliente recolhida | 12/1/2019 | N/D | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Calcular o pagamento | 12/5/2019 –12/7/2019 | Marcado como **Não Processado** no Histórico de Transações no Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Pagamento mensal | 1/5/2019 | Marcado como **Próximo** na história da transação no Painel de Pagamento | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
| Data de pagamento | 1/15/2019 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos no Painel de Pagamentos | **Relatório de utilização**: consumo apresentado com OrderID/OrderLineItemID<br>**Relatório de encomenda**: pedidos de clientes apresentados como ATIVE |
|  |  |  |  |

\*Os relatórios de utilização e ordem estão acessíveis na secção Análise no Centro parceiro.

## <a name="process-for-customer-non-payment"></a>Processo para não pagamento do cliente

Em raras ocasiões, a Microsoft não pode cobrar pagamentos dos clientes para as suas compras no mercado comercial. Quando um cliente não paga à Microsoft de acordo com o seu calendário de faturação, iniciamos o processo de cobrança. Este processo demora aproximadamente quatro meses e envolve uma comunicação persistente da Microsoft. Se o pagamento não for recebido até ao final deste processo, a Microsoft considera os fundos incobráveis.

De acordo com o processo de pagamento aqui articulado, a Microsoft pode já ter pago fundos a editores (você) que são, em última análise, incobráveis. Portanto, temos um processo de conciliação destes montantes. Para garantir que tem aviso de que o seu pagamento (já recebido) pode ser reconciliado, será notificado quando um cliente estiver no processo de cobrança e as compras são suscetíveis de ser canceladas.

A Microsoft recuperará quaisquer pagamentos já pagos utilizando um dos seguintes métodos: (1) a Microsoft pode subtrair os montantes não pagos de pagamentos futuros; por exemplo, se $1.000 em pagamentos forem considerados incobráveis e anulados, os seus pagamentos futuros serão retidos até que os $1.000 sejam recuperados, ou (2) a Microsoft pode solicitar um reembolso ou editores de faturas por quaisquer montantes não cobrados.

Segue-se um calendário de exemplo:

| Evento | Data aproximada | Visibilidade do parceiro |
| --- | --- | --- |
| Data de pagamento de exemplo | 10/15/2020 | Marcado como **Enviado** no Histórico de Transações e na secção de Pagamentos no Painel de Pagamentos |
| <font color="red">Se o cliente não pagar à Microsoft</font> | 12/2/2020 – 12/5/2020 | Sem alterações, como acima |
| Cliente recebe primeiro e-mail de pagamento atrasado | 12/6/2020 | Nenhum |
| Cliente recebe e-mails regulares de crescente urgência | 12/7/2020 – 1/31/2020 | Nenhum |
| Editora é notificada de que a anulação é provável | 1/7/2020 | Notificação por e-mail enviada à editora que o seu cliente ainda não enviou o pagamento. O ID de transação e o valor do dólar estão incluídos. |
| Cliente recebe aviso de rescisão | 2/1/2020 | Nenhum |
| Fim do processo de recolha/fundos são anulados | 2/15/2020 | Notificação por e-mail enviada à editora de que os fundos foram anulados. O ID de transação e o valor do dólar estão incluídos. |
| O pagamento é deduzido | 3/1/2020 | Editora verá transação negativa na Declaração de Pagamento do Partner Center |
| O pagamento é retido | 3/15/2020 | Os pagamentos futuros serão apresentados na Declaração de Pagamento do Partner Center. A editora não receberá o pagamento até que o saldo não seja mais negativo.  |
|||

## <a name="next-step"></a>Próximo passo

- [Detalhes fiscais](./tax-details-paid-transactions.md)
