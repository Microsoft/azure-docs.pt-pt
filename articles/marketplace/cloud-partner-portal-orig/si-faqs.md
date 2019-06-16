---
title: FAQ do vendedor Insights
description: Perguntas mais frequentes sobre a funcionalidade de informações de vendedor do Portal de parceiro de Cloud.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 2719b6b47225576f2eadeb5e5b40b3aa7b39444d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943104"
---
<a name="seller-insights-faq"></a>FAQ do vendedor Insights
===================

Este artigo fornece orientações para dentro de procedimentos de utilizador comuns e perguntas sobre as informações de vendedor.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Encontrar as definições para os valores no ficheiro de transação transferido
------------------------------------------------------------------

As definições de valores de métrica no ficheiro de transação são encontradas no artigo [definições de Insights do vendedor](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Ver detalhes do cliente de transações para o qual eu já foi paga
-------------------------------------------------------------

Depois de transferir suas transações do módulo dividendos, localize a coluna rotulada **dividendos estado**e aplique o filtro para apresentar apenas o valor "pago de saída." As seguintes colunas serão exibido contendo os detalhes do cliente: **Nome da empresa**, **cliente de E-Mail**, **país de cliente**, **estado do cliente**, e **Código Postal do cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Calcular o meu Abra Contas a receber
-------------------------------------

Depois de transferir suas transações do módulo dividendos, localize a coluna rotulada **dividendos estado**e aplique o filtro para apresentar apenas o valor "Dividendos futuras" e "Não está pronto para dividendos." Somar, em seguida, a coluna intitulada **dividendos quantidade (PC)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Calcular a receita por período de utilização do cliente
------------------------------------------

Depois de transferir suas transações do módulo dividendos, localize a coluna rotulada **estado de transação**e o valor "Pago" do filtro.   Para cada transação listada, a coluna intitulada **dividendos quantidade (PC)**  representa a quantidade que foram pagas.  Para estimar o período de utilização associado à transação, utilize a coluna **data de cobrança**, que é uma aproximação do último dia de utilização para o período a que se aplique a transação.


<a name="calculate-your-bad-debt"></a>Calcular a sua dívida incorreto
---------------------

Depois de transferir suas transações do módulo dividendos, localize a coluna rotulada **Final de recolha de estado**e aplique o filtro a apenas apresentar o valor "escrever desativado." Somar, em seguida, a coluna intitulada **dividendos quantidade (PC)** .


<a name="view-payout-or-customer-contact-information"></a>Ver informações de contacto dividendos ou cliente
-------------------------------------------

Inicie sessão como um utilizador com a função "proprietário" e não na função de "contribuinte". A função de proprietário verá informações fiscais e de clientes. Pode encontrar mais informações sobre funções de utilizador no artigo [gerir utilizadores](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcular o meu dividendos avanço
----------------------------

Depois de transferir suas transações do módulo dividendos, localize a coluna rotulada **tipo de transação**e aplique o filtro para apresentar apenas o valor "Cobrança da." Em seguida, localize a coluna intitulada **Final de recolha de estado**e aplique o filtro para apresentar apenas o valor "Em curso". Por fim, soma o **dividendos quantidade (PC)** coluna para calcular todos os avanços pago para si antes da coleção do cliente.


<a name="calculate-customer-refunds"></a>Calcular reembolsos de cliente
--------------------------

Depois de transferir suas transações do módulo dividendos, localize a coluna rotulada **Final de recolha de estado**e aplique o filtro para apresentar apenas o valor "Reembolso". Soma os **quantidade de custos (PC)** coluna para calcular todos os reembolsos processados para os seus clientes.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identificar as transações envolvidos um parceiro de canal da Microsoft
----------------------------------------------------------------

Todas as transações na coluna **tipo de licença do Azure** que são filtrados para exibir os valores "Enterprise por meio de revendedor" e "Fornecedor de soluções de Cloud" envolvem um parceiro de canal de Microsoft. Para obter mais detalhes sobre o parceiro, pode encontrar suas **nome do revendedor** e **respetivo endereço de E-Mail** no download do módulo de dividendos e o download do módulo de cliente.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificar a utilização de avaliação e conversões de avaliação
------------------------------------------

Downloads de módulo de ordem, a utilização e dividendos agora contêm **data de fim da avaliação** para ajudar a compreender quando o período de avaliação terminou para essa ordem específica, quando aplicável. Para ver a utilização de avaliação e orders, localize a **tipo de faturação de SKU** coluna em downloads e aplique o filtro para apresentar apenas o valor "Avaliação". Para ver as conversões de avaliação, localize a **data de fim da avaliação** coluna em downloads e aplicar o filtro para apresentar apenas as orders quando o **data de fim da avaliação** passou a data de hoje e **Cancelar data** coluna está vazio ou posterior a **data de fim da versão de avaliação**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando é calculado a minha dividendos mensal
------------------------------------

Sua dividendos são emitidos para o utilizador, o dia 15 de cada mês, para todos os valores de prontos para dividendos no último dia de calendário do mês anterior. No terceiro dia do mês, a Microsoft irá calcular a quantidade de dividendos do mês anterior e atualizar todas as transações de custos aplicáveis no seu download com "Dividendos futuras" no **dividendos estado** coluna. Essas transações permanecerá nesse Estado até que a solicitação de pagamento é enviada para a sua conta bancária, nesse momento seus **dividendos estado** será atualizado para "Pago de saída" e "Data-dividendos" será atualizada para mostrar a data é submetido a pedido de pagamento para seu banco.


<a name="calculate-customer-acquisition-and-loss"></a>Calcular a aquisição do cliente e perda
---------------------------------------

Pode ver a data quando o cliente pela primeira vez comprou uma das suas ofertas através da localização a **adquirido data** coluna no download do cliente. Da mesma forma, pode ver a data após o qual já não havia qualquer oferta publicada por si através da localização a **perdido data** coluna no download do cliente.


<a name="finding-more-help"></a>Obter mais ajuda
-----------------

- [Definições de Insights do vendedor](./si-insights-definitions-v4.md) -encontrar as definições de métricas e dados

- [Introdução ao vendedor Insights](./si-getting-started.md) -introdução para a funcionalidade de informações de vendedor.

