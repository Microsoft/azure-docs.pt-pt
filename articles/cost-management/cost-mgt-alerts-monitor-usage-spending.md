---
title: Monitorizar a utilização e gastos com alertas de custo | Documentos da Microsoft
description: Este artigo descreve como o custo alertas Ajuda monitorizar utilização e gastos no Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: feb7fcdd9005ef131acadfc63defbe4caeaca014
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57991934"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Utilizar alertas de custo para monitorizar a utilização e gastos

Este artigo ajuda-o a compreender e utilizar os alertas de gestão de custos para monitorizar o seu Azure utilização e gastos. Custos de alertas são geradas automaticamente com base em quando os recursos do Azure são consumidos. Alertas mostram todos os Active Directory, gestão de custos e alertas de faturação em conjunto num único local. Quando o consumo de atinge um determinado limite, os alertas são gerados pelo Cost Management. Existem três tipos de alertas de custo: alertas de orçamento, alertas de crédito e alertas de quota de gastos do departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Alertas de orçamento notificá-lo quando gastos, com base na utilização ou custo, atingirem ou exceder o montante definido no [condição do orçamento de alerta](tutorial-acm-create-budgets.md). Orçamentos de gestão de custos são criados no portal do Azure ou o [Azure-consumo](https://docs.microsoft.com/rest/api/consumption) API.

No portal do Azure, orçamentos são definidos pelo custo. Utilizar a API de consumo do Azure, orçamentos são definidos pelo custo ou pela utilização de consumo. Alertas de orçamento suportam orçamentos baseados em utilização e custos. Alertas de orçamento são gerados automaticamente sempre que estiverem reunidas as condições de alerta de orçamento. Pode ver todos os alertas de custo no portal do Azure. Sempre que é gerado um alerta, é apresentada nos alertas de custo. Um e-mail de alerta também é enviado para as pessoas na lista de destinatários de alertas do orçamento.

## <a name="credit-alerts"></a>Alertas de crédito

Alertas de crédito notificá-lo quando seus compromissos monetários do crédito do Azure são consumidos. São compromissos monetários para organizações com contratos Enterprise. Crédito alertas são geradas automaticamente a 90% e a 100% do seu saldo de crédito do Azure. Sempre que é gerado um alerta, é refletida nos alertas de custo e no e-mail enviado para os proprietários de conta.

## <a name="department-spending-quota-alerts"></a>Alertas de quota de gastos departamento

Alertas de quota de gastos departamento notificá-lo quando gastos do departamento atingem um limiar fixo da quota. Quotas de gastos são configuradas no portal do EA. Sempre que um limiar for cumprido gera uma mensagem de e-mail para os proprietários de departamento e é apresentado nos alertas de custo. Por exemplo, 50% ou 75% da quota.

## <a name="supported-alert-features-by-offer-categories"></a>Funcionalidades de alerta de suportadas por categorias da oferta

Suporte para tipos de alertas depende do tipo de conta do Azure que tem (Microsoft oferecem). A tabela seguinte mostra as funcionalidades de alertas que são suportadas por várias ofertas da Microsoft. Pode ver a lista completa de ofertas da Microsoft em [dados de compreender a gestão de custos](understand-cost-mgt-data.md).

| Tipo de alerta | Contrato Enterprise | Contrato de Cliente Microsoft | Web direct/Pay-As-You-Go |
|---|---|---|---|
| Orçamento | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Quota de gastos do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Alertas da vista de custos

Para ver os alertas de custo, abra o âmbito pretendido no portal do Azure e selecione **orçamentos** no menu. Utilize o **âmbito** envenenadas para mudar para outro âmbito. Selecione **custo alertas** no menu. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

![Imagem de exemplo de alertas, mostrado na gestão de custos](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e ignoradas é apresentada na página de alertas de custo.

Todos os alertas mostram o tipo de alerta. Um alerta de orçamento mostra o motivo por que foi gerado e o nome do orçamento aplica-se. Cada alerta mostra a data em que foi gerado, o estado e o âmbito (subscrição ou gestão de grupo) que o alerta se aplica a.

Possível estado inclui **Active Directory** e **dispensada**. Estado do Active Directory indica que o alerta ainda é relevante. Estado ignorado indica que alguém tiver marcado o alerta para o definir como não mais relevante.

Selecione um alerta na lista para ver os detalhes. Detalhes do alerta mostram mais informações sobre o alerta. Alertas de orçamento incluem um link para o orçamento. Se uma recomendação está disponível para um alerta de orçamento, uma ligação para a recomendação também é mostrada. Orçamento, crédito e alertas de quota de gastos departamento tem uma ligação para analisar em análise de custo em que pode explorar os custos de âmbito do alerta. O exemplo seguinte mostra a gastos para um departamento com detalhes do alerta.

![Imagem de exemplo que mostra gastos para um departamento com detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Ao visualizar os detalhes de um alerta ignorado, pode reativá-la se é necessária qualquer ação manual. A imagem seguinte mostra um exemplo.

![Apresentação de imagem de exemplo dispensar e reativar as opções](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Consulte também

- Se já não tiver criado um orçamento ou definir condições de alerta para um orçamento, execute o [criar e gerir orçamentos](tutorial-acm-create-budgets.md) tutorial.
