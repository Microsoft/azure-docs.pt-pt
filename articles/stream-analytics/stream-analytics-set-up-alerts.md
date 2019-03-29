---
title: Configurar a monitorização de alertas para tarefas do Azure Stream Analytics
description: Este artigo descreve como utilizar o portal do Azure para configurar a monitorização e alertas para tarefas do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 52db8217cc1e1f84d25ab896be9b42db3bf6bd81
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769502"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para tarefas do Azure Stream Analytics

É importante monitorizar a tarefa do Azure Stream Analytics para garantir que a tarefa está em execução continuamente sem problemas. Este artigo descreve como configurar alertas para cenários comuns que devem ser monitorizados. 

As regras podem ser configuradas em métricas através do portal e podem ser configuradas [programaticamente](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre os dados de registos de operações.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no portal do Azure

O exemplo seguinte demonstra como configurar alertas para quando a tarefa entra num Estado com falhas. Este alerta é recomendado para todas as tarefas.

1. No portal do Azure, abra a tarefa de Stream Analytics que pretende criar um alerta para.

2. Sobre o **tarefa** página, navegue para o **monitorização** secção.  

3. Selecione **métricas**e, em seguida, clique em **nova regra de alerta**.

   ![Configuração de alertas Stream Analytics portal do Azure](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. O nome da tarefa de Stream Analytics automaticamente deve aparecer sob **recursos**. Clique em **adicionar condição**e selecione **operações administrativas todos** sob **lógica de sinal de configurar**.

   ![Selecione o nome do sinal de alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Sob **configurar lógica de sinal**, alterar **nível do evento** para **todos os** e altere **estado** para **falha** . Deixe **evento iniciado por** em branco e clique em **feito**.

   ![Configurar lógica de sinal de alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Selecione um grupo de ação existente ou criar um novo grupo. Neste exemplo, um novo grupo de ação designado **TIDashboardGroupActions** foi criado com uma **E-mails** ação que envia um e-mail para os utilizadores com o **proprietário** recursos do Azure Função de gestor.

   ![Configurando um alerta para uma tarefa de análise de transmissão em fluxo do Azure](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. O **RESOURCE**, **condição**, e **grupos de ação** cada um tem uma entrada. Tenha em atenção que para os alertas acionar, as condições definidas tem de ser cumpridos. Por exemplo, pode medir o valor médio de uma métrica de durante os últimos 15 minutos, a cada 5 minutos.

   ![Criar regra de alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adicionar uma **nome da regra de alerta**, **Descrição**e seu **grupo de recursos** para o **detalhes do alerta** e clique em **criar alerta regra** para criar a regra para a sua tarefa do Stream Analytics.

   ![Criar regra de alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Cenários para monitorizar

Os seguintes alertas são recomendados para monitorização do desempenho da sua tarefa do Stream Analytics. Essas métricas devem ser avaliadas a cada minuto ao longo do último período de 5 minutos.

|Métrica|Condição|Agregação de Tempo|Limiar|Ações corretivas|
|-|-|-|-|-|
|% De utilização SU|Maior que|Máximo|80|Existem vários fatores que aumentam a utilização em SU %. Pode dimensionar com da paralelização de consulta ou aumentar o número de unidades de transmissão em fluxo. Para obter mais informações, veja [Leverage query parallelization in Azure Stream Analytics](stream-analytics-parallelization.md) (Tirar partido da paralelização de consultas no Azure Stream Analytics).|
|Erros de tempo de execução|Maior que|Total|0|Examine a atividade ou registos de diagnóstico e efetue as alterações necessárias para as entradas, consulta ou saídas.|
|Atraso de marca d'água|Maior que|Máximo|Quando o valor médio desta métrica durante os últimos 15 minutos é o maior tolerância de chegada tardia (em segundos). Se não tiver modificado a tolerância de chegada tardia, a predefinição está definida como 5 segundos.|Tente aumentar o número de SUs ou paralelização de sua consulta. Para obter mais informações sobre o SUs, consulte [compreender e ajustar as unidades transmissão em fluxo](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Para obter mais informações sobre a paralelização de sua consulta, consulte [paralelização de consultas de tirar partido do Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erros de desserialização de entrada|Maior que|Total|0|Examine a atividade ou registos de diagnóstico e efetue as alterações necessárias para a entrada. Para obter mais informações sobre os registos de diagnóstico, consulte [resolução de problemas do Azure Stream Analytics com os registos de diagnóstico](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Obter ajuda

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Para obter assistência, tente nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos Seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-get-started.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

