---
title: Criar alertas de monitorização para empregos da Azure Stream Analytics
description: Este artigo descreve como usar o portal Azure para criar monitorização e alertas para trabalhos de Azure Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458729"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Criar alertas para empregos da Azure Stream Analytics

É importante monitorizar o seu trabalho de Azure Stream Analytics para garantir que o trabalho está a funcionar continuamente sem problemas. Este artigo descreve como configurar alertas para cenários comuns que devem ser monitorizados. 

Pode definir regras sobre métricas a partir de dados da Operação Registos através do portal, bem como [programáticamente](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Criar alertas no portal Azure
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Seja alertado quando um trabalho para inesperadamente

O exemplo que se segue demonstra como configurar alertas para quando o seu trabalho entrar num estado falhado. Este alerta é recomendado para todos os trabalhos.

1. No portal Azure, abra o trabalho stream analytics para o que pretende criar um alerta.

2. Na página **De trabalho,** navegue para a secção **de Monitorização.**  

3. Selecione **Métricas,** e, em seguida, **nova regra de alerta**.

   ![Azure portal Stream Analytics alerta supérflua](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. O nome de trabalho do Stream Analytics deve aparecer automaticamente em **RECURSO**. Clique em **Adicionar condição**e selecione todas **as operações administrativas** sob a lógica de **sinal configuração**.

   ![Selecione o nome do sinal para o alerta Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Sob a lógica do **sinal configure,** mude o **Nível de Evento** para **Todos** e mude **o Estado** para **Falhado**. Deixe **o Evento iniciado em** branco e selecione **Done**.

   ![Configure lógica de sinal para alerta Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Selecione um grupo de ação existente ou crie um novo grupo. Neste exemplo, um novo grupo de ação chamado **TIDashboardGroupActions** foi criado com uma ação **de Emails** que envia um e-mail para os utilizadores com o Papel de Gestor de Recursos **do Proprietário** Azure.

   ![Criação de um alerta para um trabalho de Streaming Analytics azure](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Os **GRUPOS DE RECURSOS,** **CONDIÇÃO**e **AÇÃO** devem ter cada um uma entrada. Note que, para que os alertas para o fogo, as condições definidas devem ser satisfeitas. Por exemplo, pode medir o valor médio de uma métrica durante os últimos 15 minutos, de cinco em cinco minutos.

   ![Criar a regra de alerta stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adicione um nome de regra de **Alerta,** **Descrição,** e o seu **Grupo de Recursos** aos DETALHES DE **ALERTA** e clique em Criar a regra de **alerta** para criar a regra para o seu trabalho de Streaming Analytics.

   ![Criar a regra de alerta stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Cenários para monitorizar

Recomenda-se a monitorização do desempenho do seu trabalho em Análise stream. Estas métricas devem ser avaliadas a cada minuto durante os últimos 5 minutos.

|Métrica|Condição|Agregação do tempo|Limiar|Ações Corretivas|
|-|-|-|-|-|
|Utilização su%|Maior que|Máximo|80|Existem vários fatores que aumentam a utilização de SU% Pode escalar com paraparação de consultas ou aumentar o número de unidades de streaming. Para obter mais informações, veja [Leverage query parallelization in Azure Stream Analytics](stream-analytics-parallelization.md) (Tirar partido da paralelização de consultas no Azure Stream Analytics).|
|Erros de tempo de execução|Maior que|Total|0|Examine a atividade ou os registos de diagnóstico e efelo alterações adequadas nas inputs, consultas ou saídas.|
|Atraso na marca de água|Maior que|Máximo|Quando o valor médio desta métrica nos últimos 15 minutos é maior do que a tolerância à chegada tardia (em segundos). Se não tiver modificado a tolerância à chegada tardia, o padrão é definido para 5 segundos.|Tente aumentar o número de SUs ou paralelizar a sua consulta. Para obter mais informações sobre SUs, consulte [Compreender e ajustar unidades](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)de streaming . Para obter mais informações sobre a paralelização da sua consulta, consulte a [paralelização da consulta de Alavancagem no Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erros de desserialização de entrada|Maior que|Total|0|Examine a atividade ou os registos de diagnóstico e efelo alterações adequadas à entrada. Para obter mais informações sobre registos de diagnóstico, consulte [Troubleshoot Azure Stream Analytics utilizando registos](stream-analytics-job-diagnostic-logs.md) de diagnóstico|

## <a name="get-help"></a>Obter ajuda

Para mais detalhes sobre a configuração de alertas no portal Azure, consulte [Receber notificações](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)de alerta .  

Para mais assistência, experimente o nosso [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-get-started.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

