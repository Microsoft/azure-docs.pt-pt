---
title: Resumo do pagamento do mercado comercial Mercado Azure
description: O resumo do Pagamento mostra-lhe detalhes sobre o dinheiro que ganhou com a sua oferta. Também lhe permite saber quando receberá os pagamentos e quanto será pago.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 5a930dfb38007349155581424d03ee7b3e7a6b46
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730210"
---
# <a name="payout-reporting"></a>Relatórios de pagamento

O resumo do [**Pagamento**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) mostra-lhe detalhes sobre o dinheiro que ganhou com a Microsoft. Também lhe permite saber quando receberá os pagamentos e quanto será pago.

Se vender ofertas no Azure Marketplace, também verá informações sobre pagamentos bem sucedidos no resumo do **Pagamento.** Para obter mais informações sobre o pagamento do Azure Marketplace, consulte as Políticas de [Participação no Mercado do Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkId=722436) e o Acordo de [Editor estonteante Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Para ser elegível para pagamento, as suas receitas devem atingir o limiar de [pagamento](payment-thresholds-methods-timeframes.md) de $50. Para mais detalhes sobre o limiar de pagamento consulte esta página e reveja o Acordo de Editor estoiro do [Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Funções e permissão para aceder ao relatório de pagamento](#roles-and-permission-to-access-the-payout-report)
- [Relatório de pagamento: diferença entre Cloud Partner Portal e Partner Center](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Tipos de clientes](#customer-types)
- [Corelação entre pagamento e utilização](#corelation-between-payout-and-usage)
- [Download de histórico de transações](#transaction-history-download-export)
- [Perguntas de faturação e apoio](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Funções e permissão para aceder ao relatório de pagamento

| Relatórios/Páginas    | Proprietário de Conta    | Gestor  | Programador | Contribuinte de negócios |  Contribuinte financeiro | Marketer |
|------------------|------------------|----------|-----------|----|----|-----|
| Relatório de aquisição (incluindo dados de tempo próximo em tempo real) | Pode ver | Pode ver | Sem acesso | Sem acesso | Pode ver | Sem acesso |
| Relatório/respostas de feedback | Pode ver e enviar feedback | Pode ver e enviar feedback | Pode ver e enviar feedback | Sem acesso | Sem acesso | Pode ver e enviar feedback |
| Relatório de saúde (incluindo dados em tempo real) | Pode ver | Pode ver | Pode ver | Pode ver | Sem acesso | Sem acesso |
| Relatório de utilização | Pode ver | Pode ver | Pode ver | Pode ver | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil fiscal | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo dos dividendos | Pode ver | Sem acesso | Sem acesso | Sem acesso | Pode ver | Sem acesso |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Relatório de pagamento: diferença entre Cloud Partner Portal e Partner Center

| | Portal de Parceiros da Cloud | Centro de Parceiros |
|---------|---------|---------|
| Ligações | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)e[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navegação | Relatóriode pagamento fornecido no Pagamento de Insights | Relatório de pagamento fornecido no Partner Center – Ícone de pagamento |
| Âmbito | <ul> <li>Transação por rubrica é visível, para cobrança em curso, recolhida e paga </li> <li>Reporte – mostra todos os itens de linha uma vez que a encomenda de compra é criada, incluindo a recolha em andamento e faturação em Progresso, e o estado de cobrança e itens de linha que ainda não são elegíveis para serem pagos. </li> </ul> | <ul> <li>Mostra os itens de linha uma vez que são considerados como ganhos elegíveis.</li> <li>Os clientes pagam primeiro à Microsoft, e depois os ISVs podem ver o relatório de pagamento a começar.</li> <li>O relatório de pagamento não mostrará a cobrança em curso e a faturação em curso.  </li> </ul>  |
| Transação não está pronta para pagamento | Faturação em Progresso | Próximo pagamento estimado: O estado do pagamento está no estado não processado.  |
| Estado do pagamento |  | Não processado: <br> O ganho é elegível para pagamento. Permanece neste estado por um período de arrefecimento, tal como definido no guia do programa para o programa incentivo. <br> <br> A seguir: <br> Revisão interna gerada por ordem de pagamento antes de o pagamento ser processado. <br> <br> Enviado: <br> O pagamento foi enviado para o seu banco. |

## <a name="customer-types"></a>Tipos de clientes

### <a name="enterprise-agreement"></a>Acordo de empresa

Os clientes sem um Acordo de Empresa em vigor são cobrados mensalmente para licenças de software de marketplace. Os clientes com Um Acordo Empresarial são cobrados mensalmente através de uma fatura que é apresentada trimestralmente.

### <a name="credit-cards-and-monthly-invoice"></a>Cartões de crédito e fatura mensal

Os clientes também podem pagar usando um cartão de crédito e uma fatura mensal. Neste caso, as suas taxas de licença de software serão cobradas mensalmente.

### <a name="csp-and-direct-pay-users"></a>Utilizadores de CSP e Pagamento Direto

Por exemplo, se o cliente comprar usando um cartão de crédito.

## <a name="corelation-between-payout-and-usage"></a>Corelação entre pagamento e utilização

|Descrição    |    Date  | Encomendas/Utilização  | Pagamento |
|----------|----------|-----------|-------------|
|Período de encomenda   | 15 de agosto de 2019 - 30 de agosto de 2019 | **Ordens de Atributos correlaçãois** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Utilização** <br> <ul> <li>CustomerId </li> <li>Nome do cliente</li> <li>(Referência de utilização) BuyRecordId/LineItemId</li> <li> Carga alargada estimada <br> Pagamento Estimado (PC) </li> </ul> |  |
|Fim do Prazo (mês)   | 30 de agosto de 2019 | | |
|Data de faturação | 1 de setembro de 2019 | | |
|Data de Pagamento do Cliente | 1 de setembro de 2019 | | |
|Período de caução (apenas cartões de crédito, 30 dias) | 1 de setembro de 2019 - 30 set, 2019 | | **Correlação atribui ordens:** <br> <ul><li>Assetid</li> <li>ID de Cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>Assetid</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçõesMontante</li> <li>Valor InLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** Não processado |
|Início do Período de Recolha | 1 de setembro de 2019 | | |
|Fim do Período de Recolha (máximo, 30 dias) | 30 de setembro de 2019 | | |
|Data de Cálculo do Pagamento (mensalmente no dia 15) | 1 de outubro de 2019 | | **Atributos de Correlação** <br> <ul><li>Assetid</li> <li>ID de Cliente</li> <li>Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>Assetid</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçõesMontante</li> <li>Valor InLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** A seguir |
|Data de Pagamento | 15 out, 2019 | | **Atributos de Correlação** <br> <ul><li>Assetid</li> <li>ID de Cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>Assetid</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçõesMontante</li> <li>Valor InLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** Pagamento enviado |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Acordo de empresa (clientes trimestrais/mensais)

| Descrição |    Date  | Utilização | Pagamento |
|----------|----------|---------|-----------|
|Período de encomenda | 15 de agosto de 2019 - 30 de agosto de 2019 | **Correlação atribui ordens** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Relatório de utilização** <br> <ul> <li>CustomerId </li> <li>Nome do cliente</li> <li>(Referência de utilização) BuyRecordId/LineItemId</li> <li> Carga alargada estimada <br> Pagamento Estimado (PC) </li> </ul> | |
|Fim do Prazo (trimestre) | 30 de setembro de 2019 | | |
|Data de faturação | 15 out, 2019 | | |
|Período de caução (apenas cartões de crédito, 30 dias) | n/d | | |
|Início do Período de Recolha | 15 out, 2019 | | |
|Cartões de crédito apenas, 30 dias | Nov 1, 2019 - nov 30, 2019 | | |
|Fim do Período de Recolha (máximo, 90 dias) | 15 de janeiro de 2020 | | |
|Data de Pagamento do Cliente | 30 de dezembro de 2019 | | |
|Cálculo do pagamento | 15 de janeiro de 2020 | | |
|Data de Pagamento | Fev 15, 2020 | | **Para clientes trimestrais** <br> <br> **Relatório de encomendas** <br> <ul><li>Assetid</li> <li>ID de Cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>Assetid</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transaçõesMontante</li> <li>Valor InLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** enviado |

## <a name="transaction-history-download-export"></a>Transferência de transferência de histórico de transações

Esta opção fornece um download de cada item de linha de ganho que você vê na página de histórico de Transações, tipo de ganho, data, valor de transação associado, cliente, produto e outros detalhes transacionais aplicáveis ao programa Deincentivos.

| Nome da coluna     | Descrição    |
|-------------|-------------------------------|
| earningId                      | Identificador único para cada ganho                                                                                                       |
| participanteId                  | A identidade primária do sócio que ganha ao abrigo do programa                                                                            |
| participanteIdType              | Id do programa principal para programas de incentivo e vendedor IF para programas de loja e Mercado Azure                                          |
| nome de participante                | Nome do parceiro de ganhos                                                                                                              |
| parceiroCountryCode             | Localização/país do parceiro de ganhos                                                                                                  |
| programaNome                    | Nome do programa de incentivo/loja                                                                                                             |
| transaçãoId                  | Identificador único para a transação                                                                                                    |
| transaçãoMoeda            | Moeda em que ocorreu a transação original do cliente (não é a moeda de localização do parceiro)                                     |
| transacçãoData                | Data da transação. Útil para programas onde muitas transações contribuem para um ganho                                           |
| transacçõesExchangeRate        | Taxa de câmbio utilizada para mostrar o valor de usd transação correspondente                                                                 |
| transaçõesMontante              | Montante de transação na moeda de transação original com base no qual o resultado é gerado                                              |
| transaçõesAmountUSD           | Valor da transação em USD                                                                                                                |
| alavanca                          | Indica regra de negócio para o ganho                                                                                                  |
| taxa de ganho                    | Taxa de incentivo aplicada no valor da transação para gerar um ganho                                                                      |
| quantidade                       | Varia com base no programa. Indica quantidade faturada para programas transacionais                                                            |
| quantidadeTipo de letra                   | Indica tipo de quantidade, por exemplo: Quantidade faturada, MAU                                                                                     |
| earningType                    | Indica se é taxa, desconto, galinheiro, venda etc.                                                                                          |
| ganhoS                  | Valor de Ganho na moeda de transação original                                                                                      |
| ganhoSAmountUSD               | Valor de Ganho em USD                                                                                                                    |
| data de ganhos                    | Data do ganho                                                                                                                      |
| cálculoDatade                | Data em que o ganho foi calculado no sistema                                                                                            |
| earningExchangeRate            | Taxa de câmbio utilizada para mostrar o valor correspondente do USD                                                                                  |
| exchangeRateDate               | Data de câmbio utilizada para calcular o Valor Ganho USD                                                                                   |
| pagamentoAmountWOTax             | Valor de ganho (sem imposto) em Pagamento apenas para pagamentos "enviados"                                                                 |
| pagamentoMoeda                | Pague à moeda escolhida pelo parceiro no perfil de Pagamento. Mostrado apenas para pagamentos enviados                                                   |
| pagamentoSExchangeRate            | Taxa de câmbio utilizada para calcular pagamentoAmountWOTax em moeda de pagamento utilizando ExchangeRateDate                                            |
| pagamentoId            | Identificador único para o pagamento. Este número é visível no seu extrato bancário                                            |
| pagamentoEstatuto            | Estado de pagamento                                            |
| pagamentoStatusDescription            | Descrição amigável do estado do pagamento                                            |
| customerId                     | Sempre estará em branco                                                                                                                     |
| nome do cliente                   | Sempre estará em branco                                                                                                                     |
| partNumber                     | Sempre estará em branco                                                                                                                     |
| nome do produto                    | Nome do produto ligado à transação                                                                                                       |
| productId                      | Identificador de produto único                                                                                                                |
| parentProductId                | Identificador de produto parental único. Nota: se não houver um produto-mãe para a transação, então o ID do Produto-Mãe = ID do Produto. |
| nome do produto parental              | Nome do produto-mãe. Nota: se não houver um produto-mãe para a transação, então nome do produto-mãe = Nome do produto.   |
| productType                    | Tipo de produto (como App, Add-on, Game, etc.)                                                                                        |
| faturaNúmero                  | Número de fatura (aplicável apenas para EA)                                                                                                  |
| revendedorId                     | Identificador de revendedor                                                                                                                      |
| revendedorNome                   | Nome do revendedor                                                                                                                            |
| transaçãoTipo                | Tipo de transação (como compra, reembolso, reversão, cobrança, etc.)                                                               |
| fornecedor localVendedor            | Fornecedor local/vendedor de registos                                                                                                          |
| impostosRedos                    | Montante do imposto remetido (impostos sobre vendas, utilização ou IVA/GST).                                                                                   |
| taxRemitModel                  | Parte responsável pela reemissão de impostos (impostos sobre vendas, utilização ou IVA/GST).                                                                    |
| lojaFee                       | O valor retido pela Microsoft como uma taxa para disponibilizar a app ou adicionar na Loja.                                            |
| transacçãoPaymentMethod       | Instrumento de pagamento do cliente utilizado para a transação (como Cartão, Faturação da Transportadora Móvel, PayPal, etc.)                                |
| tpan                           | Indica a rede de anúncios de terceiros                                                                                                     |
| clientePaís                | País cliente                                                                                                                         |
| clienteCity                   | Cidade cliente                                                                                                                            |
| clienteEstado                  | Estado do cliente                                                                                                                           |
| clienteZip                    | Código postal/postal do cliente                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Identificador único para o programa                                                                                                        |
| externalReferenceIdLabel       | Etiqueta de identificador única                                                                                                                  |
| transaçõesCountryCode       | Código do País em que a transação aconteceu                                                                                                                  |
| impostosPaís       | Vendido ao País cliente                                                                                                                  |
| impostosEstado       | Vendido ao Estado cliente                                                                                                                  |
| taxCity       | Vendido ao Cliente Cidade                                                                                                                  |
| taxZipCode       | Vendido ao Cliente Zip                                                                                                                  |
| Nome do Programa de Licenciamento       |                                                                                                                   |
| Código do Programa       | Corda para mapear com o nome do programa                                                                                                                   |
| ganhoAmountInLastPaymentCurrency       | Valor dos ganhos na última moeda de pagamento (campo estará vazio, se não tiverem sido pagos pagamentos prévios)                                                                                                                   |
| últimaMoeda de pagamento       | Última moeda de pagamento (campo estará vazio, se não tiver sido pago nenhum pagamento prévio)                                                                                                                   |
| Assetid       | O identificador único para as encomendas do cliente para o seu serviço de marketplace.  Representa os itens de linha de compra transacionados. Pode haver vários bens.                                                                                                                   |
| OrderId       | refere-se à fatura de um cliente                                                                                                                   |
| LineItemId       | linha individual na fatura de um cliente                                                                                                                   |
| País cliente       | O nome do país fornecido pelo cliente.  Isto pode ser diferente do país na subscrição do Azure de um cliente.                                                                                                                   |
| Endereço de e-mail do cliente       | O endereço de e-mail fornecido pelo cliente final.  Isto poderia ser diferente do endereço de e-mail na Subscrição Azure de um cliente.                                                                                                                   |
| SkuId       | SKU ID conforme definido durante a publicação. Uma oferta pode ter muitos SKUs, mas um SKU só pode ser associado a uma única oferta.                                                                                                                   |

>[!Note]
>Todos os relatórios e insights para a opção de publicação transacte estão disponíveis através da secção Insights do Portal do Parceiro cloud ou da secção Analytics do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio

Para obter ajuda em questões de faturação, contacte o suporte da [editora do mercado comercial.](https://partner.microsoft.com/support/v2/?stage=1)
