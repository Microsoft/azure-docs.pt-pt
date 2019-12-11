---
title: Resumo de pagamento do Marketplace comercial | Azure Marketplace
description: O resumo de pagamento mostra detalhes sobre o dinheiro que você ganhou com sua oferta. Ele também permite que você saiba quando receberá pagamentos e quanto será pago.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980273"
---
# <a name="payout-reporting"></a>Relatório de pagamento

O **Resumo do pagamento** mostra detalhes sobre o dinheiro que você ganhou com a Microsoft. Ele também permite que você saiba quando receberá pagamentos e quanto será pago.

Se você vender ofertas no Azure Marketplace, também verá informações sobre pagamentos bem-sucedidos no **Resumo do pagamento**. Para obter mais informações sobre o pagamento do Azure Marketplace, consulte as [políticas de participação Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) e o [contrato de Microsoft Azure Marketplace editor](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Para ser elegível para pagamento, seus prosseguimentos devem alcançar o [limite de pagamento](payment-thresholds-methods-timeframes.md) de $50. Para obter detalhes sobre o limite de pagamento, consulte esta página e examine o [contrato de Microsoft Azure Marketplace editor](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Relatório de pagamento: diferença entre Portal do Cloud Partner e o Partner Center](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Tipos de clientes](#customer-types)
- [Corelação entre pagamento e uso](#corelation-between-payout-and-usage)
- [Download do histórico de transações](#transaction-history-download-export)
- [Perguntas e suporte de cobrança](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Relatório de pagamento: diferença entre Portal do Cloud Partner e o Partner Center

| | Portal de Parceiros da Cloud | Centro de Parceiros |
|---------|---------|---------|
| Ligações | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory e https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Navegação | Relatório de pagamento fornecido no pagamento de informações | Relatório de pagamento fornecido no Partner Center – ícone de pagamento |
| Âmbito | <ul> <li>A transação por item de linha está visível para coleta em andamento, coletada e paga </li> <li>Relatório – mostra todos os itens de linha após a criação da ordem de compra, incluindo a coleta em andamento e a cobrança em andamento, bem como o status da coleta e os itens de linha que ainda não estão qualificados para serem pagos. </li> </ul> | <ul> <li>Mostra os itens de linha depois que eles são considerados ganhos qualificados.</li> <li>Os clientes pagam para a Microsoft primeiro e, em seguida, os ISVs podem ver o relatório de pagamento iniciando.</li> <li>O relatório de pagamento não mostrará a coleção em andamento e a cobrança em andamento.  </li> </ul>  |
| A transação não está pronta para pagamento | Cobrança em andamento | Próximo pagamento estimado: o status do pagamento está no estado não processado.  |
| Status do pagamento |  | Não processados <br> A conquista está qualificada para pagamento. Ele permanece nesse estado por um período de resfriamento, conforme definido no guia do programa do programa de incentivo. <br> <br> Cerimônia <br> Ordem de pagamento-revisões internas pendentes geradas antes de o pagamento ser processado. <br> <br> Enviadas: <br> O pagamento foi enviado ao seu banco. |

## <a name="customer-types"></a>Tipos de clientes 

### <a name="enterprise-agreement"></a>Contrato Enterprise

Os clientes sem um Enterprise Agreement em vigor são cobrados mensalmente por licenças de software do Marketplace. Os clientes com uma Enterprise Agreement são cobrados mensalmente por meio de uma nota fiscal que é apresentada trimestral.

### <a name="credit-cards-and-monthly-invoice"></a>Cartões de crédito e fatura mensal

Os clientes também podem pagar usando um cartão de crédito e uma nota fiscal mensal. Nesse caso, suas taxas de licença de software serão cobradas mensalmente.

### <a name="csp-and-direct-pay-users"></a>CSP e usuários de pagamento direto

Por exemplo, se o cliente comprar usando um cartão de crédito.

## <a name="corelation-between-payout-and-usage"></a>Corelação entre pagamento e uso 

|Descrição    |    Date  | Pedidos/uso  | Dividendos |
|----------|----------|-----------|-------------|
|Período do pedido   | 15 de agosto de 2019-30 de agosto de 2019 | **Pedidos de atributos de correlação** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Utilização** <br> <ul> <li>CustomerId </li> <li>Nome do cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Custo estendido estimado <br> Pagamento estimado (PC) </li> </ul> |  |
|Final do termo (mês)   | 30 de agosto de 2019 | | |
|Data de cobrança | 1 de setembro de 2019 | | |
|Data de pagamento do cliente | 1 de setembro de 2019 | | |
|Período de caução (somente cartões de crédito, 30 dias) | 1 de setembro de 2019-30 de setembro de 2019 | | **Pedidos de atributos de correlação:** <br> <ul><li>AssetId</li> <li>ID de Cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status do pagamento:** Não processados |
|Início do período de coleta | 1 de setembro de 2019 | | |
|Término do período de coleta (máximo, 30 dias) | 30 de setembro de 2019 | | |
|Data de cálculo do pagamento (mensalmente no dia 15) | 1 de outubro de 2019 | | **Atributos de correlação** <br> <ul><li>AssetId</li> <li>ID de Cliente</li> <li>Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status do pagamento:** Cerimônia |
|Data do pagamento | 15 de outubro de 2019 | | **Atributos de correlação** <br> <ul><li>AssetId</li> <li>ID de Cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status do pagamento:** Pagamento enviado |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Contrato Enterprise (clientes trimestrais/mensais)

| Descrição |    Date  | Utilização | Dividendos |
|----------|----------|---------|-----------|
|Período do pedido | 15 de agosto de 2019-30 de agosto de 2019 | **Pedidos de atributos de correlação** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Relatório de uso** <br> <ul> <li>CustomerId </li> <li>Nome do cliente</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> Custo estendido estimado <br> Pagamento estimado (PC) </li> </ul> | |
|Final do termo (trimestre) | 30 de setembro de 2019 | | |
|Data de cobrança | 15 de outubro de 2019 | | |
|Período de caução (somente cartões de crédito, 30 dias) | n/d | | |
|Início do período de coleta | 15 de outubro de 2019 | | |
|Somente cartões de crédito, 30 dias | 1º de novembro de 2019 – 30 de novembro de 2019 | | |
|Término do período de coleta (máximo, 90 dias) | 15 de janeiro de 2020 | | |
|Data de pagamento do cliente | 30 de dezembro de 2019 | | |
|Cálculo de pagamento | 15 de janeiro de 2020 | | |
|Data do pagamento | 15 de fevereiro de 2020 | | **Para clientes baseados em trimestre** <br> <br> **Relatório de pedidos** <br> <ul><li>AssetId</li> <li>ID de Cliente</li> <li> Nome do cliente</li> </ul> <br> **Utilização** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Nome do cliente</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **Status do pagamento:** enviado |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>Exportação de download de histórico de transações

Essa opção fornece um download de cada item de linha de produção que você vê na página Histórico de transações, tipo de conquista, data, valor da transação associada, cliente, produto e outros detalhes transacionais aplicáveis ao programa incentivos. 

| Nome da coluna     | Descrição    | 
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
| paymentStatus            | Status do pagamento                                            |
| paymentStatusDescription            | Descrição amigável do status de pagamento                                            |
| IDCliente                     | Estará sempre em branco                                                                                                                     |
| Customer                   | Estará sempre em branco                                                                                                                     |
| partNumber                     | Estará sempre em branco                                                                                                                     |
| NomeDoProduto                    | Nome do produto vinculado à transação                                                                                                       |
| productId                      | Identificador de produto exclusivo                                                                                                                |
| parentProductId                | Identificador de produto pai exclusivo. Observação: se não houver um produto pai para a transação, a ID do produto pai = ID do produto. |
| parentProductName              | Nome do produto pai. Observação: se não houver um produto pai para a transação, em seguida, nome do produto pai = nome do produto.   |
| productType                    | Tipo de produto (como aplicativo, complemento, jogo, etc.)                                                                                        |
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
| OrderId       | relacionado à fatura de um cliente                                                                                                                   |
| LineItemId       | linha individual na fatura de um cliente                                                                                                                   |
| País do cliente       | O nome do país fornecido pelo cliente.  Isso pode ser diferente do país na assinatura do Azure de um cliente.                                                                                                                   |
| EmailAddress do cliente       | O endereço de email fornecido pelo cliente final.  Isso pode ser diferente do endereço de email na assinatura do Azure de um cliente.                                                                                                                   |
| SkuId       | ID do SKU conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode ser associada a uma única oferta.                                                                                                                   |

>[!Note]
>Todos os relatórios e informações para a opção de publicação de transação estão disponíveis por meio da seção insights da seção Portal do Cloud Partner ou análise do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas e suporte de cobrança

Para obter ajuda sobre perguntas de cobrança, entre em contato com o [suporte do editor do Marketplace comercial](https://aka.ms/marketplacepublishersupport).
