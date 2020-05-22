---
title: Relatórios de pagamento - Mercado comercial da Microsoft
description: Os relatórios do Pagamento mostram-lhe detalhes sobre o dinheiro que ganhou com a sua oferta, incluindo o valor do pagamento e quando será pago.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6639026d071b496027996036a81f6bc66e0185e3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744817"
---
# <a name="payout-summaries"></a>Resumos de dividendos

O resumo do Pagamento mostra-lhe detalhes sobre o dinheiro que ganhou com a Microsoft. Também lhe permite saber quando receberá os pagamentos e quanto será pago.

Se vender produtos no Azure Marketplace, também verá informações sobre pagamentos bem sucedidos no resumo do Pagamento. Para mais detalhes, consulte as Políticas de [Participação no Mercado microsoft Azure](https://docs.microsoft.com/legal/marketplace/participation-policy) e o Acordo de Editor estoiro do [Microsoft Azure Marketplace](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt).

> [!NOTE]
> Para ser elegível para pagamento, as suas receitas devem atingir o limiar de [pagamento](payment-thresholds-methods-timeframes.md) de $50. Para mais detalhes sobre o limiar de pagamento consulte esta página e reveja o acordo de programador de aplicações.

> [!NOTE]
> Se procura apoio em relação a pagamentos, incluindo configurar contas de pagamento, pagamentos em falta, colocar pagamentos em espera, ou qualquer outra coisa, suporte de contato [aqui](https://developer.microsoft.com/windows/support).

## <a name="access-the-payout-summary-pages"></a>Aceda às páginas de resumo do pagamento

Para abrir uma das páginas resumidas do pagamento:

1. Selecione o ícone Payout no canto superior direito.
2. Selecione histórico de transações, pagamentos ou dados de exportação.

## <a name="transaction-history-page"></a>Página de histórico de transações

Esta página exibe todos os seus ganhos individuais, incluindo a data, o tipo e o ganho para cada um. Pode selecionar um período de tempo para visualizar, e também pode filtrar por ID de inscrição, Programa, ID de pagamento, tipo de ganho, alavanca e Estado. Os dados estão disponíveis para o exercício em curso (1 de julho a 30 de junho) e para os dois exercícios anteriores.

Para ver mais detalhes sobre um ganho, selecione a seta para baixo no lado direito da página. Isto mostrará a alavanca, o valor das receitas e o produto. Se algum destes dados estiver indisponível, mas necessitar de acesso aos mesmos, contacte o [suporte](https://developer.microsoft.com/windows/support). Se o ganho for o resultado de um ajustamento e não de uma transação, os campos de produtos não serão apresentados.

Para exportar qualquer um dos dados de transação nesta página, utilize a página de dados da **Exportação.**

## <a name="payments-page"></a>Página de pagamentos

Os totais desta página representam todos os programas em que participa. Pode filtrar por ID do Participante, Programa, ID de pagamento e tipo de ganho. Os montantes são dados em dólares americanos. O valor pago também é apresentado em pagamento à moeda.

| Área                   | Descrição                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| Total pago este ano   | O total combinado pago-lhe este ano, em dólares americanos, por todos os seus programas       |
| Próximo pagamento estimado | O próximo pagamento que vem até si (mesmo que haja outros em breve), em dólares americanos |
| Último pagamento           | A quantia (em dólares americanos), nome do programa e programa do seu pagamento mais recente           |
| Pagamentos por fonte     | Montante dos pagamentos (em dólares americanos) representados por programa nos últimos 12 meses           |
| Pagamentos               | Selecione **Pago** ou **Pendente** e, em seguida, separe como desejado. Para mais detalhes de um pagamento específico selecione **Ver**. Para fazer o download de uma cópia da declaração de remessa de pagamentos, selecione **Download**. Como os dados do histórico de transações podem demorar até 24 horas a aparecer, pode não ver os ganhos associados imediatamente. |
|||

Para exportar qualquer um dos dados desta página, selecione **Export e** siga as instruções na página de dados da Exportação.

## <a name="transaction-history-page"></a>Página de histórico de transações

Esta página exibe todos os seus ganhos individuais, incluindo a data, o tipo e o ganho para cada um. Pode selecionar um período de tempo para visualizar e filtrar por ID de inscrição, Programa, ID de pagamento, tipo de ganho, alavanca e Estado. Os dados estão disponíveis para o exercício em curso (1 de julho a 30 de junho) e para os dois exercícios anteriores.

Para ver mais detalhes sobre um ganho, selecione a seta para baixo no lado direito da página. Isto mostrará a alavanca, o valor das receitas e o produto. Se algum destes dados estiver indisponível, mas necessitar de acesso aos mesmos, contacte o [suporte](https://developer.microsoft.com/windows/support)de contacto]. Se o ganho for o resultado de um ajustamento e não de uma transação, os campos de produtos não serão apresentados.

Para exportar qualquer um dos dados de transação nesta página, selecione **Export e** siga as instruções na página de dados da Export. Os ficheiros exportados da página do Histórico de Transações mostram dados em moeda de transação, ganhos em moeda de transação e dólares americanos, e o valor pago em pagamento à moeda.

## <a name="payment-status"></a>Estado dos pagamentos

| Estatuto de ganho           | Razão                                                                                                                                      | Ação do parceiro necessária?                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| Não processado              | O ganho é elegível para pagamento. Permanece neste estado por um período de arrefecimento, tal como definido no guia do programa para o programa incentivo. | Não                                                         |
| A seguir                 | Ordem de pagamento gerada pendente de revisões internas antes do pagamento ser processado                                                               | Não                                                         |
| Fatura fiscal pendente      | A sua fatura fiscal é incompleta ou inválida                                                                                                  | Precisa atualizar a sua fatura de imposto antes de ser pago |
| Rejeitado durante revisão   | O pagamento foi rejeitado durante a revisão                                                                                                     | Contacte o [suporte da Microsoft](https://developer.microsoft.com/windows/support) para obter mais informações                      |
| Falhou                   | O pagamento falhou devido a um erro do sistema da Microsoft                                                                                         | Contacte o [suporte da Microsoft](https://developer.microsoft.com/windows/support) para obter mais informações                      |
| Em curso              | O pagamento está em andamento                                                                                                                 | Não                                                         |
| Pagamento incorreto        | A recuperação de pagamentos está em andamento                                                                                                       | Não                                                         |
| Enviados                     | O pagamento foi enviado para o seu banco.                                                                                                     | Não                                                         |
| Reprocessamento             | O pagamento encontrou um erro do sistema da Microsoft e está a ser reprocessado                                                                  | Não                                                         |
| Invertido                 | O pagamento foi revertido pelo seu banco e será enviado novamente no próximo ciclo de pagamentos                                                     | Não                                                         |
| Fatura fiscal rejeitada     | A sua fatura fiscal foi rejeitada durante a revisão. Todos os pagamentos pendentes estarão suspensos até que a revisão da fatura fiscal esteja concluída.                 | Contacte o [suporte da Microsoft](https://developer.microsoft.com/windows/support) para obter mais informações                      |
| Fatura fiscal em revisão | A sua fatura fiscal está a ser revista. O seu pagamento será liberado assim que a fatura do imposto tiver sido aprovada.                                   | Não                                                         |
| Rejected                 | O pagamento foi rejeitado pelo seu banco.                                                                                                      | Contacte o seu banco para obter mais informações.                             |
|||

## <a name="export-data-page"></a>Página de dados de exportação

Siga estas instruções para exportar dados.

A página de dados da Exportação não se atualiza por si só. Pode ser necessário refrescar a página manualmente para ver os dados mais recentes.

O filtro pode resultar num erro **sem dados disponíveis.** Isto provavelmente significa que você deixou o período de tempo padrão selecionado em três meses e, em seguida, selecionou um ID de pagamento de um ganho que está fora desse período. Expanda o seu período de tempo e tente novamente.

## <a name="payments"></a>Pagamentos

![Pagamentos de exportação](./media/pc-export-payments.png)

Esta opção fornece um download dos pagamentos recebidos no seu banco para um determinado programa, o imposto associado e o valor de ganho agregado. Este relatório é usado para muitos programas do Partner Center, pelo que algumas colunas podem ser inaplicáveis ao seu relatório. As colunas estão marcadas abaixo.

| Nome da coluna              | Descrição                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| participanteID            | A identidade primária do sócio que ganha ao abrigo do programa                                                                             |
| participanteIDType        | Normalmente programa ID para programas de incentivo e ID de vendedor para programas de loja                                                                |
| nome de participante          | Nome do parceiro de ganhos                                                                                                               |
| programaNome              | Nome do programa de incentivo/loja                                                                                                              |
| ganhou                   | Valor auferido na moeda Pay To para aquele programa/participanteID                                                                       |
| earnedUSD                | Valor ganho para o ID do programa/participante, em USD                                                                                      |
| retidoTax              | Montante do imposto retido no Pay To currency para o programa/participanteID                                                               |
| vendasImposto                 | Montante total do imposto sobre as vendas no Pay To currency para o programa/participanteID (aplicável apenas para programas de incentivo)                   |
| serviçoFeeTax            | Quantidade total de serviçoFeeTax em Pay Para moeda para o programa/participanteID (aplicável apenas para programas de loja e Azure Marketplace) |
| total pagamento             | Pagamento total em moeda local excluindo a retenção na fonte e incluindo o imposto sobre as vendas (se aplicável) para o programa/participanteID   |
| currencyCode             | Pagar ao código cambial                                                                                                                      |
| pagamentoMétodo            | O método usado para pagar ao parceiro, por exemplo, transferência bancária electrónica, nota de crédito                                                     |
| paymentID                | Identificador único para o pagamento. Este número é geralmente visível no seu extrato bancário (aplicável apenas aos pagamentos SAP).              |
| pagamentoEstatuto            | Estado dos pagamentos                                                                                                                            |
| pagamentoStatusDescription | Descrição amigável do estado do pagamento                                                                                                    |
| pagamentoData              | O pagamento da data foi enviado da Microsoft                                                                                                      |
|||

## <a name="transaction-history"></a>Histórico de transações

![História da transação de exportação](./media/pc-export-transaction.png)

Esta opção fornece um download de cada item de linha de ganho que você vê na página de histórico de Transações, tipo de ganho, data, valor de transação associado, cliente, produto e outros detalhes transacionais aplicáveis aos seus programas.

| Nome da coluna                    | Descrição                                                                                                                              | Aplicabilidade para incentivos/loja/mercado azure           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | Identificador único para cada ganho                                                                                                       | Todos                                                            |
| participanteId                  | A identidade primária do sócio que ganha ao abrigo do programa                                                                            | Todos                                                            |
| participanteIdType              | Id do programa principal para programas de incentivo e vendedor IF para programas de loja e Mercado Azure                                          | Todos                                                            |
| nome de participante                | Nome do parceiro de ganhos                                                                                                              | Todos                                                            |
| parceiroCountryCode             | Localização/país/região do parceiro de ganhos                                                                                                  | Todos                                                            |
| programaNome                    | Nome do programa de incentivo/loja                                                                                                             | Todos                                                            |
| transaçãoId                  | Identificador único para a transação                                                                                                    | Todos                                                            |
| transaçãoMoeda            | Moeda em que ocorreu a transação original do cliente (esta não é a moeda de localização do parceiro)                                     | Todos                                                            |
| transacçãoData                | Data da transação. Útil para programas onde muitas transações contribuem para um ganho                                           | Todos                                                            |
| transacçõesExchangeRate        | Data de câmbio utilizada para mostrar o valor de usd de transação correspondente                                                                 | Todos                                                            |
| transaçõesMontante              | Montante de transação na moeda de transação original com base no qual o resultado é gerado                                              | Todos                                                            |
| transaçõesAmountUSD           | Valor da transação em USD                                                                                                                | Todos                                                            |
| alavanca                          | Indica regra de negócio para o ganho                                                                                                  | Todos                                                            |
| taxa de ganho                    | Taxa de incentivo aplicada no valor da transação para gerar um ganho                                                                      | Todos                                                            |
| quantidade                       | Varia com base no programa. Indica quantidade faturada para programas transacionais                                                            | Todos                                                            |
| quantidadeTipo de letra                   | Indica tipo de quantidade, por exemplo, quantidade faturada, MAU                                                                             | Todos                                                            |
| earningType                    | Indica se é taxa, desconto, cooperativa, venda, e assim por diante                                                                                          | Todos                                                            |
| ganhoS                  | Valor de Ganho na moeda de transação original                                                                                      | Todos                                                            |
| ganhoSAmountUSD               | Valor de Ganho em USD                                                                                                                    | Todos                                                            |
| data de ganhos                    | Data do ganho                                                                                                                      | Todos                                                            |
| cálculoDatade                | Data em que o ganho foi calculado no sistema                                                                                            | Todos                                                            |
| earningExchangeRate            | Taxa de câmbio utilizada para mostrar o valor correspondente do USD                                                                                  | Todos                                                            |
| exchangeRateDate               | Data de câmbio utilizada para calcular o Valor Ganho USD                                                                                   | Todos                                                            |
| pagamentoAmountWOTax             | Valor de ganho (sem imposto) em Pagamento apenas para pagamentos "enviados"                                                                 | Todos                                                            |
| pagamentoMoeda                | Pague à moeda escolhida pelo parceiro no perfil de Pagamento. Mostrado apenas para pagamentos enviados                                                   | Todos                                                            |
| pagamentoSExchangeRate            | Taxa de câmbio utilizada para calcular pagamentoAmountWOTax em moeda de pagamento utilizando ExchangeRateDate                                            | Todos                                                            |
| reivindicaçãoId                        | Identificador único para reclamação                                                                                                              | Incentivos - alguns programas apenas                                |
| planId                         | Identificador único para plano                                                                                                               | Incentivos - alguns programas apenas                                |
| pagamentoId                      | Identificador único para o pagamento. Este número é geralmente visível no seu extrato bancário                                                 | Pagamentos SAP apenas                                              |
| pagamentoEstatuto                  | Estado dos pagamentos                                                                                                                           | Todos                                                            |
| pagamentoStatusDescription       | Descrição amigável do estado do pagamento                                                                                                   | Todos                                                            |
| customerId                     | Sempre estará em branco                                                                                                                     | Programas de incentivo apenas (exceção: OEM) e Azure Marketplace |
| nome do cliente                   | Sempre estará em branco                                                                                                                     | Programas de incentivo apenas (exceção: OEM) e Azure Marketplace |
| partNumber                     | Sempre estará em branco                                                                                                                     | Alguns programas de Incentivo e Loja e Mercado Azure        |
| nome do produto                    | Nome do produto ligado à transação                                                                                                       | Todos                                                            |
| productId                      | Identificador de produto único                                                                                                                | Loja e Mercado Azure                                    |
| parentProductId                | Identificador de produto parental único. Se não houver um produto-mãe para a transação, então o ID do Produto-Mãe = ID do produto. | Loja e Mercado Azure                                    |
| nome do produto parental              | Nome do produto-mãe. Se não houver um produto-mãe para a transação, então nome do produto-mãe = Nome do produto.   | Loja e Mercado Azure                                    |
| productType                    | Tipo de produto, como App, Add-on ou Game                                                                                        | Loja e Mercado Azure                                    |
| faturaNúmero                  | Número de fatura (aplicável apenas para EA)                                                                                                  | Incentivo e Azure Marketplace - alguns programas apenas           |
| subscriptionId                 | Identificador de subscrição associado ao cliente                                                                                         | Incentivo - alguns programas apenas                                 |
| subscriçãoStartDate          | Data de início da subscrição                                                                                                                  | Incentivo - alguns programas apenas                                 |
| subscriçãoEndDate            | Data final da subscrição                                                                                                                    | Incentivo - alguns programas apenas                                 |
| revendedorId                     | Identificador de revendedor                                                                                                                      | Incentivo - alguns programas apenas                                 |
| revendedorNome                   | Nome do revendedor                                                                                                                            | Incentivo - alguns programas apenas                                 |
| distribuidorId                  | Identificador de distribuidor                                                                                                                   | Incentivo - alguns programas apenas                                 |
| nome do distribuidor                | Nome do distribuidor                                                                                                                         | Incentivo - alguns programas apenas                                 |
| acordoNúmero                | Número do contrato                                                                                                                         | Incentivo - alguns programas apenas                                 |
| acordoStartDate             | Data de início do contrato                                                                                                                     | Incentivo - alguns programas apenas                                 |
| acordoEndDate               | Data de fim do contrato                                                                                                                       | Incentivo - alguns programas apenas                                 |
| workload                       | Carga de trabalho                                                                                                                                 | Incentivo - alguns programas apenas                                 |
| transaçãoTipo                | Tipo de transação, tais como compra, reembolso, reversão ou cobrança                                                               | Loja e Mercado Azure                                    |
| fornecedor localVendedor            | Fornecedor local/vendedor de registos                                                                                                          | Armazenar apenas                                                     |
| impostosRedos                    | Montante do imposto remetido (impostos sobre vendas, utilização ou IVA/GST)                                                                                   | Loja e Mercado Azure                                    |
| taxRemitModel                  | Parte responsável pela reemissão de impostos (impostos sobre vendas, utilização ou IVA/GST)                                                                    | Armazenar apenas                                                     |
| lojaFee                       | O valor retido pela Microsoft como uma taxa para tornar a app ou add-on disponível na Loja                                           | Armazenar apenas                                                     |
| transacçãoPaymentMethod       | Instrumento de pagamento ao cliente utilizado para a transação, tais como Card, Mobile Carrier Billing ou PayPal                                | Loja e Mercado Azure                                    |
| tpan                           | Indica a rede de anúncios de terceiros                                                                                                     | Loja - Anúncios apenas                                               |
| clientePaís                | País/região cliente                                                                                                                         | Loja e Mercado Azure                                    |
| clienteCity                   | Cidade cliente                                                                                                                            | Loja e Mercado Azure                                    |
| clienteEstado                  | Estado do cliente                                                                                                                           | Loja e Mercado Azure                                    |
| clienteZip                    | Código postal/postal do cliente                                                                                                                 | Loja e Mercado Azure                                    |
| compraTypeCode               | Sempre estará em branco                                                                                                                     | Programa de incentivos - CRI                                        |
| compraOrderType              | Sempre estará em branco                                                                                                                     | Programa de incentivos - CRI                                        |
| compraOrderCoverageStartDate | Sempre estará em branco                                                                                                                     | Programa de incentivos - CRI                                        |
| compraOrderCoverageEndDate   | Sempre estará em branco                                                                                                                     | Programa de incentivos - CRI                                        |
| programaOfnofn           |                                                                                                                                          | Programa de incentivos - CRI                                        |
| TenantID                       |                                                                                                                                          | Programas de incentivo                                             |
| externalReferenceId            | Identificador único para o programa                                                                                                        | Programas de Pagamento Direto (Incentivo e Loja)                      |
| externalReferenceIdLabel       | Etiqueta de identificador única                                                                                                                  | Programas de Pagamento Direto (Incentivo e Loja)                      |
|||

## <a name="historical-statements"></a>Declarações históricas

![Exportação de declarações históricas](./media/pc-export-statements.png)

O histórico de transações de antes de 1 de julho de 2019 é tratado separadamente. As declarações utilizarão os seguintes campos em vez dos atuais.

> [!NOTE]
> O histórico de transações legados tem uma coluna chamada "Reservado" que corresponde à coluna "Ganhos" na história moderna, exceto que exclui todos os ganhos com estatuto = "Pagamento Enviado".

> [!NOTE]
> Filtros como 3M, 6M ou 12M não se aplicam à secção **de declarações históricas.**

| Nome do campo              | Descrição                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fonte de Receita          | A fonte das suas receitas com base no local onde ocorreu a transação, como microsoft Store, Windows Phone Store, Windows Store 8 ou publicidade                  |
| ID da Encomenda                | Identificador de encomenda único. Este ID permite identificar transações de compra com as respetivas transações não-compras, tais como reembolsos ou cobranças. Ambos terão a mesma identificação da Ordem. Além disso, no caso de uma taxa dividida em que foram utilizados múltiplos métodos de pagamento para uma única compra, permite-lhe ligar as transações de compra. |
| ID de Transação          | Identificador de transações único.                                                                                                                                          |
| Hora da data da transação   | A data e a hora da transação ocorreram (UTC).                                                                                                                       |
| ID do produto-mãe       | Identificador de produto parental único. Se não houver um produto-mãe para a transação, então o ID do Produto-Mãe = ID do produto.                                |
| ID do Produto              | Identificador de produto único.                                                                                                                                              |
| Nome do produto-mãe     | Nome do produto-mãe. Se não houver um produto-mãe para a transação, então nome do produto-mãe = Nome do produto.                                  |
| Nome do Produto            | Nome do produto                                                                                                                                                    |
| Tipo de Produto            | Tipo de produto, como App, Add-on ou Game                                                                                                                       |
| Quantidade                | Quando a Fonte de Receita é a Microsoft Store para Negócios, a Quantidade representa o número de licenças adquiridas. Para todas as outras Fontes de Receita, a Quantidade será sempre 1. Mesmo quando uma única transação é dividida em itens de duas linhas porque foram utilizados dois métodos de pagamento diferentes, cada item de linha mostrará uma quantidade de 1. |
| Tipo de Transação        | Tipo de transação, tais como compra, reembolso, reversão ou cobrança                                                                                              |
| Método de Pagamento          | Instrumento de pagamento ao cliente utilizado para a transação, tais como Card, Mobile Carrier Billing ou PayPal                                                               |
| País / Região        | País/região onde ocorreu a transação                                                                                                                          |
| Fornecedor Local / Vendedor | Fornecedor local/vendedor de registos                                                                                                                                        |
| Moeda de transação    | Moeda da transação                                                                                                                                            |
| Valor da Transação      | Montante da transação                                                                                                                                              |
| Imposto Remetido            | Montante do imposto remetido (impostos sobre vendas, utilização ou IVA/GST)                                                                                                                  |
| Recibos Líquidos            | Valor de transação menos imposto remetido                                                                                                                                   |
| Taxa de loja               | A percentagem de Recibos Líquidos retidos pela Microsoft como uma taxa para disponibilizar a app ou add-on na Loja                                                      |
| Receitas da aplicação            | Recibos líquidos menos a Taxa de Loja                                                                                                                                       |
| Impostos Retidos          | Montante do imposto sobre o rendimento retido (ot incluído no ficheiro **CSV Reservado)**                                                                                                |
| Payment                 | App Procede menos qualquer retenção na retenção na taxa de rendimento aplicável (valor mostrado na Moeda de Transação). Não incluído no ficheiro CSV **Reservado.**                               |
| Taxa FX                 | Taxa de câmbio utilizada para converter moeda de transação em moeda de pagamento                                                                                         |
| Moeda de Pagamento        | Moeda em que o seu pagamento é feito                                                                                                                                       |
| Pagamento Convertido       | Valor de pagamento convertido em Moeda de Pagamento utilizando a Taxa FX                                                                                                         |
| Modelo de remessa fiscal         | Parte responsável pela reemissão de impostos (impostos sobre vendas, utilização ou IVA/GST)                                                                                                   |
| Hora da data da elegibilidade   | A data e a hora em que as receitas da transação se tornam elegíveis para pagamento (UTC). Quando um pagamento é criado, inclui receitas de transação com uma Data de Elegibilidade Hora antes da data de criação do pagamento (apenas incluído no ficheiro CSV **Reservado).** |
| Cobranças                 | Mostra uma repartição de todos os detalhes de cobrança agregados na coluna Transaction Amount (apenas incluído si para o Mercado Azure; não incluído no ficheiro CSV **Reservado).** |
|||

## <a name="next-step"></a>Próximo passo

- [Detalhes da política de dividendos](./payout-policy-details.md)
