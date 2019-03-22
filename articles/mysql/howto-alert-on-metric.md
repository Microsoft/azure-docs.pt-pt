---
title: Configurar alertas de métricas da base de dados do Azure para MySQL no portal do Azure
description: Este artigo descreve como configurar e alertas de métricas de acesso da base de dados do Azure para MySQL do portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 387877efea4df30685fcf443a4d114c4feb66401
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098275"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Utilizar o portal do Azure para configurar alertas em métricas da base de dados do Azure para MySQL 

Este artigo mostra-lhe como configurar a base de dados do Azure para MySQL alertas ao utilizar o portal do Azure. Pode receber um alerta com base nas métricas para os seus serviços do Azure de monitorização.

A alerta é acionada quando o valor de uma métrica especificado ultrapassar um limiar que atribui. Os acionadores alerta, quando a condição é a primeira cumpridos e, em seguida, em seguida quando condição que já não é a ser cumprida. 

Pode configurar um alerta para fazer as seguintes ações quando é acionado:
* enviar notificações por e-mail para o administrador de serviços e coadministradores
* Envie e-mail para e-mails adicionais que especificar.
* Chamar um webhook

Pode configurar e obter informações sobre regras de alerta com:
* [Portal do Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta numa métrica do portal do Azure
1. Na [portal do Azure](https://portal.azure.com/), selecione a base de dados do Azure para o servidor MySQL que pretende monitorizar.

2. Sob o **monitorização** secção a barra lateral, selecione **alertas** conforme mostrado:

   ![Selecionar regras de alerta](./media/howto-alert-on-metric/2-alert-rules.png)

3. Selecione **Adicionar alerta de métrica** (ícone +).

4. O **criar regra** é aberta a página conforme mostrado abaixo. Preencha as informações necessárias:

   ![Adicionar formulário de alerta de métrica](./media/howto-alert-on-metric/4-add-rule-form.png)

5. Dentro de **condição** secção, selecione **adicionar condição**.

6. Selecione uma métrica da lista de sinais para ser alertado no. Neste exemplo, selecione "Percentagem de armazenamento".
   
   ![Selecionar métrica](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Configurar a lógica de alerta, incluindo o **condição** (ex. "Maior que"), **limiar** (ex. 85 por cento), **agregação de tempo**, **período** de tempo, a regra de métrica deve ser satisfeita antes dos acionadores de alerta (ex. "Durante os últimos 30 minutos"), e **frequência**.
   
   Selecione **feito** quando terminar.

   ![Selecionar métrica](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. Dentro de **grupos de ação** secção, selecione **criar nova** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário de "Adicionar grupo de ação" com um nome, o nome abreviado, a subscrição e o grupo de recursos.

10. Configurar uma **E-Mail/SMS/Push/voz** tipo de ação.
    
    Escolha "E-Mail do Azure Resource Manager função" para selecionar a subscrição proprietários, contribuidores e leitores para receber notificações.
   
    Opcionalmente, forneça um URI válido no **Webhook** campo se desejar que ele chamado quando o alerta é acionado.

    Selecione **OK** quando concluída.

    ![Grupo de ações](./media/howto-alert-on-metric/10-action-group-type.png)

11. Especifique um nome de regra de alerta, a descrição e a gravidade.

    ![Grupo de ações](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Selecione **criar regra de alerta** para criar o alerta.

    Em poucos minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerir os alertas
Assim que tiver criado um alerta, pode selecioná-lo e fazer as seguintes ações:

* Ver um gráfico que mostra o limiar de métrica e os valores reais do dia anterior relevantes para este alerta.
* **Editar** ou **eliminar** a regra de alerta.
* **Desativar** ou **ativar** o alerta, se quiser temporariamente parar ou retomar a receção das notificações.


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [configurar webhooks nos alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obter um [descrição geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e reativo.
