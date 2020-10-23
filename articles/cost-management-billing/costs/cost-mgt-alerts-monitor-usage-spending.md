---
title: Monitorizar a utilização e as despesas com alertas de custo
description: Este artigo descreve como os alertas de custo o ajudam a monitorizar a utilização e as despesas no Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: c59bd7f9bc8c5049572afdf93343222b30c0007b
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131909"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Utilizar alertas de custo para monitorizar a utilização e as despesas

Este artigo ajuda-o a compreender e a utilizar os alertas do Cost Management para monitorizar a utilização e as despesas do Azure. Os alertas de custo são gerados automaticamente quando os recursos do Azure são consumidos. Os alertas mostram todos os alertas de faturação e gestão de custos ativos num único lugar. Quando o consumo atinge um determinado limiar, o Cost Management gera alertas. Existem três tipos de alertas de custo: alertas de orçamento, alertas de crédito e alertas de quotas de despesas do departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Os alertas de orçamento notificam-no quando as despesas, com base na utilização ou no custo, atingem ou excedem o valor definido na [condição de alerta do orçamento](tutorial-acm-create-budgets.md). Os orçamentos do Cost Management são criados com o portal do Azure ou a API de [Consumo do Azure](/rest/api/consumption).

No portal do Azure, os orçamentos são definidos pelo custo. Com a API de Consumo do Azure, os orçamentos são definidos pelo custo ou pela utilização do consumo. Os alertas de orçamento suportam os orçamentos baseados nos custos e na utilização. Os alertas de orçamento são gerados automaticamente sempre que se verifiquem as condições dos alertas de orçamento. Pode ver todos os alertas de custo no portal do Azure. Sempre que um alerta é gerado, é apresentado nos alertas de custo. É também enviado um e-mail de alerta para as pessoas que se encontram na lista de destinatários dos alertas de orçamento.

Pode utilizar a API de Orçamentos para enviar alertas de e-mail num idioma diferente. Para obter mais informações, veja [Regiões suportadas para e-mails de alerta de orçamento](manage-automation.md#supported-locales-for-budget-alert-emails).

## <a name="credit-alerts"></a>Alertas de crédito

Os alertas de crédito notificam-no quando as suas alocações monetárias de crédito do Azure são consumidas. As alocações monetárias destinam-se às organizações com Contratos Enterprise. Os alertas de crédito são gerados automaticamente quando atinge 90% e 100% do seu saldo de crédito do Azure. Sempre que é gerado um alerta, este é refletido nos alertas de custos e no e-mail enviado aos proprietários da conta.

## <a name="department-spending-quota-alerts"></a>Alertas de quotas de despesas do departamento

Os alertas de quotas de despesas do departamento notificam-no quando as despesas dos departamentos atingem um limiar fixo da quota. As quotas de despesas são configuradas no EA Portal. Sempre que é atingido um limiar, é gerado um e-mail para os proprietários do departamento e este é mostrado nos alertas de custos. Por exemplo, 50% ou 75% da quota.

## <a name="supported-alert-features-by-offer-categories"></a>Funcionalidades de alerta suportadas por categorias de oferta

O suporte para tipos de alertas depende do tipo de conta do Azure que tiver (oferta da Microsoft). A tabela abaixo mostra as funcionalidades de alerta que são suportadas por várias ofertas da Microsoft. Pode ver a lista completa das ofertas da Microsoft em [Compreender os dados do Cost Management](understand-cost-mgt-data.md).

| Tipo de alerta | Contrato Enterprise | Contrato de Cliente da Microsoft | Web Direct/Pay As You Go |
|---|---|---|---|
| Orçamento | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Quotas de despesas do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Ver alertas de custos

Para ver alertas de custos, abra o âmbito pretendido no portal do Azure e selecione **Orçamentos** no menu. Utilize o atalho **Âmbito** para mudar para um âmbito diferente. Selecione **Alertas de custos** no menu. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

![Imagem de exemplo dos alertas apresentados no Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e dispensados aparece na página dos alertas de custos.

Todos os alertas mostram o tipo de alerta. Os alertas de orçamento mostram o motivo pelo qual foram gerados e o nome do orçamento ao qual se aplicam. Cada alerta mostra a data em que foi gerado, o seu estado, o âmbito (subscrição ou grupo de gestão) ao qual se aplica.

**Ativo** e **dispensado** são possíveis estados. O estado "ativo" indica que o alerta ainda é relevante. O estado "dispensado" indica que alguém marcou o alerta para o definir como não sendo mais relevante.

Selecione um alerta a partir da lista para ver os respetivos detalhes. Os detalhes dos alertas mostram mais informações sobre os alertas. Os alertas de orçamento incluem uma ligação que encaminha para o orçamento. Se estiver disponível uma recomendação para um alerta de orçamento, também é mostrada uma ligação que encaminha para a recomendação. Os alertas de orçamento, de crédito e de quota de despesas do departamento têm uma ligação que permite analisar na análise de custos, onde pode explorar os custos para o âmbito do alerta. O exemplo a seguir mostra as despesas de um departamento com os detalhes do alerta.

![Imagem de exemplo a mostrar as despesas de um departamento com os detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Quando visualiza os detalhes de um alerta ignorado, poderá reativá-lo se for necessária uma ação manual. A imagem seguinte mostra um exemplo.

![Imagem de exemplo a mostrar as opções Ignorar e Reativar](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Ver também

- Se ainda não tiver criado um orçamento ou estabelecido as condições de alerta para um orçamento, conclua o tutorial [Criar e gerir orçamentos](tutorial-acm-create-budgets.md).