---
title: Monitorar o uso e gastos com alertas de custo | Microsoft Docs
description: Este artigo descreve como os alertas de custo ajudam a monitorar o uso e os gastos no gerenciamento de custos do Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: alavital
ms.custom: ''
ms.openlocfilehash: 4be484cdff2014f11c872da9a246ef8406447712
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988503"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Utilizar alertas de custo para monitorizar a utilização e as despesas

Este artigo ajuda você a entender e usar alertas de gerenciamento de custos para monitorar o uso e as despesas do Azure. Os alertas de custo são gerados automaticamente com base quando os recursos do Azure são consumidos. Os alertas mostram todos os alertas de cobrança e gerenciamento de custos ativos em um só lugar. Quando o consumo atinge um determinado limite, os alertas são gerados pelo gerenciamento de custos. Há três tipos de alertas de custos: alertas de orçamento, alertas de crédito e alertas de quota de despesas do departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Os alertas de orçamento notificam você quando gastos, com base no uso ou no custo, atingem ou excedem a quantidade definida na [condição de alerta do orçamento](tutorial-acm-create-budgets.md). Os orçamentos de gerenciamento de custos são criados usando o portal do Azure ou a API de [consumo do Azure](https://docs.microsoft.com/rest/api/consumption) .

No portal do Azure, os orçamentos são definidos pelo custo. Ao utilizar a API de Consumo do Azure, os orçamentos são definidos por custo ou utilização. Os alertas de orçamento suportam orçamentos baseados em custos e na utilização. Os alertas de orçamento são gerados automaticamente sempre que as condições de alerta de orçamento são cumpridas. Pode ver todos os alertas de custos no portal do Azure. Sempre que é gerado um alerta, este é mostrado nos alertas de custos. Também é enviado um e-mail de alerta para as pessoas presentes na lista de destinatários dos alertas do orçamento.

## <a name="credit-alerts"></a>Alertas de crédito

Os alertas de crédito notificam-no quando as suas alocações monetárias de crédito do Azure são consumidas. As alocações monetárias destinam-se às organizações com Contratos Enterprise. Os alertas de crédito são gerados automaticamente quando atinge 90% e 100% do seu saldo de crédito do Azure. Sempre que é gerado um alerta, este é refletido nos alertas de custos e no e-mail enviado aos proprietários da conta.

## <a name="department-spending-quota-alerts"></a>Alertas de quotas de despesas do departamento

Os alertas de quotas de despesas do departamento notificam-no quando as despesas dos departamentos atingem um limiar fixo da quota. As quotas de despesas são configuradas no EA Portal. Sempre que é atingido um limiar, é gerado um e-mail para os proprietários do departamento e este é mostrado nos alertas de custos. Por exemplo, 50% ou 75% da quota.

## <a name="supported-alert-features-by-offer-categories"></a>Funcionalidades de alerta suportadas por categorias de oferta

O suporte para tipos de alertas depende do tipo de conta do Azure que tiver (oferta da Microsoft). A tabela abaixo mostra as funcionalidades de alerta que são suportadas por várias ofertas da Microsoft. Você pode exibir a lista completa de ofertas da Microsoft em [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md).

| Tipo de alerta | Enterprise Agreement | Contrato de Cliente Microsoft | Web Direct/pago conforme o uso |
|---|---|---|---|
| Orçamento | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Quotas de despesas do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Ver alertas de custos

Para ver alertas de custos, abra o âmbito pretendido no portal do Azure e selecione **Orçamentos** no menu. Utilize o atalho **Âmbito** para mudar para um âmbito diferente. Selecione **Alertas de custos** no menu. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

![Imagem de exemplo dos alertas mostrados no Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e dispensados aparece na página dos alertas de custos.

Todos os alertas mostram o tipo de alerta. Os alertas de orçamento mostram o motivo pelo qual foram gerados e o nome do orçamento ao qual se aplicam. Cada alerta mostra a data em que foi gerado, seu status e o escopo (assinatura ou grupo de gerenciamento) ao qual o alerta se aplica.

**Ativo** e **dispensado** são possíveis estados. O estado "ativo" indica que o alerta ainda é relevante. O estado "dispensado" indica que alguém marcou o alerta para o definir como não sendo mais relevante.

Selecione um alerta a partir da lista para ver os respetivos detalhes. Os detalhes dos alertas mostram mais informações sobre os alertas. Os alertas de orçamento incluem uma ligação que encaminha para o orçamento. Se estiver disponível uma recomendação para um alerta de orçamento, também é mostrada uma ligação que encaminha para a recomendação. Os alertas de orçamento, de crédito e de quota de despesas do departamento têm uma ligação que permite analisar na análise de custos, onde pode explorar os custos para o âmbito do alerta. O exemplo a seguir mostra o gasto para um departamento com detalhes de alerta.

![Imagem de exemplo mostrando os gastos de um departamento com detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Ao exibir os detalhes de um alerta ignorado, você poderá reativá-lo se a ação manual for necessária. A imagem seguinte mostra um exemplo.

![Imagem de exemplo mostrando opções de ignorar e reativar](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Ver também

- Se você ainda não tiver criado um orçamento ou definir condições de alerta para um orçamento, conclua o tutorial [criar e gerenciar orçamentos](tutorial-acm-create-budgets.md) .
