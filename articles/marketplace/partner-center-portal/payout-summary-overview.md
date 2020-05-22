---
title: Resumo do resumo do pagamento - Azure Marketplace
description: O resumo do Pagamento mostra-lhe detalhes sobre o dinheiro que ganhou com a sua oferta. Também lhe permite saber quando receberá os pagamentos e quanto será pago.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: a872331238946de0d57e6d42164f1ce7fb1c7357
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746210"
---
# <a name="payout-summary-overview"></a>Descrição geral do resumo de dividendos

O resumo do [Pagamento](./payout-summary.md) mostra-lhe detalhes sobre o dinheiro que ganhou com a Microsoft. Também lhe permite saber quando receberá os pagamentos e quanto será pago.

Se vender ofertas no Azure Marketplace, também verá informações sobre pagamentos bem sucedidos no resumo do Pagamento. Para obter mais informações sobre o pagamento do Azure Marketplace, consulte as políticas de [participação do Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e o [Microsoft Azure Marketplace Publisher Agreement](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Para ser elegível para pagamento, as suas receitas devem atingir o limiar de [pagamento](./payment-thresholds-methods-timeframes.md) de $50. Para mais detalhes sobre o limiar de pagamento, consulte o Acordo de Editor estoiro do [Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

Todos os relatórios e insights para a opção de publicação transacte estão disponíveis na secção Analytics do Partner Center, acessado usando este ícone no canto superior direito do portal:

![Ilustra o ícone do Pagamento no canto superior direito do portal Partner Center.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Funções e permissões

Estas são funções e permissões para aceder ao relatório de pagamento:

| Relatórios/Páginas | Proprietário de Conta | Gestor | Programador | Contribuinte de negócios | Contribuinte financeiro | Marketer |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de aquisição (incluindo dados quase em tempo real) | Pode ver | Pode ver | Sem acesso | Sem acesso | Pode ver | Sem acesso |
| Relatório/respostas de feedback | Pode ver e enviar feedback | Pode ver e enviar feedback | Pode ver e enviar feedback | Sem acesso | Sem acesso | Pode ver e enviar feedback |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de saúde (incluindo dados quase em tempo real) | Pode ver | Pode ver | Pode ver | Pode ver | Sem acesso | Sem acesso |
| Relatório de utilização | Pode ver | Pode ver | Pode ver | Pode ver | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil fiscal | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo dos dividendos | Pode ver | Sem acesso | Sem acesso | Sem acesso | Pode ver | Sem acesso  |
| | | | | | | |

## <a name="payout-report-differences"></a>Diferenças no relatório de pagamento

Estas são as diferenças no relatório de pagamento entre cloud partner portal (antigo) e Partner Center (novo):

| Portal de Parceiros da Cloud | Centro de Parceiros |
| --- | --- |
| **Ligação:**https://cloudpartner.azure.com/ | **Ligação**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory ehttps://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navegação**: Relatório de pagamento fornecido no Pagamento de Insights | **Navegação**: Relatório de pagamento fornecido no Partner Center – Ícone de Pagamento |
| **Âmbito:**<ul><li>A transação por linha é visível, para cobrança em curso, recolhida e paga.</li><li>Reporte – mostra todos os itens de linha uma vez que a encomenda de compra é criada, incluindo a recolha em andamento e faturação em andamento, e o estado de cobrança e itens de linha que ainda não são elegíveis para serem pagos.</li></ul> | **Âmbito:**<ul><li>Mostra os itens da linha depois de serem considerados ganhos elegíveis.</li><li>Os clientes pagam primeiro à Microsoft, e depois os ISVs podem ver o relatório de pagamento a começar.</li><li>O relatório de pagamento não mostrará a cobrança em andamento e a faturação em Progresso.</li></ul> |
| **Transação não está pronta para pagamento**: Faturação em Curso | **Transação não está pronta para pagamento**: Próximo pagamento estimado: O estado de pagamento está no estado não processado. |
| **Estado do pagamento**: n/a | **Estado do pagamento:**<ul><li>Não processado: O ganho é elegível para pagamento.</li><li>A seguir: Os ganhos serão enviados para a editora no próximo pagamento mensal.</li><li>Enviado: O pagamento foi enviado para o seu banco.</li></ul> |
| | |

## <a name="payment-schedules"></a>Horários de pagamento

Para uma discussão sobre os horários de pagamento, incluindo períodos de retenção, visibilidade do parceiro, e quando o cliente utilizar um cartão de crédito ou fatura, consulte a secção de [horários](./payout-policy-details.md#payment-schedules) de pagamento do tópico de detalhes do **Pagamento.**

## <a name="transaction-history-download-export"></a>Transferência de transferência de histórico de transações

Esta opção fornece um download de cada item de linha de ganho que você vê na página de histórico de Transações. Isto inclui o tipo de ganho, data, valor de transação associado, cliente, produto e outros detalhes transacionais relacionados com o programa Incentivos.

| Nome da coluna | Descrição |
| --- | --- |
| earningId | Identificador único para cada ganho |
| participanteId | A identidade primária do sócio que ganha ao abrigo do programa |
| participanteIdType | Id do programa para programas de incentivo e vendedor se o programa for para programas de loja e Mercado Azure |
| nome de participante | Nome do parceiro de ganhos |
| parceiroCountryCode | Localização/país/região do parceiro de ganhos |
| programaNome | Nome do programa de incentivo/loja |
| transaçãoId | Identificador único para a transação |
| transaçãoMoeda | Moeda em que ocorreu a transação original do cliente (não é a moeda de localização do parceiro) |
| transacçãoData | Data da transação. Útil para programas onde muitas transações contribuem para um ganho |
| transacçõesExchangeRate | Taxa de câmbio utilizada para mostrar o valor de usd transação correspondente |
| transaçõesMontante | Montante de transação na moeda de transação original com base no qual o resultado é gerado |
| transaçõesAmountUSD | Valor da transação em dólares americanos (USD) |
| alavanca | Regra do negócio para os ganhos |
| taxa de ganho | Taxa de incentivo aplicada no valor da transação para gerar um ganho |
| quantidade | Quantidade cobrada para programas transacionais. Varia com base no programa |
| quantidadeTipo de letra | Tipo de quantidade, por exemplo: Quantidade faturada, Utilizadores Ativos Mensais (MAU) |
| earningType | Indica se é taxa, desconto, galinheiro, venda, e assim por diante |
| ganhoS | Valor de Ganho na moeda de transação original |
| ganhoSAmountUSD | Valor de Ganho em USD |
| data de ganhos | Data do ganho |
| cálculoDatade | Data em que o ganho foi calculado no sistema |
| earningExchangeRate | Taxa de câmbio utilizada para mostrar o valor correspondente do USD |
| exchangeRateDate | Data de câmbio utilizada para calcular o Valor Ganho USD |
| pagamentoAmountWOTax | Valor de ganho (sem imposto) em Pagamento apenas para &quot; &quot; pagamentos enviados |
| pagamentoMoeda | Pague à moeda escolhida pelo parceiro no perfil de Pagamento. Mostrado apenas para pagamentos enviados |
| pagamentoSExchangeRate | Taxa de câmbio utilizada para calcular pagamentoAmountWOTax em moeda de pagamento utilizando ExchangeRateDate |
| pagamentoId | Identificador único para o pagamento. Este número é visível no seu extrato bancário |
| pagamentoEstatuto | Estado dos pagamentos |
| pagamentoStatusDescription | Descrição do estado do pagamento |
| customerId | Sempre estará em branco |
| nome do cliente | Sempre estará em branco |
| partNumber | Sempre estará em branco |
| nome do produto | Nome do produto ligado à transação |
| productId | Identificador de produto único |
| parentProductId | Identificador de produto parental único. Se não houver um produto-mãe para a transação, então o ID do Produto-Mãe = ID do produto. |
| nome do produto parental | Nome do produto-mãe. Se não houver um produto-mãe para a transação, então nome do produto-mãe = Nome do produto. |
| productType | Tipo de produto (como App, Add-on e Game) |
| faturaNúmero | Número de fatura (apenas para Acordos Empresariais) |
| revendedorId | Identificador de revendedor |
| revendedorNome | Nome do revendedor |
| transaçãoTipo | Tipo de transação (como compra, reembolso, reversão e cobrança) |
| fornecedor localVendedor | Fornecedor local/vendedor de registos |
| impostosRedos | Montante do imposto remetido (impostos sobre vendas, utilização ou IVA/GST). |
| taxRemitModel | Parte responsável pela reemissão de impostos (impostos sobre vendas, utilização ou IVA/GST). |
| lojaFee | O valor retido pela Microsoft como uma taxa para disponibilizar a app ou adicionar no mercado comercial. |
| transacçãoPaymentMethod | Instrumento de pagamento ao cliente utilizado para a transação (como Card, Mobile Carrier Billing e PayPal) |
| tpan | Rede de anúncios de terceiros |
| clientePaís | País/região cliente |
| clienteCity | Cidade cliente |
| clienteEstado | Estado do cliente |
| clienteZip | Código postal/postal do cliente |
| TenantID | A identificação do Inquilino |
| externalReferenceId | Identificador único para o programa |
| externalReferenceIdLabel | Etiqueta de identificador única |
| transaçõesCountryCode | Código país/região em que a transação aconteceu |
| impostosPaís | País/região do cliente |
| impostosEstado | Estado do cliente |
| taxCity | Cidade do cliente |
| taxZipCode | Código postal/postal do cliente |
| Nome do Programa de Licenciamento | Nome do programa de licenciamento |
| Código do Programa | Corda para mapear com o nome do programa |
| ganhoAmountInLastPaymentCurrency | Valor dos ganhos na última moeda de pagamento (campo ficará vazio se não tiverem sido pagos pagamentos prévios) |
| últimaMoeda de pagamento | Última moeda de pagamento (campo ficará vazio se não tiver sido pago qualquer pagamento prévio) |
| Assetid | O identificador único para as encomendas do cliente para o seu serviço de marketplace. Representa os itens da linha de compra. Pode haver vários bens. |
| OrderId | Refere-se à fatura de um cliente |
| LineItemId | Linha individual na fatura de um cliente |
| País/Região cliente | O nome país/região fornecido pelo cliente. Isto poderia ser diferente do país/região na Subscrição Azure de um cliente. |
| Endereço de e-mail do cliente | O endereço de e-mail fornecido pelo cliente. Isto poderia ser diferente do endereço de e-mail na Subscrição Azure de um cliente. |
| SkuId | SKU ID conforme definido durante a publicação. Uma oferta pode ter muitos SKUs, mas um SKU só pode ser associado a uma única oferta. |

> [!NOTE]
> Todos os relatórios e insights para a opção de publicação transata podem ser encontrados na secção Analytics do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio
Para suporte de faturação, contacte o suporte da [editora](https://partner.microsoft.com/support/v2/?stage=1)de marketplace comercial.

## <a name="next-step"></a>Próximo passo

- [Resumos de dividendos](./payout-summary.md)
