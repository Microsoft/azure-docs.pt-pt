---
title: Monitorizar a utilização e as despesas com alertas de custo | Microsoft Docs
description: Este artigo descreve como os alertas de custo o ajudam a monitorizar a utilização e as despesas no Azure Cost Management.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988503"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Utilizar alertas de custo para monitorizar a utilização e as despesas

Este artigo ajuda-o a compreender e a utilizar os alertas do Cost Management para monitorizar a utilização e as despesas do Azure. Os alertas de custo são gerados automaticamente quando os recursos do Azure são consumidos. Os alertas mostram todos os alertas de faturação e gestão de custos ativos num único lugar. Quando o consumo atinge um determinado limiar, o Cost Management gera alertas. Existem três tipos de alertas de custo: alertas de orçamento, alertas de crédito e alertas de quotas de despesas do departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Os alertas de orçamento notificam-no quando as despesas, com base na utilização ou no custo, atingem ou excedem o valor definido na [condição de alerta do orçamento](tutorial-acm-create-budgets.md). Os orçamentos do Cost Management são criados com o portal do Azure ou a API de [Consumo do Azure](https://docs.microsoft.com/rest/api/consumption).

No portal do Azure, os orçamentos são definidos pelo custo. Com a API de Consumo do Azure, os orçamentos são definidos pelo custo ou pela utilização do consumo. Os alertas de orçamento suportam os orçamentos baseados nos custos e na utilização. Os alertas de orçamento são gerados automaticamente sempre que se verifiquem as condições dos alertas de orçamento. Pode ver todos os alertas de custo no portal do Azure. Sempre que um alerta é gerado, é apresentado nos alertas de custo. É também enviado um e-mail de alerta para as pessoas que se encontram na lista de destinatários dos alertas de orçamento.

## <a name="credit-alerts"></a>Alertas de crédito

Os alertas de crédito notificam-no quando as alocações monetárias do crédito do Azure são consumidas. As alocações monetárias destinam-se a organizações com Contratos Enterprise. Os alertas de crédito são gerados automaticamente quanto atinge 90% e 100% do saldo do crédito do Azure. Sempre que um alerta é gerado, será refletido nos alertas de custo e no e-mail enviado para os proprietários da conta.

## <a name="department-spending-quota-alerts"></a>Alertas de quotas de despesas do departamento

Os alertas de quotas de despesas do departamento notificam-no quando as despesas do departamento atingirem um limiar fixo da quota. As quotas das despesas são configuradas no EA Portal. Sempre que um limiar é atingido, é enviado um e-mail para os proprietários do departamento e é apresentado nos alertas de custo. Por exemplo, quando atinge 50% ou 75% da quota.

## <a name="supported-alert-features-by-offer-categories"></a>Funcionalidades de alerta suportadas pelas categorias de ofertas

O suporte dos tipos de alerta depende do tipo de conta do Azure que possui (oferta da Microsoft). A tabela seguinte mostra as funcionalidades de alerta que são suportadas pelas várias ofertas da Microsoft. Pode ver a lista completa das ofertas da Microsoft em [Compreender os dados do Cost Management](understand-cost-mgt-data.md).

| Tipo de alerta | Contrato Enterprise | Contrato de Cliente da Microsoft | Web Direct/Pay As You Go |
|---|---|---|---|
| Orçamento | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Quota de despesas do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Ver os alertas de custo

Para ver os alertas de custo, abra o âmbito desejado no portal do Azure e selecione **Orçamentos** no menu. Utilize o atalho **Âmbito** para mudar para um âmbito diferente. Selecione **Alertas de custo** no menu. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

![Imagem de exemplo dos alertas apresentados no Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e ignorados aparece na página de alertas de custo.

Todos os alertas mostram o tipo de alerta. Um alerta de orçamento mostra o motivo pelo qual foi gerado e o nome do orçamento ao qual se aplica. Cada alerta mostra a data em que foi gerado, o seu estado, o âmbito (subscrição ou grupo de gestão) ao qual se aplica.

Estados possíveis: **ativo** e **ignorado**. O estado ativo indica que o alerta ainda é relevante. O estado ignorado indica que alguém marcou o alerta para o definir como não relevante.

Selecione um alerta na lista para ver os detalhes associados. Os detalhes do alerta mostram mais informações sobre o alerta. Os alertas de orçamento incluem uma ligação para o orçamento. Se estiver disponível uma recomendação para um alerta de orçamento, será também apresentada uma ligação para essa recomendação. Os alertas de orçamento, de crédito e de quotas de despesas do departamento apresentam uma ligação para permitir analisar na análise de custos, onde pode explorar os custos do âmbito do alerta. O exemplo a seguir mostra as despesas de um departamento com os detalhes do alerta.

![Imagem de exemplo a mostrar as despesas de um departamento com os detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Quando visualiza os detalhes de um alerta ignorado, poderá reativá-lo se for necessária uma ação manual. A imagem seguinte mostra um exemplo.

![Imagem de exemplo a mostrar as opções Ignorar e Reativar](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Consulte também

- Se ainda não tiver criado um orçamento ou estabelecido as condições de alerta para um orçamento, conclua o tutorial [Criar e gerir orçamentos](tutorial-acm-create-budgets.md).
