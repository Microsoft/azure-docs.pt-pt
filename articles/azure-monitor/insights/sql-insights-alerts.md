---
title: Criar alertas com insights SQL (pré-visualização)
description: Criar alertas com insights SQL no Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726867"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Criar alertas com insights SQL (pré-visualização)
Os insights SQL incluem um conjunto de modelos de regras de alerta que pode usar para criar [regras de alerta no Azure Monitor](../alert/../alerts/alerts-overview.md) para problemas comuns de SQL. As regras de alerta em insights SQL são regras de alerta de registo com base em dados de desempenho armazenados na tabela *InsightsMetrics* em Registos monitores Azure.  

> [!NOTE]
> Para criar um alerta para insights SQL usando um modelo de gestor de recursos, consulte [amostras de modelo de Gestor de Recursos para insights SQL](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights).


> [!NOTE]
> Se tiver pedidos para mais modelos de regras de alerta de insights SQL, por favor envie feedback usando o link na parte inferior desta página ou usando o link de feedback de insights SQL no portal Azure.

## <a name="enable-alert-rules"></a>Ativar regras de alerta 
Utilize os seguintes passos para ativar os alertas no Monitor Azure a partir do portal Azure.As regras de alerta que são criadas serão analisadas a todos os recursos SQL monitorizados no perfil de monitorização selecionado.  Quando uma regra de alerta é ativada, ativa-se na instância ou base de dados específica do SQL.

> [!NOTE]
> Também pode criar [regras](../alerts/alerts-log.md) de alerta de registo personalizado, executando consultas nos conjuntos de dados na tabela *InsightsMetrics* e, em seguida, guardando essas consultas como regra de alerta. 

Selecione **SQL (pré-visualização)** a partir da secção **Insights** do menu Azure Monitor no portal Azure. Clique **em Alertas**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Botão alertas":::

O **painel de alertas** abre-se no lado direito da página. Por predefinição, apresentará alertas disparados para recursos SQL no perfil de monitorização selecionado com base nas regras de alerta que já criou. Selecione **modelos de alerta**, que apresentarão a lista de modelos disponíveis que pode usar para criar uma regra de alerta.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Modelos de alerta":::

Na página **'Criar Alerta',** reveja as definições predefinidos para a regra e edite-as conforme necessário. Também pode selecionar um [grupo de ação](../alerts/action-groups.md) para criar notificações e ações quando a regra de alerta é desencadeada. Clique **Em Ativar** a regra de alerta para criar a regra de alerta uma vez verificada todas as suas propriedades.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Página de regras de alerta":::

Para implementar imediatamente a regra de alerta, clique em **Implementar a regra de alerta**. Clique **em Ver Modelo** se quiser ver o modelo de regra antes de realmente implementá-lo.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Implementar regra de alerta":::

Se optar por visualizar os modelos, **selecione Implementar** a partir da página do modelo para criar a regra de alerta.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Implementar a partir do modelo de visualização":::


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os alertas no Azure Monitor.](../alerts/alerts-overview.md)

