---
title: Insights do Vendedor FAQ
description: Perguntas frequentes sobre a funcionalidade "Insights do Vendedor" do Portal do Parceiro cloud.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 011558baa43ee3db2803e9229d1d15df5158d668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80285389"
---
<a name="seller-insights-faq"></a>Insights do Vendedor FAQ
===================

Este artigo fornece orientações para procedimentos comuns do utilizador dentro e perguntas sobre insights de vendedor.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Encontre definições para os valores no ficheiro de transação descarregado
------------------------------------------------------------------

As definições dos valores métricos no ficheiro de transações encontram-se no artigo [Definições](./si-insights-definitions-v4.md)de Insights do Vendedor .


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Consulte os detalhes do cliente das transações pelas quais fui pago
-------------------------------------------------------------

Depois de descarregar as suas transações a partir do módulo Payout, localize a coluna rotulada **de Estado**de Pagamento , e aplique o filtro apenas para exibir o valor "Pago". As seguintes colunas aparecerão contendo os dados do cliente: **Nome da empresa,** **E-mail do Cliente,** **País de Cliente,** **Estado cliente**e Código Postal do **Cliente.**


<a name="calculate-my-open-accounts-receivable"></a>Calcular as minhas contas abertas a receber
-------------------------------------

Depois de descarregar as suas transações a partir do módulo Payout, localize a coluna rotulada **de Estado**de Pagamento , e aplique o filtro apenas para exibir o valor "Pagamento De Futuro" e "Não Pronto para Pagamento". Em seguida, soma a coluna rotulada **Valor de Pagamento (PC)**.


<a name="calculate-revenue-by-customer-usage-period"></a>Calcular as receitas por período de utilização do cliente
------------------------------------------

Depois de descarregar as suas transações a partir do módulo 'Pagamento', localize o Estado de **Transação**da coluna, e filtre o valor "Pago".   Para cada transação listada, a coluna rotulada de Valor de **Pagamento (PC)** representa o valor que lhe foi pago.  Para estimar o período de utilização associado à transação, utilize a data de **carregamento**da coluna, que é uma aproximação próxima do último dia de utilização para o período a que a transação se aplica.


<a name="calculate-your-bad-debt"></a>Calcule a sua dívida má
---------------------

Depois de descarregar as suas transações a partir do módulo Payout, localize a coluna com o nome De Estatuto de **Recolha Final**, e aplique o filtro apenas para exibir o valor "Write Off". Em seguida, soma a coluna rotulada **Valor de Pagamento (PC)**.


<a name="view-payout-or-customer-contact-information"></a>Ver informações de pagamento ou de contacto com o cliente
-------------------------------------------

Inscreva-se como utilizador com a função "proprietário" e não com a função de "contribuinte". Apenas o papel do proprietário verá o pagamento e a informação do cliente. Pode saber mais sobre as funções dos utilizadores no artigo [Gerir utilizadores](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcular os meus pagamentos antecipados
----------------------------

Depois de descarregar as suas transações a partir do módulo 'Pagamento', localize a coluna com a etiqueta Tipo de **Transação**, e aplique o filtro apenas para exibir o valor "Cobrar". Em seguida, localize a coluna com o estatuto de **recolha final,** e aplique o filtro apenas para mostrar o valor "Em Progresso". Por fim, soma a coluna **Valor de Pagamento (PC)** para calcular todos os adiantamentos que lhe foram pagos antes da cobrança do cliente.


<a name="calculate-customer-refunds"></a>Calcular reembolsos de clientes
--------------------------

Depois de descarregar as suas transações a partir do módulo Payout, localize a coluna com o nome De Estatuto de **Recolha Final**, e aplique o filtro apenas para mostrar o valor "Reembolso". Soma a coluna **Valor De Cobrança (PC)** para calcular todos os reembolsos processados para os seus clientes.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identifique quais as transações envolvidas num Microsoft Channel Partner
----------------------------------------------------------------

Todas as transações na coluna Do Tipo de **Licença Azure** que são filtradas para exibir os valores "Enterprise through Reseller" e "Cloud Solution Provider" envolvem um Parceiro do Microsoft Channel. Para mais detalhes sobre o parceiro, pode encontrar o seu **Nome revendedor** e **e-mail revendedor** no download do módulo Payout e o download do módulo Cliente.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificar o uso do ensaio e as conversões experimentais
------------------------------------------

Os downloads do módulo de encomenda, utilização e pagamento contêm agora data de fim de **prova** para ajudá-lo a entender quando o período experimental terminou para essa ordem específica, quando aplicável. Para ver o uso do ensaio e as encomendas, localize a coluna **SKU Billing Type** nos downloads e aplique o filtro apenas para mostrar o valor "Trial". Para ver conversões de ensaios, localize a coluna **Data final** experimental nos downloads e aplique o filtro apenas para exibir ordens quando a data de fim de **julgamento** terminar a data de hoje e a coluna De cancelamento da **data** estiver vazia ou posterior à data de fim de **julgamento**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando é calculado o meu pagamento mensal
------------------------------------

Os seus pagamentos são-lhe emitidos até ao dia 15 de cada mês por todos os valores prontos para serem pagamentos até ao último dia civil do mês anterior. No terceiro dia do mês, a Microsoft calculará o valor do pagamento do mês anterior e atualizará todas as transações de cobrança aplicáveis no seu download com "Upcoming Payout" na coluna **'Pagamento** de Pagamento'. Essas transações permanecerão nesse estado até que o pedido de pagamento seja enviado para a sua conta bancária, altura em que o seu Estado de **Pagamento** será atualizado para "Paid out", e a "Data de Pagamento" será atualizada para mostrar a data em que submetemos o pedido de pagamento ao seu banco.


<a name="calculate-customer-acquisition-and-loss"></a>Calcular a aquisição e perda de clientes
---------------------------------------

Pode ver a data em que o cliente comprou pela primeira vez uma das suas ofertas, localizando a coluna **Data Adquirida** no download do cliente. Da mesma forma, pode ver a data após a qual já não tinham qualquer oferta publicada por si, localizando a coluna **Date Lost** no download do cliente.


<a name="finding-more-help"></a>Encontrar mais ajuda
-----------------

- [Definições de Insights do Vendedor](./si-insights-definitions-v4.md) - Encontrar definições para métricas e dados

- [Começando com O Seller Insights](./si-getting-started.md) - Introdução à funcionalidade Seller Insights.

