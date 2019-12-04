---
title: Configurar alertas de métrica-portal do Azure-banco de dados do Azure para MariaDB
description: Este artigo descreve como configurar e acessar alertas de métricas para o banco de dados do Azure para MariaDB no portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c2367fc58530675783277c2edc4d62efd55a1da8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771885"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Use o portal do Azure para configurar alertas em métricas para o banco de dados do Azure para MariaDB

Este artigo mostra como configurar alertas do banco de dados do Azure para MariaDB usando o portal do Azure. Você pode receber um alerta com base nas métricas de monitoramento para seus serviços do Azure.

O alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. O alerta é disparado quando a condição é atendida pela primeira vez e depois quando essa condição não é mais atendida.

Você pode configurar um alerta para executar as seguintes ações quando ele disparar:
* enviar notificações por email para o administrador de serviços e os administradores
* Envie um email para emails adicionais que você especificar.
* Chamar um webhook

Você pode configurar e obter informações sobre regras de alerta usando:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>Criar uma regra de alerta em uma métrica
1. Na [portal do Azure](https://portal.azure.com/), selecione o banco de dados do Azure para o servidor MariaDB que você deseja monitorar.

2. Na seção **monitoramento** da barra lateral, selecione **alertas** , conforme mostrado:

   ![Selecionar regras de alerta](./media/howto-alert-metric/2-alert-rules.png)

3. Selecione **adicionar alerta de métrica** (+ ícone).

4. A página **criar regra** é aberta, conforme mostrado abaixo. Preencha as informações necessárias:

   ![Formulário adicionar alerta de métrica](./media/howto-alert-metric/4-add-rule-form.png)

5. Na seção **condição** , selecione **Adicionar condição**.

6. Selecione uma métrica na lista de sinais a serem alertados. Neste exemplo, selecione "porcentagem de armazenamento".
   
   ![Selecionar métrica](./media/howto-alert-metric/6-configure-signal-logic.png)

7. Configure a lógica de alerta, incluindo a **condição** (por exemplo, "Maior que"), **limite** (ex. 85 por cento), **agregação de tempo**, **período** de tempo em que a regra de métrica deve ser satisfeita antes do gatilho de alerta (por exemplo, "Nos últimos 30 minutos") e **frequência**.
   
   Selecione **concluído** ao concluir.

   ![Selecionar métrica](./media/howto-alert-metric/7-set-threshold-time.png)

8. Na seção **grupos de ações** , selecione **criar novo** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário "Adicionar grupo de ações" com um nome, um nome curto, uma assinatura e um grupo de recursos.

10. Configure um tipo de ação de **email/SMS/Push/voz** .
    
    Escolha "função de Azure Resource Manager de email" para selecionar proprietários, colaboradores e leitores de assinatura para receber notificações.
   
    Opcionalmente, forneça um URI válido no campo **webhook** se desejar que ele seja chamado quando o alerta for disparado.

    Selecione **OK** quando concluído.

    ![Grupo de ação](./media/howto-alert-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, uma descrição e uma severidade.

    ![Grupo de ação](./media/howto-alert-metric/11-name-description-severity.png) 

12. Selecione **criar regra de alerta** para criar o alerta.

    Em alguns minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas
Depois de criar um alerta, você pode selecioná-lo e executar as seguintes ações:

* Exiba um gráfico que mostra o limite de métrica e os valores reais do dia anterior relevantes para esse alerta.
* **Edite** ou **exclua** a regra de alerta.
* **Desabilitar** ou **habilitar** o alerta, se desejar interromper temporariamente ou retomar as notificações de recebimento.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [Configurar WebHooks em alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Obtenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responsivo.
