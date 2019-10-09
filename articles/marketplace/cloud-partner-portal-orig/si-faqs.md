---
title: Perguntas frequentes sobre o vendedor insights
description: Perguntas frequentes sobre o recurso de informações do vendedor do Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 059ec175a48cdcdec6214a6581452ec0536bf566
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030446"
---
<a name="seller-insights-faq"></a>Perguntas frequentes sobre o vendedor insights
===================

Este artigo fornece diretrizes para procedimentos de usuário comuns no e perguntas sobre o vendedor insights.


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>Localizar definições para os valores no arquivo de transação baixado
------------------------------------------------------------------

As definições dos valores de métrica no arquivo de transação são encontradas no artigo [definições do vendedor insights](./si-insights-definitions-v4.md).


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>Consulte os detalhes do cliente das transações para as quais fui pago
-------------------------------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status de pagamento**e aplique o filtro para exibir apenas o valor "pago". As colunas a seguir aparecerão contendo os detalhes do cliente: **Nome da empresa**, **email do cliente**, **país do cliente**, estado do **cliente**e **CEP do cliente**.


<a name="calculate-my-open-accounts-receivable"></a>Calcular minhas contas a receber abertas
-------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status de pagamento**e aplique o filtro para exibir apenas o valor "pagamento futuro" e "não está pronto para pagamento". Em seguida, some a coluna rotulada como **valor do pagamento (PC)** .


<a name="calculate-revenue-by-customer-usage-period"></a>Calcular receita por período de uso do cliente
------------------------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da transação**e filtre o valor "pago".   Para cada transação listada, a coluna denominada **valor do pagamento (PC)** representa o valor pago.  Para estimar o período de uso associado à transação, use a **data de cobrança**da coluna, que é uma aproximação aproximada do último dia de uso do período ao qual a transação se aplica.


<a name="calculate-your-bad-debt"></a>Calcular a dívida inadequada
---------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da coleção final**e aplique o filtro para exibir apenas o valor "write off". Em seguida, some a coluna rotulada como **valor do pagamento (PC)** .


<a name="view-payout-or-customer-contact-information"></a>Exibir informações de pagamento ou contato do cliente
-------------------------------------------

Entre como um usuário com a função "proprietário" e não a função "colaborador". Somente a função de proprietário verá o pagamento e as informações do cliente. Você pode saber mais sobre as funções de usuário no artigo [gerenciar usuários](./cloud-partner-portal-manage-users.md).


<a name="calculate-my-advance-payouts"></a>Calcular meus pagamentos antecipados
----------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna chamada **tipo de transação**e aplique o filtro para exibir apenas o valor "encargo". Em seguida, localize a coluna denominada **status da coleção final**e aplique o filtro para exibir apenas o valor "em andamento". Por fim, some a coluna **valor do pagamento (PC)** para calcular todos os avanços pagos antes da coleta do cliente.


<a name="calculate-customer-refunds"></a>Calcular reembolsos de clientes
--------------------------

Depois de baixar suas transações do módulo pagamento, localize a coluna denominada **status da coleção final**e aplique o filtro para exibir apenas o valor "reembolso". Some a coluna **valor da cobrança (PC)** para calcular todos os reembolsos processados para seus clientes.


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>Identificar quais transações envolvem um parceiro de canal da Microsoft
----------------------------------------------------------------

Todas as transações na coluna **tipo de licença do Azure** que são filtradas para exibir os valores "Enterprise por revendedor" e "provedor de soluções na nuvem" envolvem um parceiro de canal da Microsoft. Para obter mais detalhes sobre o parceiro, você pode encontrar o **nome do revendedor** e o **email do revendedor** no download do módulo de pagamento e o download do módulo do cliente.


<a name="identify-trial-usage-and-trial-conversions"></a>Identificar conversões de avaliação e uso de avaliação
------------------------------------------

Os downloads de módulo de pagamento, ordem e uso agora contêm a **data de término da avaliação** para ajudá-lo a entender quando o período de avaliação terminou para esse pedido específico, quando aplicável. Para ver o uso e as ordens de avaliação, localize a coluna **tipo de cobrança de SKU** nos downloads e aplique o filtro para exibir apenas o valor "avaliação". Para ver as conversões de avaliação, localize a coluna **data de término da avaliação** nos downloads e aplique o filtro para exibir apenas os pedidos quando a data de término da **avaliação** for anterior à data de hoje e a coluna de **data de cancelamento** estiver vazia ou posterior à data de término da **avaliação**.


<a name="when-is-my-monthly-payout-calculated"></a>Quando meu pagamento mensal é calculado
------------------------------------

Seus pagamentos são emitidos para você pelo 15º de cada mês para todos os valores prontos para pagamento pelo último dia do calendário do mês anterior. No terceiro dia do mês, a Microsoft calculará o valor de pagamento do mês anterior e atualizará todas as transações de cobrança aplicáveis em seu download com "pagamento futuro" na coluna **status do pagamento** . Essas transações permanecerão nesse estado até que a solicitação de pagamento seja enviada para sua conta bancária, quando o **status** do pagamento for atualizado para "pago", e a "data de pagamento" será atualizada para mostrar a data em que enviamos a solicitação de pagamento ao seu banco.


<a name="calculate-customer-acquisition-and-loss"></a>Calcular a aquisição e a perda do cliente
---------------------------------------

Você pode ver a data em que o cliente comprou primeiro uma de suas ofertas localizando a coluna **data de aquisição** no download do cliente. Da mesma forma, você pode ver a data após a qual elas não tinham mais nenhuma oferta publicada por você, localizando a coluna **data de perda** no download do cliente.


<a name="finding-more-help"></a>Encontrando mais ajuda
-----------------

- [Definições do vendedor insights](./si-insights-definitions-v4.md) – localizar definições para métricas e dados

- [Introdução ao vendedor insights](./si-getting-started.md) – introdução ao recurso de informações do vendedor.

