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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230106"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Utilizar alertas de custo para monitorizar a utilização e as despesas

Este artigo ajuda você a entender e usar alertas de gerenciamento de custos para monitorar o uso e as despesas do Azure. Os alertas de custo são gerados automaticamente com base quando os recursos do Azure são consumidos. Os alertas mostram todos os alertas de cobrança e gerenciamento de custos ativos em um só lugar. Quando o consumo atinge um determinado limite, os alertas são gerados pelo gerenciamento de custos. Há três tipos de alertas de custo: alertas de orçamento, alertas de crédito e alertas de cota de gastos de departamento.

## <a name="budget-alerts"></a>Alertas de orçamento

Os alertas de orçamento notificam você quando gastos, com base no uso ou no custo, atingem ou excedem a quantidade definida na [condição de alerta do orçamento](tutorial-acm-create-budgets.md). Os orçamentos de gerenciamento de custos são criados usando o portal do Azure ou a API de [consumo do Azure](https://docs.microsoft.com/rest/api/consumption) .

No portal do Azure, os orçamentos são definidos pelo custo. Usando a API de consumo do Azure, os orçamentos são definidos por custo ou pelo uso de consumo. Os alertas de orçamento dão suporte a orçamentos baseados em custo e de uso. Os alertas de orçamento são gerados automaticamente sempre que as condições de alerta de orçamento são atendidas. Você pode exibir todos os alertas de custo no portal do Azure. Sempre que um alerta é gerado, ele é mostrado em alertas de custo. Um email de alerta também é enviado para as pessoas na lista de destinatários de alertas do orçamento.

## <a name="credit-alerts"></a>Alertas de crédito

Alertas de crédito notificam você quando seus compromissos monetários de crédito do Azure são consumidos. Compromissos monetários são para organizações com contratos Enterprise. Os alertas de crédito são gerados automaticamente às 90% e às 100% do saldo de crédito do Azure. Sempre que um alerta é gerado, ele é refletido nos alertas de custo e no email enviado aos proprietários da conta.

## <a name="department-spending-quota-alerts"></a>Alertas de cota de gastos do departamento

Os alertas de cota de gastos do departamento notificam você quando os gastos dos departamentos atingem um limite fixo da cota. As cotas de gastos são configuradas no portal de EA. Sempre que um limite é atingido, ele gera um email para proprietários do departamento e é mostrado em alertas de custo. Por exemplo, 50% ou 75% da cota.

## <a name="supported-alert-features-by-offer-categories"></a>Recursos de alerta com suporte por categorias de oferta

O suporte para tipos de alertas depende do tipo de conta do Azure que você tem (oferta da Microsoft). A tabela a seguir mostra os recursos de alerta que têm suporte de várias ofertas da Microsoft. Você pode exibir a lista completa de ofertas da Microsoft em [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md).

| Tipo de alerta | Contrato Enterprise | Contrato de Cliente da Microsoft | Web Direct/pago conforme o uso |
|---|---|---|---|
| Visto | ✔ | ✔ | ✔ |
| Crédito | ✔ |✘ | ✘ |
| Cota de gastos do departamento | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Exibir alertas de custo

Para exibir alertas de custo, abra o escopo desejado no portal do Azure e selecione **orçamentos** no menu. Use o **escopo** Pill para alternar para um escopo diferente. Selecione **alertas de custo** no menu. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

![Imagem de exemplo de alertas mostrados no gerenciamento de custos](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

O número total de alertas ativos e ignorados é exibido na página alertas de custo.

Todos os alertas mostram o tipo de alerta. Um alerta de orçamento mostra o motivo pelo qual ele foi gerado e o nome do orçamento ao qual ele se aplica. Cada alerta mostra a data em que foi gerado, seu status e o escopo (assinatura ou grupo de gerenciamento) ao qual o alerta se aplica.

O status possível inclui **ativo** e **ignorado**. Status ativo indica que o alerta ainda é relevante. Status ignorado indica que alguém marcou o alerta para defini-lo como não mais relevante.

Selecione um alerta na lista para exibir seus detalhes. Os detalhes do alerta mostram mais informações sobre o alerta. Os alertas de orçamento incluem um link para o orçamento. Se uma recomendação estiver disponível para um alerta de orçamento, um link para a recomendação também será mostrado. Os alertas de cota de despesas de orçamento, crédito e departamento têm um link para analisar a análise de custo, em que você pode explorar os custos do escopo do alerta. O exemplo a seguir mostra o gasto para um departamento com detalhes de alerta.

![Imagem de exemplo mostrando os gastos de um departamento com detalhes do alerta](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Ao exibir os detalhes de um alerta ignorado, você poderá reativá-lo se a ação manual for necessária. A imagem seguinte mostra um exemplo.

![Imagem de exemplo mostrando opções de ignorar e reativar](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Consulte também

- Se você ainda não tiver criado um orçamento ou definir condições de alerta para um orçamento, conclua o tutorial [criar e gerenciar orçamentos](tutorial-acm-create-budgets.md) .
