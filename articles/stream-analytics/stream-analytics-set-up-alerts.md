---
title: Configurar alertas de monitoramento para trabalhos de Azure Stream Analytics
description: Este artigo descreve como usar o portal do Azure para configurar o monitoramento e alertas para trabalhos de Azure Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458729"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos de Azure Stream Analytics

É importante monitorar seu trabalho de Azure Stream Analytics para garantir que o trabalho esteja sendo executado continuamente sem problemas. Este artigo descreve como configurar alertas para cenários comuns que devem ser monitorados. 

Você pode definir regras em métricas de dados de logs de operação por meio do portal, bem como [programaticamente](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no portal do Azure
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Receber alertas quando um trabalho parar inesperadamente

O exemplo a seguir demonstra como configurar alertas para quando seu trabalho entrar em um estado de falha. Esse alerta é recomendado para todos os trabalhos.

1. No portal do Azure, abra o trabalho de Stream Analytics para o qual você deseja criar um alerta.

2. Na página **trabalho** , navegue até a seção **monitoramento** .  

3. Selecione **métricas**e **nova regra de alerta**.

   ![Instalação de alertas do portal do Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. O nome do trabalho de Stream Analytics deve aparecer automaticamente em **recurso**. Clique em **Adicionar condição**e selecione **todas as operações administrativas** em **Configurar lógica de sinal**.

   ![Selecione o nome do sinal para Stream Analytics alerta](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Em **Configurar lógica de sinal**, **altere nível de evento** para **todos** e altere o **status** para **falha**. Deixe o **evento iniciado por** em branco e selecione **concluído**.

   ![Configurar a lógica de sinal para Stream Analytics alerta](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Selecione um grupo de ações existente ou crie um novo grupo. Neste exemplo, um novo grupo de ação chamado **TIDashboardGroupActions** foi criado com uma ação **emails** que envia um email aos usuários com a função **proprietário** Azure Resource Manager.

   ![Configurando um alerta para um trabalho do Azure streaming Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Os grupos de **recursos**, **condições**e **ações** devem ter uma entrada. Observe que, para que os alertas sejam acionados, as condições definidas precisam ser atendidas. Por exemplo, pode medir o valor médio de uma métrica durante os últimos 15 minutos, de cinco em cinco minutos.

   ![Criar Stream Analytics regra de alerta](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adicione um **nome de regra de alerta**, uma **Descrição**e seu **grupo de recursos** aos **detalhes do alerta** e clique em **criar regra de alerta** para criar a regra para seu trabalho de Stream Analytics.

   ![Criar Stream Analytics regra de alerta](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Cenários para monitorar

Os alertas a seguir são recomendados para monitorar o desempenho de seu trabalho de Stream Analytics. Essas métricas devem ser avaliadas a cada minuto durante o último período de 5 minutos.

|Métrica|Condição|Agregação de tempo|Limiar|Ações corretivas|
|-|-|-|-|-|
|% De utilização de SU|Mais do que|Máximo|80|Há vários fatores que aumentam a utilização de SU%. Você pode dimensionar com a paralelização de consulta ou aumentar o número de unidades de streaming. Para obter mais informações, veja [Leverage query parallelization in Azure Stream Analytics](stream-analytics-parallelization.md) (Tirar partido da paralelização de consultas no Azure Stream Analytics).|
|Erros de tempo de execução|Mais do que|Total|0|Examine a atividade ou os logs de diagnóstico e faça as alterações apropriadas nas entradas, na consulta ou nas saídas.|
|Atraso da marca d' água|Mais do que|Máximo|Quando o valor médio dessa métrica nos últimos 15 minutos for maior que a tolerância de chegada tardia (em segundos). Se você não tiver modificado a tolerância de chegada tardia, o padrão será definido como 5 segundos.|Tente aumentar o número de SUs ou paralelizar sua consulta. Para obter mais informações sobre o SUs, consulte [entender e ajustar unidades de streaming](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Para obter mais informações sobre como paralelizar sua consulta, consulte [alavancar a paralelização de consulta no Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erros de desserialização de entrada|Mais do que|Total|0|Examine a atividade ou os logs de diagnóstico e faça as alterações apropriadas na entrada. Para obter mais informações sobre logs de diagnóstico, consulte [solucionar problemas Azure Stream Analytics usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Obter ajuda

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-get-started.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

