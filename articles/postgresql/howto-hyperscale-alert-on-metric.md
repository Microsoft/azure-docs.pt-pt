---
title: Configurar alertas de métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
description: Este artigo descreve como configurar e acessar os alertas de métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5f45df16ac294f50e99cc7e05ab6eba43c0ae85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516022"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Usar o portal do Azure para configurar alertas em métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Este artigo mostra como configurar alertas do banco de dados do Azure para PostgreSQL usando o portal do Azure. Você pode receber um alerta com base nas métricas de monitoramento para seus serviços do Azure.

Vamos configurar um alerta para disparar quando o valor de uma métrica especificada ultrapassar um limite. O alerta é disparado quando a condição é atendida pela primeira vez e continua a disparar posteriormente.

Você pode configurar um alerta para executar as seguintes ações quando ele disparar:
* Envie notificações por email para o administrador de serviços e coadministradores.
* Envie um email para emails adicionais que você especificar.
* Chamar um webhook.

Você pode configurar e obter informações sobre regras de alerta usando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica do portal do Azure
1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor do banco de dados do Azure para PostgreSQL que você deseja monitorar.

2. Na seção **monitoramento** da barra lateral, selecione **alertas** , conforme mostrado:

   ![Selecionar regras de alerta](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selecione **nova regra de alerta** (+ ícone).

4. A página **criar regra** é aberta, conforme mostrado abaixo. Preencha as informações necessárias:

   ![Formulário adicionar alerta de métrica](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Na seção **condição** , selecione **Adicionar**.

6. Selecione uma métrica na lista de sinais a serem alertados. Neste exemplo, selecione "porcentagem de armazenamento".
   
   ![Selecionar métrica](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta:

    * **Operador** (ex. "Maior que")
    * **Valor de limite** (ex. 85 por cento)
    * Quantidade de **granularidade de agregação** de tempo que a regra de métrica deve ser satisfeita antes que o alerta seja disparado (por exemplo, "Nos últimos 30 minutos")
    * e **frequência de avaliação** (ex. "1 minuto")
   
   Selecione **concluído** ao concluir.

   ![Selecionar métrica](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Na seção **grupos de ações** , selecione **criar novo** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário "Adicionar grupo de ações" com um nome, um nome curto, uma assinatura e um grupo de recursos.

    ![Grupo de ação](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Configure um tipo de ação de **email/SMS/Push/voz** .
    
    Escolha "função de Azure Resource Manager de email" para enviar notificações para proprietários, colaboradores e leitores de assinatura.
   
    Selecione **OK** quando concluído.

    ![Grupo de ação](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, uma descrição e uma severidade.

    ![Grupo de ação](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selecione **criar regra de alerta** para criar o alerta.

    Em alguns minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, você pode selecioná-lo e executar as seguintes ações:

* Exiba um gráfico que mostra o limite de métrica e os valores reais do dia anterior relevantes para esse alerta.
* **Edite** ou **exclua** a regra de alerta.
* **Desabilitar** ou **habilitar** o alerta, se desejar interromper temporariamente ou retomar as notificações de recebimento.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [Configurar WebHooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responsivo.
