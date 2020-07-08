---
title: Relatórios de pagamento - Mercado comercial da Microsoft
description: Os relatórios do Payout mostram-lhe detalhes sobre o dinheiro que ganhou com a sua oferta, incluindo o valor do pagamento e quando será pago.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6639026d071b496027996036a81f6bc66e0185e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83744817"
---
# <a name="payout-summaries"></a>Resumos de dividendos

O resumo do Payout mostra-lhe detalhes sobre o dinheiro que ganhou com a Microsoft. Também lhe permite saber quando receberá pagamentos e quanto será pago.

Se vender produtos no Azure Marketplace, também verá informações sobre pagamentos bem-sucedidos no resumo do Payout. Para mais detalhes, consulte [as políticas de participação do Microsoft Azure Marketplace e](https://docs.microsoft.com/legal/marketplace/participation-policy) o Microsoft [Azure Marketplace Publisher Agreement](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Para ser elegível para pagamento, as suas receitas devem atingir o limiar de [pagamento](payment-thresholds-methods-timeframes.md) de $50. Para mais detalhes sobre o limiar de pagamento consulte esta página e reveja o acordo de desenvolvedor de aplicações.

> [!NOTE]
> Se procura apoio em relação a pagamentos, incluindo configurar contas de pagamento, pagamentos em falta, colocar pagamentos em espera, ou qualquer outra coisa, apoio de contato [aqui.](https://developer.microsoft.com/windows/support)

## <a name="access-the-payout-summary-pages"></a>Aceda às páginas de resumo do pagamento

Para abrir uma das páginas de resumo do pagamento:

1. Selecione o ícone Payout no canto superior direito.
2. Selecione o histórico de transações, pagamentos ou dados de exportação.

## <a name="transaction-history-page"></a>Página de histórico de transações

Esta página exibe todos os seus ganhos individuais, incluindo a data, o tipo e o ganho para cada um. Pode selecionar um período de tempo para visualizar, e também pode filtrar por ID de Inscrição, Programa, ID de Pagamento, Tipo de Ganho, Alavanca e Estado. Os dados estão disponíveis para o exercício em curso (1 de julho a 30 de junho) e para os dois exercícios anteriores.

Para ver mais detalhes sobre um ganho, selecione a seta para baixo no lado direito da página. Isto mostrará a alavanca, o valor das receitas e o produto. Se algum destes dados não estiver disponível, mas necessitar de acesso aos mesmos, contacte [o suporte](https://developer.microsoft.com/windows/support). Se o ganho for o resultado de um ajustamento e não de uma transação, os campos de produto não serão apresentados.

Para exportar qualquer um dos dados de transação nesta página, utilize a página **de dados de Exportação.**

## <a name="payments-page"></a>Página de pagamentos

Os totais desta página representam todos os programas em que participa. Pode filtrar por ID do Participante, Programa, ID de pagamento e tipo de Ganho. Os montantes são dados em dólares americanos. O valor pago também é apresentado em pagamento à moeda.

| Área                   | Descrição                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| Total pago este ano   | O total combinado pago a você este ano, em dólares americanos, para todos os seus programas       |
| Próximo pagamento estimado | O único próximo pagamento que vem até si (mesmo que haja outros em breve), em dólares americanos |
| Último pagamento           | O valor (em dólares americanos), nome do programa e programa do seu pagamento mais recente           |
| Pagamentos por fonte     | Montante dos pagamentos (em dólares americanos) representados pelo programa nos últimos 12 meses           |
| Pagamentos               | Selecione **Pago** ou Pendente e, **em** seguida, ordenar como desejado. Para mais detalhes sobre um pagamento específico, **selecione Ver**. Para fazer o download de uma cópia da declaração de remessa de pagamento, selecione **Baixar**. Como os dados do histórico de transações podem demorar até 24 horas a aparecer, pode não ver os ganhos associados imediatamente. |
|||

Para exportar qualquer um dos dados desta página, selecione **Export** e siga as instruções na página de dados de Exportação.

## <a name="transaction-history-page"></a>Página de histórico de transações

Esta página exibe todos os seus ganhos individuais, incluindo a data, o tipo e o ganho para cada um. Pode selecionar um período de tempo para visualizar e pode filtrar por ID de Inscrição, Programa, ID de Pagamento, Tipo de Ganho, Alavanca e Estado. Os dados estão disponíveis para o exercício em curso (1 de julho a 30 de junho) e para os dois exercícios anteriores.

Para ver mais detalhes sobre um ganho, selecione a seta para baixo no lado direito da página. Isto mostrará a alavanca, o valor das receitas e o produto. Se algum destes dados não estiver disponível, mas necessitar de acesso aos mesmos, [contacte o suporte](https://developer.microsoft.com/windows/support)]. Se o ganho for o resultado de um ajustamento e não de uma transação, os campos de produto não serão apresentados.

Para exportar qualquer um dos dados de transação nesta página, selecione **Export** e siga as instruções na página de dados de Exportação. Os ficheiros exportados da página De Histórico de Transações mostram dados em moeda de transação, ganhos tanto em moeda de transação como em dólares dos EUA, e o valor pago em pagamento à moeda.

## <a name="payment-status"></a>Estado dos pagamentos

| Estado de ganho           | Razão                                                                                                                                      | A ação do parceiro é necessária?                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Não processado              | O ganho é elegível para pagamento. Permanece neste estado por um período de arrefecimento, conforme definido no guia do programa para o programa Incentivo. | Não                                                         |
| A seguir                 | Ordem de pagamento gerada até revisões internas pendentes antes do pagamento ser processado                                                               | Não                                                         |
| Fatura fiscal pendente      | A sua fatura fiscal é incompleta ou inválida                                                                                                  | Precisa atualizar a sua fatura fiscal antes de poder ser paga |
| Rejeitado durante a revisão   | O pagamento foi rejeitado durante a revisão                                                                                                     | Contacte [o suporte da Microsoft](https://developer.microsoft.com/windows/support) para obter mais detalhes                      |
| Falhou                   | O pagamento falhou devido a um erro no sistema da Microsoft                                                                                         | Contacte [o suporte da Microsoft](https://developer.microsoft.com/windows/support) para obter mais detalhes                      |
| Em curso              | O pagamento está em curso.                                                                                                                 | Não                                                         |
| Pagamento incorreto        | A recuperação do pagamento está em andamento                                                                                                       | Não                                                         |
| Enviados                     | O pagamento foi enviado para o seu banco.                                                                                                     | Não                                                         |
| Reprocessamento             | O pagamento encontrou um erro no sistema da Microsoft e está a ser reprocessado                                                                  | Não                                                         |
| Invertido                 | O pagamento foi invertido pelo seu banco e será enviado novamente no próximo ciclo de pagamentos                                                     | Não                                                         |
| Fatura fiscal rejeitada     | A sua fatura fiscal foi rejeitada durante a revisão. Todos os pagamentos pendentes estarão suspensos até que a revisão da fatura fiscal esteja completa.                 | Contacte [o suporte da Microsoft](https://developer.microsoft.com/windows/support) para obter mais detalhes                      |
| Fatura fiscal em análise | A sua fatura fiscal está a ser revista. O seu pagamento será liberado assim que a fatura do imposto tiver sido aprovada.                                   | Não                                                         |
| Rejeitado                 | O pagamento foi rejeitado pelo seu banco.                                                                                                      | Contacte o seu banco para obter mais detalhes.                             |
|||

## <a name="export-data-page"></a>Página de dados de exportação

Siga estas instruções para exportar dados.

A página de dados de exportação não se atualiza por si só. Poderá ser necessário atualizar a página manualmente para ver os dados mais recentes.

O seu filtro pode resultar num erro **de não dados disponíveis.** Isto provavelmente significa que deixou o período de tempo padrão selecionado em três meses e, em seguida, selecionou um ID de pagamento de um ganho que está fora desse período. Expanda o seu período de tempo e tente novamente.

## <a name="payments"></a>Pagamentos

![Pagamentos de exportação](./media/pc-export-payments.png)

Esta opção fornece um download dos pagamentos que recebeu no seu banco para um determinado programa, o imposto associado e o valor agregado de ganhos. Este relatório é usado para muitos programas do Partner Center, por isso algumas colunas podem ser inaplicáveis ao seu relatório. As colunas estão marcadas abaixo.

| Nome da coluna              | Descrição                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| id participante            | A identidade primária do parceiro que ganha ao abrigo do programa                                                                             |
| IDType participante        | Normalmente programa ID para programas de incentivo e ID do vendedor para programas de loja                                                                |
| nome participante          | Nome do parceiro de ganhos                                                                                                               |
| programaName              | Nome do programa de incentivo/loja                                                                                                              |
| merecido                   | Valor auferido na moeda Pay To para esse programa/participanteID                                                                       |
| earnedusd                | Valor ganho para o ID do programa/participante, em USD                                                                                      |
| retidoTax              | Montante do imposto retido na moeda Pay To para o programa/participanteID                                                               |
| salesTax                 | Montante total do imposto sobre as vendas na moeda Pay To para o programa/participanteID (aplicável apenas para programas de incentivo)                   |
| serviceFeeTax            | Montante total do serviçoFeeTax em Moeda Paga para o programa/participanteID (aplicável apenas para programas de loja e apenas Azure Marketplace) |
| total de pagamento             | Pagamento total em moeda local, excluindo a retenção na fonte e incluindo o imposto sobre as vendas (se aplicável) para o programa/participanteID   |
| currencyCode             | Pagar para código de moeda                                                                                                                      |
| pagamentoMethod            | O método usado para pagar ao parceiro, por exemplo, transferência bancária eletrónica, nota de crédito                                                     |
| paymentID                | Identificador único para o pagamento. Este número é geralmente visível no seu extrato bancário (aplicável apenas para pagamentos SAP).              |
| estatísticas de pagamentos            | Estado dos pagamentos                                                                                                                            |
| subscrição de pagamentosSdededesimento | Descrição amigável do estado do pagamento                                                                                                    |
| pagamentoDate              | O pagamento da data foi enviado da Microsoft                                                                                                      |
|||

## <a name="transaction-history"></a>Histórico de transações

![História da transação de exportação](./media/pc-export-transaction.png)

Esta opção fornece um download de cada item de linha de ganho que vê na página de histórico de Transação, tipo de ganho, data, valor de transação associado, cliente, produto e outros detalhes transacionais aplicáveis aos seus programas.

| Nome da coluna                    | Descrição                                                                                                                              | Aplicabilidade para Incentivos/Loja/Mercado Azure           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | Identificador único para cada ganho                                                                                                       | Todos                                                            |
| participanteId                  | A identidade primária do parceiro que ganha ao abrigo do programa                                                                            | Todos                                                            |
| ParticipanteIdType              | Principalmente iD de programa para programas de incentivo e vendedor IF para programas de loja e Azure Marketplace                                          | Todos                                                            |
| nome participante                | Nome do parceiro de ganhos                                                                                                              | Todos                                                            |
| partnerCountryCode             | Localização/país/região do parceiro de ganhos                                                                                                  | Todos                                                            |
| programaName                    | Nome do programa de incentivo/loja                                                                                                             | Todos                                                            |
| transactionId                  | Identificador único para a transação                                                                                                    | Todos                                                            |
| transacçõesAcorrency            | Moeda em que ocorreu a transação original do cliente (esta não é moeda de localização de parceiros)                                     | Todos                                                            |
| transacçõesDate                | Data da transação. Útil para programas onde muitas transações contribuem para um ganho                                           | Todos                                                            |
| transacçãoExchangeRate        | Data de câmbio utilizada para mostrar o valor correspondente da transação USD                                                                 | Todos                                                            |
| transacçõesAmonte              | Valor da transação na moeda de transação original com base no qual os ganhos são gerados                                              | Todos                                                            |
| transactionAmountUSD           | Valor da transação em USD                                                                                                                | Todos                                                            |
| alavanca                          | Indica regra de negócio para o ganho                                                                                                  | Todos                                                            |
| ganhoSArtra                    | Taxa de incentivo aplicada no valor da transação para gerar um ganho                                                                      | Todos                                                            |
| quantidade                       | Varia com base no programa. Indica quantidade faturada para programas transacionais                                                            | Todos                                                            |
| quantidadeType                   | Indica tipo de quantidade, por exemplo, quantidade faturada, MAU                                                                             | Todos                                                            |
| tipo de ganhos                    | Indica se é taxa, desconto, cooperativa, venda, e assim por diante                                                                                          | Todos                                                            |
| earningAmount                  | Valor de Ganho na moeda de transação original                                                                                      | Todos                                                            |
| earningAmountUSD               | Valor de Ganho em USD                                                                                                                    | Todos                                                            |
| ganhandoDate                    | Data do ganho                                                                                                                      | Todos                                                            |
| cálculoDate                | Data em que o ganho foi calculado no sistema                                                                                            | Todos                                                            |
| earningExchangeRate            | Taxa de câmbio utilizada para mostrar o montante correspondente de USD                                                                                  | Todos                                                            |
| exchangeRateDate               | Data de câmbio utilizada para calcular O Resultado Amount USD                                                                                   | Todos                                                            |
| pagamentoAmountWOTax             | Valor de ganho (sem imposto) em Pagamento a moeda para pagamentos "Enviados" apenas                                                                 | Todos                                                            |
| pagamentoSAviência                | Pague à moeda escolhida pelo parceiro no perfil de Pagamento. Mostrado apenas para pagamentos enviados                                                   | Todos                                                            |
| pagamentoExchangeRate            | Taxa de câmbio utilizada para calcular o pagamentoAmountWOTax em moeda de pagamento utilizando o ExchangeRateDate                                            | Todos                                                            |
| claimId                        | Identificador único para reclamação                                                                                                              | Incentivos - apenas alguns programas                                |
| planId                         | Identificador único para plano                                                                                                               | Incentivos - apenas alguns programas                                |
| pagamentoId                      | Identificador único para o pagamento. Este número é geralmente visível no seu extrato bancário                                                 | Pagamentos SAP apenas                                              |
| estatísticas de pagamentos                  | Estado dos pagamentos                                                                                                                           | Todos                                                            |
| subscrição de pagamentosSdededesimento       | Descrição amigável do estado do pagamento                                                                                                   | Todos                                                            |
| customerId                     | Estará sempre em branco.                                                                                                                     | Apenas programas de incentivo (exceção: OEM) e Azure Marketplace |
| nome do cliente                   | Estará sempre em branco.                                                                                                                     | Apenas programas de incentivo (exceção: OEM) e Azure Marketplace |
| partNumber                     | Estará sempre em branco.                                                                                                                     | Alguns programas de incentivo e loja e Azure Marketplace        |
| produtoName                    | Nome do produto ligado à transação                                                                                                       | Todos                                                            |
| productId                      | Identificador de produto único                                                                                                                | Loja e Mercado Azure                                    |
| parentProductId                | Identificador de produto único. Se não houver um produto-mãe para a transação, então iD do produto-mãe = ID do produto. | Loja e Mercado Azure                                    |
| nome de produto parental              | Nome do produto-mãe. Se não houver um produto-mãe para a transação, então o nome do produto principal = nome do produto.   | Loja e Mercado Azure                                    |
| productType                    | Tipo de produto, como App, Add-on ou Game                                                                                        | Loja e Mercado Azure                                    |
| faturaNumber                  | Número de fatura (aplicável apenas para EA)                                                                                                  | Incentivo e Azure Marketplace - apenas alguns programas           |
| subscriptionId                 | Identificador de assinatura associado ao cliente                                                                                         | Incentivo - apenas alguns programas                                 |
| subscriçãoStartDate          | Data de início da subscrição                                                                                                                  | Incentivo - apenas alguns programas                                 |
| subscriçãoEndDate            | Data de fim da assinatura                                                                                                                    | Incentivo - apenas alguns programas                                 |
| revendedorId                     | Identificador de revendedor                                                                                                                      | Incentivo - apenas alguns programas                                 |
| revendedorName                   | Nome do revendedor                                                                                                                            | Incentivo - apenas alguns programas                                 |
| distribuidorId                  | Identificador de distribuidor                                                                                                                   | Incentivo - apenas alguns programas                                 |
| nome distribuidorna                | Nome do distribuidor                                                                                                                         | Incentivo - apenas alguns programas                                 |
| agreementNumber                | Número do contrato                                                                                                                         | Incentivo - apenas alguns programas                                 |
| agreementStartDate             | Data de início do acordo                                                                                                                     | Incentivo - apenas alguns programas                                 |
| agreementEndDate               | Data limite do acordo                                                                                                                       | Incentivo - apenas alguns programas                                 |
| workload                       | Carga de trabalho                                                                                                                                 | Incentivo - apenas alguns programas                                 |
| tipo de transação                | Tipo de transação, tais como compra, reembolso, reversão ou cobrança                                                               | Loja e Mercado Azure                                    |
| LocalProviderSeller            | Fornecedor local/vendedor de registos                                                                                                          | Armazenar apenas                                                     |
| imposto Remetido                    | Montante dos impostos remetidos (taxas de venda, utilização ou IVA/GST)                                                                                   | Loja e Mercado Azure                                    |
| taxRemitModel                  | Parte responsável pela remessa de impostos (taxas de venda, utilização ou IVA/GST)                                                                    | Armazenar apenas                                                     |
| storeFee                       | O valor retido pela Microsoft como uma taxa para disponibilizar a app ou o addon na Loja                                           | Armazenar apenas                                                     |
| transactionPaymentMethod       | Instrumento de pagamento do cliente utilizado para a transação, como Cartão, Faturação de Transportadora Móvel ou PayPal                                | Loja e Mercado Azure                                    |
| tpan                           | Indica a rede de anúncios de terceiros                                                                                                     | Loja - Apenas anúncios                                               |
| clienteCountry                | País/região do cliente                                                                                                                         | Loja e Mercado Azure                                    |
| customerCity                   | Cidade do cliente                                                                                                                            | Loja e Mercado Azure                                    |
| estado de clientes                  | Estado do cliente                                                                                                                           | Loja e Mercado Azure                                    |
| clienteSAp                    | Código postal/postal do cliente                                                                                                                 | Loja e Mercado Azure                                    |
| comprarTypeCode               | Estará sempre em branco.                                                                                                                     | Programa de incentivos - CRI                                        |
| purchaseOrderType              | Estará sempre em branco.                                                                                                                     | Programa de incentivos - CRI                                        |
| comprarOrderCoverageStartDate | Estará sempre em branco.                                                                                                                     | Programa de incentivos - CRI                                        |
| comprarOrderCoverageEndDate   | Estará sempre em branco.                                                                                                                     | Programa de incentivos - CRI                                        |
| programaOfferingLevel           |                                                                                                                                          | Programa de incentivos - CRI                                        |
| TenantID                       |                                                                                                                                          | Programas de incentivo                                             |
| externoReferênciaId            | Identificador único para o programa                                                                                                        | Programas de Pagamento Direto (Incentivo e Loja)                      |
| externoReferenceIdLabel       | Rótulo de identificador único                                                                                                                  | Programas de Pagamento Direto (Incentivo e Loja)                      |
|||

## <a name="historical-statements"></a>Declarações históricas

![Exportação de declarações históricas](./media/pc-export-statements.png)

O histórico de transações de antes de 1 de julho de 2019 é tratado separadamente. As declarações utilizarão os seguintes campos em vez dos atuais.

> [!NOTE]
> O histórico de transações legacy tem uma coluna chamada "Reservado" que corresponde à coluna "Ganhos" na história moderna, exceto que exclui todos os ganhos com estatuto = "Pagamento Enviado".

> [!NOTE]
> Filtros como 3M, 6M ou 12M não se aplicam à secção **de declarações históricas.**

| Nome do campo              | Descrição                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fonte de Receitas          | A fonte das suas receitas com base no local onde ocorreu a transação, como a Microsoft Store, Windows Phone Store, Windows Store 8 ou publicidade                  |
| ID da Encomenda                | Identificador de ordem único. Este ID permite identificar transações de compra com as respetivas transações não-compra, tais como reembolsos ou reembolsos. Ambos terão a mesma identificação de encomenda. Além disso, no caso de uma taxa dividida em que foram utilizados vários métodos de pagamento para uma única compra, permite-lhe ligar as transações de compra. |
| ID de Transação          | Identificador de transações único.                                                                                                                                          |
| Data de Data de Transação   | A data e a hora da transação ocorreu (UTC).                                                                                                                       |
| ID do produto parental       | Identificador de produto único. Se não houver um produto-mãe para a transação, então iD do produto-mãe = ID do produto.                                |
| ID do Produto              | Identificador de produto único.                                                                                                                                              |
| Nome do produto principal     | Nome do produto-mãe. Se não houver um produto-mãe para a transação, então o nome do produto principal = nome do produto.                                  |
| Nome do Produto            | Nome do produto                                                                                                                                                    |
| Tipo de Produto            | Tipo de produto, como App, Add-on ou Game                                                                                                                       |
| Quantidade                | Quando a Fonte de Receita é a Microsoft Store para Negócios, a Quantidade representa o número de licenças adquiridas. Para todas as outras Fontes de Receita, a Quantidade será sempre 1. Mesmo quando uma única transação é dividida em dois itens de linha porque foram utilizados dois métodos de pagamento diferentes, cada item de linha mostrará uma quantidade de 1. |
| Tipo de Transação        | Tipo de transação, tais como compra, reembolso, reversão ou cobrança                                                                                              |
| Método de Pagamento          | Instrumento de pagamento do cliente utilizado para a transação, como Cartão, Faturação de Transportadora Móvel ou PayPal                                                               |
| País / Região        | País/região onde ocorreu a transação                                                                                                                          |
| Fornecedor Local / Vendedor | Fornecedor local/vendedor de registos                                                                                                                                        |
| Moeda de Transação    | Moeda da transação                                                                                                                                            |
| Valor da Transação      | Montante da transação                                                                                                                                              |
| Imposto Remetido            | Montante dos impostos remetidos (taxas de venda, utilização ou IVA/GST)                                                                                                                  |
| Recibos Líquidos            | Valor de transação menos imposto remetido                                                                                                                                   |
| Taxa de Loja               | A percentagem de Recibos Líquidos retidos pela Microsoft como uma taxa para disponibilizar a app ou o addon na Loja                                                      |
| Receitas da App            | Recibos líquidos menos a Taxa de Loja                                                                                                                                       |
| Impostos Retidos          | Montante do imposto sobre o rendimento retido (ot incluído no ficheiro CSV **reservado)**                                                                                                |
| Payment                 | A App Procede menos qualquer retenção na fonte de imposto sobre o rendimento aplicável (valor indicado em Moeda de Transação). Não incluído no ficheiro CSV **reservado.**                               |
| Taxa FX                 | Taxa cambial utilizada para converter moeda de transação em moeda de pagamento                                                                                         |
| Moeda de Pagamento        | Moeda em que o seu pagamento é feito                                                                                                                                       |
| Pagamento Convertido       | Valor de pagamento convertido em Moeda de Pagamento utilizando a Taxa FX                                                                                                         |
| Modelo de remessa fiscal         | Parte responsável pela remessa de impostos (taxas de venda, utilização ou IVA/GST)                                                                                                   |
| Data de elegibilidade   | A data e a hora em que as transações prosseguem tornam-se elegíveis para pagamento (UTC). Quando um pagamento é criado, inclui receitas de transações com uma data de elegibilidade antes da data de criação do pagamento (apenas incluída no ficheiro CSV **reservado).** |
| Cobranças                 | Apresenta uma repartição de todos os detalhes de cobrança agregados na coluna Transaction Amount (apenas incluído para O Mercado Azure; não incluído no ficheiro CSV **reservado).** |
|||

## <a name="next-step"></a>Próximo passo

- [Detalhes da política de dividendos](./payout-policy-details.md)
