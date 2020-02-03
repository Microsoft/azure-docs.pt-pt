---
title: Resumo de pagamento do Marketplace comercial | Azure Marketplace
description: O resumo de pagamento mostra detalhes sobre o dinheiro que você ganhou com sua oferta. Ele também permite que você saiba quando receberá pagamentos e quanto será pago.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 6ee6b6f325ba58ecaa3c3acb5d5ded173262bafb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715442"
---
# <a name="payout-reporting"></a>Relatório de pagamento

O resumo do [**Pagamento**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) mostra-lhe detalhes sobre o dinheiro que ganhou com a Microsoft. Ele também permite que você saiba quando receberá pagamentos e quanto será pago.

Se vender ofertas no Azure Marketplace, também verá informações sobre pagamentos bem sucedidos no resumo do **Pagamento.** Para obter mais informações sobre o pagamento do Azure Marketplace, consulte as Políticas de [Participação no Mercado do Microsoft Azure](https://go.microsoft.com/fwlink/p/?LinkId=722436) e o Acordo de [Editor estonteante Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Para ser elegível para pagamento, as suas receitas devem atingir o limiar de [pagamento](payment-thresholds-methods-timeframes.md) de $50. Para mais detalhes sobre o limiar de pagamento consulte esta página e reveja o Acordo de Editor estoiro do [Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Funções e permissão para aceder ao relatório de pagamento](#roles-and-permission-to-access-the-payout-report)
- [Relatório de pagamento: diferença entre Cloud Partner Portal e Partner Center](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Tipos de clientes](#customer-types)
- [Corelação entre pagamento e utilização](#corelation-between-payout-and-usage)
- [Download de histórico de transações](#transaction-history-download-export)
- [Perguntas de faturação e apoio](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>Funções e permissão para acessar o relatório de pagamento

| Relatórios/páginas    | Proprietário da conta    | Gestor  | Programador | Colaborador de negócios |  Colaborador financeiro | Comerciante |
|------------------|------------------|----------|-----------|----|----|-----|
| Relatório de aquisição (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Sem acesso | Sem acesso | Pode exibir | Sem acesso |
| Relatório de comentários/respostas | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Pode exibir e enviar comentários | Sem acesso | Sem acesso | Pode exibir e enviar comentários |
| Relatório de integridade (incluindo dados quase em tempo real) | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Relatório de utilização | Pode exibir | Pode exibir | Pode exibir | Pode exibir | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil de imposto | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo dos dividendos | Pode exibir | Sem acesso | Sem acesso | Sem acesso | Pode exibir | Sem acesso |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Relatório de pagamento: diferença entre Portal do Cloud Partner e o Partner Center

| | Portal de Parceiros da Cloud | Centro de Parceiros |
|---------|---------|---------|
| Hiperligações | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory) e [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| Navegação | Relatório de pagamento fornecido no pagamento de informações | Relatório de pagamento fornecido no Partner Center – ícone de pagamento |
| Âmbito | <ul> <li>A transação por item de linha está visível para coleta em andamento, coletada e paga </li> <li>Relatório – mostra todos os itens de linha após a criação da ordem de compra, incluindo a coleta em andamento e a cobrança em andamento, bem como o status da coleta e os itens de linha que ainda não estão qualificados para serem pagos. </li> </ul> | <ul> <li>Mostra os itens de linha depois que eles são considerados ganhos qualificados.</li> <li>Os clientes pagam para a Microsoft primeiro e, em seguida, os ISVs podem ver o relatório de pagamento iniciando.</li> <li>O relatório de pagamento não mostrará a coleção em andamento e a cobrança em andamento.  </li> </ul>  |
| A transação não está pronta para pagamento | Cobrança em andamento | Próximo pagamento estimado: o status do pagamento está no estado não processado.  |
| Status do pagamento |  | Não processados <br> A conquista está qualificada para pagamento. Ele permanece nesse estado por um período de resfriamento, conforme definido no guia do programa do programa de incentivo. <br> <br> Cerimônia <br> Ordem de pagamento-revisões internas pendentes geradas antes de o pagamento ser processado. <br> <br> Enviado: <br> O pagamento foi enviado ao seu banco. |

## <a name="customer-types"></a>Tipos de clientes

### <a name="enterprise-agreement"></a>Contrato Enterprise

Os clientes sem um Enterprise Agreement em vigor são cobrados mensalmente por licenças de software do Marketplace. Os clientes com uma Enterprise Agreement são cobrados mensalmente por meio de uma nota fiscal que é apresentada trimestral.

### <a name="credit-cards-and-monthly-invoice"></a>Cartões de crédito e fatura mensal

Os clientes também podem pagar usando um cartão de crédito e uma nota fiscal mensal. Nesse caso, suas taxas de licença de software serão cobradas mensalmente.

### <a name="csp-and-direct-pay-users"></a>CSP e usuários de pagamento direto

Por exemplo, se o cliente comprar usando um cartão de crédito.

## <a name="corelation-between-payout-and-usage"></a>Corelação entre pagamento e uso

|Descrição    |    Date  | Pedidos/uso  | Pagamento |
|----------|----------|-----------|-------------|
|Período do pedido   | 15 de agosto de 2019-30 de agosto de 2019 | **Ordens de Atributos correlaçãois** <br> <ul> <li>Rdenouno</li> <li>clienteId</li> </ul> <br> **Utilização** <br> <ul> <li>clienteId </li> <li>Nome do cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Custo estendido estimado <br> Pagamento estimado (PC) </li> </ul> |  |
|Final do termo (mês)   | 30 de agosto de 2019 | | |
|Data de cobrança | 1 de setembro de 2019 | | |
|Data de pagamento do cliente | 1 de setembro de 2019 | | |
|Período de caução (somente cartões de crédito, 30 dias) | 1 de setembro de 2019-30 de setembro de 2019 | | **Correlação atribui ordens:** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>clienteId</li> <li>Nome do cliente</li> <li>Rdenouno</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** Não processado |
|Início do período de coleta | 1 de setembro de 2019 | | |
|Término do período de coleta (máximo, 30 dias) | 30 de setembro de 2019 | | |
|Data de cálculo do pagamento (mensalmente no dia 15) | 1 de outubro de 2019 | | **Atributos de Correlação** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li>Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>clienteId</li> <li>Nome do cliente</li> <li>Rdenouno</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** A seguir |
|Data do pagamento | 15 de outubro de 2019 | | **Atributos de Correlação** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>clienteId</li> <li>Nome do cliente</li> <li>Rdenouno</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** Pagamento enviado |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contrato Enterprise (clientes trimestrais/mensais)

| Descrição |    Date  | Utilização | Pagamento |
|----------|----------|---------|-----------|
|Período do pedido | 15 de agosto de 2019-30 de agosto de 2019 | **Correlação atribui ordens** <br> <ul> <li>Rdenouno</li> <li>clienteId</li> </ul> <br> **Relatório de utilização** <br> <ul> <li>clienteId </li> <li>Nome do cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Custo estendido estimado <br> Pagamento estimado (PC) </li> </ul> | |
|Final do termo (trimestre) | 30 de setembro de 2019 | | |
|Data de cobrança | 15 de outubro de 2019 | | |
|Período de caução (somente cartões de crédito, 30 dias) | n/d | | |
|Início do período de coleta | 15 de outubro de 2019 | | |
|Somente cartões de crédito, 30 dias | 1º de novembro de 2019 – 30 de novembro de 2019 | | |
|Término do período de coleta (máximo, 90 dias) | 15 de janeiro de 2020 | | |
|Data de pagamento do cliente | 30 de dezembro de 2019 | | |
|Cálculo de pagamento | 15 de janeiro de 2020 | | |
|Data do pagamento | 15 de fevereiro de 2020 | | **Para clientes trimestrais** <br> <br> **Relatório de encomendas** <br> <ul><li>AssetId</li> <li>ID do cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>clienteId</li> <li>Nome do cliente</li> <li>Rdenouno</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **Estado do pagamento:** enviado |

## <a name="transaction-history-download-export"></a>Exportação de download de histórico de transações

Essa opção fornece um download de cada item de linha de produção que você vê na página Histórico de transações, tipo de conquista, data, valor da transação associada, cliente, produto e outros detalhes transacionais aplicáveis ao programa incentivos.

| nome da coluna     | Descrição    |
|-------------|-------------------------------|
| ganhandoid                      | Identificador exclusivo para cada conquista                                                                                                       |
| participante da                  | A principal identidade do parceiro ganhando sob o programa                                                                            |
| participantIdType              | Principalmente a ID do programa para programas de incentivo e vendedor se for para os programas da loja e o Azure Marketplace                                          |
| participantename                | Nome do parceiro de conquista                                                                                                              |
| partnerCountryCode             | Localização/país do parceiro de conquista                                                                                                  |
| programName                    | Nome do programa de incentivo/loja                                                                                                             |
| transactionId                  | Identificador exclusivo para a transação                                                                                                    |
| transactionCurrency            | Moeda na qual a transação original do cliente ocorreu (não é a moeda do local do parceiro)                                     |
| transactionDate                | Data da transação. Útil para programas em que muitas transações contribuem para uma conquista                                           |
| transactionExchangeRate        | Taxa de câmbio usada para mostrar a transação correspondente valor USD                                                                 |
| transactionAmount              | Valor da transação na moeda da transação original com base na qual a conquista é gerada                                              |
| transactionAmountUSD           | Valor da transação em USD                                                                                                                |
| alavanca                          | Indica a regra de negócio para a conquista                                                                                                  |
| earningRate                    | Taxa de incentivo aplicada ao valor da transação para gerar uma conquista                                                                      |
| quantidade                       | Varia de acordo com o programa. Indica a quantidade cobrada para programas transacionais                                                            |
| quanttype                   | Indica o tipo de quantidade, por exemplo: quantidade cobrada, MAU                                                                                     |
| ganhandotype                    | Indica se é Tarifa, desconto, Coop, venda etc.                                                                                          |
| earningAmount                  | Conquistando valor na moeda da transação original                                                                                      |
| earningAmountUSD               | Conquistando valor em USD                                                                                                                    |
| earningDate                    | Data da conquista                                                                                                                      |
| calculationDate                | Data em que a conquista foi calculada no sistema                                                                                            |
| earningExchangeRate            | Taxa de câmbio usada para mostrar o valor USD correspondente                                                                                  |
| exchangeRateDate               | Data da taxa de câmbio usada para calcular EarningAmount USD                                                                                   |
| paymentAmountWOTax             | Valor de conquista (sem imposto) em pagar para moeda somente para pagamentos "enviados"                                                                 |
| paymentCurrency                | Pague para moeda escolhida pelo parceiro no perfil de pagamento. Mostrado somente para pagamentos enviados                                                   |
| paymentExchangeRate            | Taxa de câmbio usada para calcular paymentAmountWOTax na moeda de pagamento usando ExchangeRateDate                                            |
| pagamento de            | Identificador exclusivo para o pagamento. Esse número é visível em seu extrato bancário                                            |
| paymentStatus            | Estado de pagamento                                            |
| paymentStatusDescription            | Descrição amigável do status de pagamento                                            |
| clienteId                     | Estará sempre em branco                                                                                                                     |
| Customer                   | Estará sempre em branco                                                                                                                     |
| partNumber                     | Estará sempre em branco                                                                                                                     |
| NomeDoProduto                    | Nome do produto vinculado à transação                                                                                                       |
| productId                      | Identificador de produto exclusivo                                                                                                                |
| parentProductId                | Identificador de produto pai exclusivo. Observação: se não houver um produto pai para a transação, a ID do produto pai = ID do produto. |
| parentProductName              | Nome do produto pai. Observação: se não houver um produto pai para a transação, em seguida, nome do produto pai = nome do produto.   |
| produtoType                    | Tipo de produto (como aplicativo, complemento, jogo, etc.)                                                                                        |
| invoiceNumber                  | Número da nota fiscal (aplicável apenas ao EA)                                                                                                  |
| revendedorid                     | Identificador do revendedor                                                                                                                      |
| revendedorname                   | Nome do revendedor                                                                                                                            |
| transactionType                | Tipo de transação (como compra, reembolso, estorno, estorno, etc.)                                                               |
| localProviderSeller            | Provedor local/vendedor de registro                                                                                                          |
| taxRemitted                    | Quantidade de impostos remetidos (vendas, uso ou impostos sobre IVA/GST).                                                                                   |
| taxRemitModel                  | Parte responsável por remeter impostos (vendas, uso ou impostos sobre IVA/GST).                                                                    |
| storeFee                       | O valor retido pela Microsoft como uma taxa para tornar o aplicativo ou complemento disponível na loja.                                            |
| transactionPaymentMethod       | Instrumento de pagamento de cliente usado para a transação (como cartão, cobrança de operadora móvel, PayPal, etc.)                                |
| tpan                           | Indica a rede de terceiros do AD                                                                                                     |
| CustomerCountryComparer                | País do cliente                                                                                                                         |
| customerCity                   | Cidade do cliente                                                                                                                            |
| customerstate                  | Estado do cliente                                                                                                                           |
| customerZip                    | CEP do cliente/CEP                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Identificador exclusivo do programa                                                                                                        |
| externalReferenceIdLabel       | Rótulo de identificador exclusivo                                                                                                                  |
| transactionCountryCode       | Código do país no qual a transação ocorreu                                                                                                                  |
| taxCountry       | Vendido para o país do cliente                                                                                                                  |
| taxState       | Vendido para o estado do cliente                                                                                                                  |
| taxCity       | Vendido à cidade do cliente                                                                                                                  |
| taxZipCode       | Vendido para o zip do cliente                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Código do programa       | Cadeia de caracteres para mapear com o nome do programa                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Conquistando valor na última moeda de pagamento (o campo estará vazio, se nenhum pagamento anterior tiver sido pago)                                                                                                                   |
| lastPaymentCurrency       | Última moeda de pagamento (o campo estará vazio, se nenhum pagamento anterior tiver sido pago)                                                                                                                   |
| AssetId       | O identificador exclusivo para os pedidos do cliente para o serviço do Marketplace.  Ele representa os itens de linha de compra transacionados. Pode haver vários ativos.                                                                                                                   |
| Rdenouno       | relacionado à fatura de um cliente                                                                                                                   |
| LineItemId       | linha individual na fatura de um cliente                                                                                                                   |
| País do cliente       | O nome do país fornecido pelo cliente.  Isso pode ser diferente do país na assinatura do Azure de um cliente.                                                                                                                   |
| EmailAddress do cliente       | O endereço de email fornecido pelo cliente final.  Isso pode ser diferente do endereço de email na assinatura do Azure de um cliente.                                                                                                                   |
| SkuId       | ID do SKU conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode ser associada a uma única oferta.                                                                                                                   |

>[!Note]
>Todos os relatórios e informações para a opção de publicação de transação estão disponíveis por meio da seção insights da seção Portal do Cloud Partner ou análise do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas e suporte de cobrança

Para obter ajuda em questões de faturação, contacte o suporte da [editora do mercado comercial.](https://aka.ms/marketplacepublishersupport)
