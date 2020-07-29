---
title: Confiúde os alertas de monitorização para os trabalhos do Azure Stream Analytics
description: Este artigo descreve como usar o portal Azure para configurar monitorização e alertas para trabalhos do Azure Stream Analytics.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: de8b69cbe3117a3ec248cee4808b676b39c56658
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324799"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos do Azure Stream Analytics

É importante monitorizar o seu trabalho Azure Stream Analytics para garantir que o trabalho está a funcionar continuamente sem problemas. Este artigo descreve como configurar alertas para cenários comuns que devem ser monitorizados. 

Pode definir regras sobre métricas a partir de dados de Registos de Operação através do portal, bem como [programáticamente](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no portal Azure
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Fique alerta quando um trabalho para inesperadamente

O exemplo a seguir demonstra como configurar alertas para quando o seu trabalho entra num estado falhado. Este alerta é recomendado para todos os trabalhos.

1. No portal Azure, abra o trabalho stream Analytics para o que pretende criar um alerta.

2. Na página **'Trabalho',** navegue para a secção **de Monitorização.**  

3. Selecione **Métricas**e, em seguida, **Nova regra de alerta**.

   ![Portal Azure Stream Analytics alerta configuração](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. O nome de trabalho stream Analytics deve aparecer automaticamente em **RECURSO**. Clique **em Adicionar a condição**e selecione Todas as **operações administrativas** sob **lógica de sinal de configuração**.

   ![Selecione o nome do sinal para alerta stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Na **lógica de sinal de configuração,** altere o **nível de evento** para **todos** e **altere o Estado** para **Falhar**. Leave **Event iniciado por** blank e selecione **Feito**.

   ![Lógica de sinal de configuração para alerta stream analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Selecione um grupo de ação existente ou crie um novo grupo. Neste exemplo, um novo grupo de ação chamado **TIDashboardGroupActions** foi criado com uma ação **de Emails** que envia um e-mail aos utilizadores com o Papel de Gestor de Recursos **Azure Proprietário.**

   ![Criação de um alerta para um trabalho da Azure Streaming Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Os **GRUPOS DE RECURSO,** **CONDIÇÃO**e **AÇÃO** devem ter uma entrada. Note que para que os alertas de incêndio, as condições definidas devem ser cumpridas. Por exemplo, pode medir o valor médio de uma métrica durante os últimos 15 minutos, de cinco em cinco minutos.

   ![Criar regra de alerta stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adicione um **nome de regra de alerta**, **Descrição**, e o seu **Grupo de Recursos** aos DETALHES DE **ALERTA** e clique em Criar regra **de alerta** para criar a regra para o seu trabalho stream Analytics.

   ![Criar regra de alerta stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Cenários a monitorizar

Recomenda-se que os seguintes alertas sejam necessários para monitorizar o desempenho do seu trabalho stream Analytics. Estas métricas devem ser avaliadas a cada minuto durante o último período de 5 minutos.

|Métrica|Condição|Agregação do tempo|Limiar|Ações corretivas|
|-|-|-|-|-|
|Utilização su%|Maior que|Máximo|80|Existem múltiplos fatores que aumentam a utilização por PARTE. Pode escalar com paralelização de consulta ou aumentar o número de unidades de streaming. Para obter mais informações, veja [Leverage query parallelization in Azure Stream Analytics](stream-analytics-parallelization.md) (Tirar partido da paralelização de consultas no Azure Stream Analytics).|
|Erros de tempo de execução|Maior que|Total|0|Examine a atividade ou registos de recursos e eseri as alterações adequadas às entradas, consultas ou saídas.|
|Atraso da marca de água|Maior que|Máximo|Quando o valor médio desta métrica ao longo dos últimos 15 minutos for maior do que a tolerância de chegada tardia (em segundos). Se não tiver modificado a tolerância de chegada tardia, o padrão é definido para 5 segundos.|Tente aumentar o número de SUs ou paralelizar a sua consulta. Para obter mais informações sobre as SUs, consulte [compreender e ajustar unidades de streaming.](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job) Para obter mais informações sobre a paralelização da sua consulta, consulte [a paralelização da consulta de alavancagem no Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erros de deserialização de entradas|Maior que|Total|0|Examine a atividade ou registos de recursos e faça alterações apropriadas na entrada. Para obter mais informações sobre registos de recursos, consulte [Troubleshoot Azure Stream Analytics utilizando registos de recursos](stream-analytics-job-diagnostic-logs.md)|

## <a name="next-steps"></a>Passos seguintes

* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

