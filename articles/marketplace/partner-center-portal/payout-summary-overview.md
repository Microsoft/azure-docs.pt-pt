---
title: Resumo do pagamento - Azure Marketplace
description: O resumo do pagamento mostra-lhe detalhes sobre o dinheiro que ganhou com a sua oferta. Também lhe permite saber quando receberá pagamentos e quanto será pago.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 4ee970f7e4a1228d40a284372300ed9e95c00b7e
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460393"
---
# <a name="payout-summary-overview"></a>Descrição geral do resumo de dividendos

O [resumo do Payout](./payout-summary.md) mostra-lhe detalhes sobre o dinheiro que ganhou com a Microsoft. Também lhe permite saber quando receberá pagamentos e quanto será pago.

Se vender ofertas no Azure Marketplace, também verá informações sobre pagamentos bem-sucedidos no resumo do Payout. Para obter mais informações sobre o pagamento do Azure Marketplace, consulte [as políticas de participação do Azure Marketplace](https://docs.microsoft.com/legal/marketplace/participation-policy) e o Microsoft [Azure Marketplace Publisher Agreement](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Para ser elegível para pagamento, as suas receitas devem atingir o limiar de [pagamento](./payment-thresholds-methods-timeframes.md) de $50. Para obter mais informações sobre o limiar de pagamento, consulte o [Microsoft Azure Marketplace Publisher Agreement](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

Para ver os detalhes do pagamento, inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home) e selecione o ícone de pagamento no canto superior direito do ecrã:

![Ilustra o ícone Payout no canto superior direito do portal Partner Center.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>Funções e permissões

Estas são papéis e permissões para aceder ao relatório de pagamento:

| Relatórios/Páginas | Proprietário de Conta | Gestor | Programador | Contribuinte de negócios | Contribuinte financeiro | Marketer |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de aquisição (incluindo dados quase em tempo real) | Pode ver | Pode ver | Sem acesso | Sem acesso | Pode ver | Sem acesso |
| Relatório de feedback/respostas | Pode ver e enviar feedback | Pode ver e enviar feedback | Pode ver e enviar feedback | Sem acesso | Sem acesso | Pode ver e enviar feedback |
| --- | --- | --- | --- | --- | --- | --- |
| Relatório de saúde (incluindo dados quase em tempo real) | Pode ver | Pode ver | Pode ver | Pode ver | Sem acesso | Sem acesso |
| Relatório de utilização | Pode ver | Pode ver | Pode ver | Pode ver | Sem acesso | Sem acesso |
| Conta de pagamento | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Perfil fiscal | Pode atualizar | Sem acesso | Sem acesso | Sem acesso | Pode atualizar | Sem acesso |
| Resumo dos dividendos | Pode ver | Sem acesso | Sem acesso | Sem acesso | Pode ver | Sem acesso  |
| | | | | | | |

## <a name="payment-schedules"></a>Horários de pagamento

Para uma discussão dos horários de pagamento, incluindo períodos de detenção, visibilidade do parceiro, e quando o cliente utilizar um cartão de crédito ou fatura, consulte a secção de [horários](./payout-policy-details.md#payment-schedules) de pagamento do tópico **de detalhes do Payout.**

## <a name="transaction-history-download-export"></a>Exportação de download de histórico de transações

Esta opção fornece um download de cada item de linha de ganho que vê na página de histórico de Transações. Isto inclui tipo de ganho, data, valor de transação associado, cliente, produto e outros detalhes transacionais relacionados com o programa Incentivos.

| Nome da coluna | Descrição |
| --- | --- |
| earningId | Identificador único para cada ganho |
| participanteId | A identidade primária do parceiro que ganha ao abrigo do programa |
| ParticipanteIdType | ID do programa para programas de incentivo e vendedor se o programa for para programas de Loja e Azure Marketplace |
| nome participante | Nome do parceiro de ganhos |
| partnerCountryCode | Localização/país/região do parceiro de ganhos |
| programaName | Nome do programa de incentivo/loja |
| transactionId | Identificador único para a transação |
| transacçõesAcorrency | Moeda em que ocorreu a transação original do cliente (não é a moeda de localização do parceiro) |
| transacçõesDate | Data da transação. Útil para programas onde muitas transações contribuem para um ganho |
| transacçãoExchangeRate | Taxa de câmbio utilizada para mostrar o valor correspondente da transação USD |
| transacçõesAmonte | Valor da transação na moeda de transação original com base no qual os ganhos são gerados |
| transactionAmountUSD | Valor da transação em dólares americanos (USD) |
| alavanca | Regra de negócio para o ganho |
| ganhoSArtra | Taxa de incentivo aplicada no valor da transação para gerar um ganho |
| quantidade | Quantidade faturada para programas transacionais. Varia com base no programa |
| quantidadeType | Tipo de quantidade, por exemplo: Quantidade faturada, Utilizadores Ativos Mensais (MAU) |
| tipo de ganhos | Indica se é taxa, desconto, galinheiro, venda, e assim por diante |
| earningAmount | Valor de Ganho na moeda de transação original |
| earningAmountUSD | Valor de Ganho em USD |
| ganhandoDate | Data do ganho |
| cálculoDate | Data em que o ganho foi calculado no sistema |
| earningExchangeRate | Taxa de câmbio utilizada para mostrar o montante correspondente de USD |
| exchangeRateDate | Data de câmbio utilizada para calcular O Resultado Amount USD |
| pagamentoAmountWOTax | Valor de ganho (sem imposto) em Pagamento a moeda apenas para &quot; &quot; pagamentos enviados |
| pagamentoSAviência | Pague à moeda escolhida pelo parceiro no perfil de Pagamento. Mostrado apenas para pagamentos enviados |
| pagamentoExchangeRate | Taxa de câmbio utilizada para calcular o pagamentoAmountWOTax em moeda de pagamento utilizando o ExchangeRateDate |
| pagamentoId | Identificador único para o pagamento. Este número é visível no seu extrato bancário |
| estatísticas de pagamentos | Estado dos pagamentos |
| subscrição de pagamentosSdededesimento | Descrição do estado do pagamento |
| customerId | Estará sempre em branco. |
| nome do cliente | Estará sempre em branco. |
| partNumber | Estará sempre em branco. |
| produtoName | Nome do produto ligado à transação |
| productId | Identificador de produto único |
| parentProductId | Identificador de produto único. Se não houver um produto-mãe para a transação, então iD do produto-mãe = ID do produto. |
| nome de produto parental | Nome do produto-mãe. Se não houver um produto-mãe para a transação, então o nome do produto principal = nome do produto. |
| productType | Tipo de produto (como App, Add-on e Game) |
| faturaNumber | Número de fatura (apenas para acordos empresariais) |
| revendedorId | Identificador de revendedor |
| revendedorName | Nome do revendedor |
| tipo de transação | Tipo de transação (como compra, reembolso, reversão e cobrança) |
| LocalProviderSeller | Fornecedor local/vendedor de registos |
| imposto Remetido | Montante de imposto remetido (vendas, uso ou impostos sobre o IVA/GST). |
| taxRemitModel | Parte responsável pela remissão de impostos (taxas de venda, utilização ou IVA/GST). |
| storeFee | O valor retido pela Microsoft como uma taxa para disponibilizar a app ou o addon no mercado comercial. |
| transactionPaymentMethod | Instrumento de pagamento do cliente utilizado para a transação (como Cartão, Faturação de Transportadora Móvel e PayPal) |
| tpan | Rede de anúncios de terceiros |
| clienteCountry | País/região do cliente |
| customerCity | Cidade do cliente |
| estado de clientes | Estado do cliente |
| clienteSAp | Código postal/postal do cliente |
| TenantID | A ID do Inquilino |
| externoReferênciaId | Identificador único para o programa |
| externoReferenceIdLabel | Rótulo de identificador único |
| transacçãoCountryCode | Código país/região em que a transação aconteceu |
| imposto País | País/região do cliente |
| taxState | Estado do cliente |
| taxCity | Cidade do cliente |
| taxZipCode | Código postal/postal do cliente |
| LicenciamentoProgramName | Nome do programa de licenciamento |
| Código do Programa | Cadeia para mapear com o nome do programa |
| earningAmountInLastPaymentCurrency | Valor da obtenção na última moeda de pagamento (o campo estará vazio se não tiverem sido pagos pagamentos prévios) |
| última Procuração | Última moeda de pagamento (o campo estará vazio se não tiver sido pago nenhum pagamento prévio) |
| AssetId | O identificador único para as encomendas de clientes para o seu serviço de marketplace. Representa os itens da linha de compra. Pode haver vários bens. |
| OrderId | Diz respeito à fatura de um cliente |
| LineItemId | Linha individual na fatura de um cliente |
| País/Região do Cliente | O nome país/região fornecido pelo cliente. Isto poderia ser diferente do país/região na Subscrição Azure de um cliente. |
| EmailAddress do cliente | O endereço de e-mail fornecido pelo cliente. Isto poderia ser diferente do endereço de e-mail na Subscrição Azure de um cliente. |
| SkuId | SKU ID como definido durante a publicação. Uma oferta pode ter muitos SKUs, mas um SKU só pode ser associado a uma única oferta. |

> [!NOTE]
> Todos os relatórios e insights para a opção de publicação de transações podem ser encontrados na secção de Analytics do Partner Center.

## <a name="billing-questions-and-support"></a>Perguntas de faturação e apoio
Para suporte à faturação, contacte o suporte de [editores](https://partner.microsoft.com/support/v2/?stage=1)de marketplace comercial.

## <a name="next-step"></a>Próximo passo

- [Resumos de dividendos](./payout-summary.md)
