---
title: Alerta sobre problemas nos serviços Cloud do Azure através da integração de diagnóstico do Azure com o Azure Application Insights | Documentos da Microsoft
description: Monitor para problemas como falhas no arranque, falhas e função reciclam ciclos em serviços Cloud do Azure com o Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: 219ba632d7688f1a428378309828b689698d2fe5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409554"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alerta relativamente a problemas nos serviços Cloud do Azure através da integração de diagnóstico do Azure com o Azure Application Insights

Neste artigo, descrevemos como configurar regras de alerta que monitorizar para problemas como falhas no arranque, falhas e função reciclam ciclos em serviços Cloud do Azure (funções web e de trabalho).

O método descrito neste artigo se baseia a [integração de diagnóstico do Azure com o Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)e o recém-lançado [alertas de registo para o Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) capacidade.

## <a name="define-a-base-query"></a>Definir uma consulta de base

Para começar, iremos definir uma consulta de base que obtém os eventos de registo de eventos do Windows a partir do canal do Windows Azure, que são capturados para o Application Insights como registos de rastreio.
Estes registos podem ser utilizados para detectar uma variedade de problemas em serviços Cloud do Azure, como falhas no arranque, falhas de tempo de execução e reciclam loops.

> [!NOTE]
> A consulta de base abaixo verifica a existência de problemas numa janela de tempo de 30 minutos e assume uma latência de 10 minutos em ingerir os registos de telemetria. Estas predefinições podem ser configuradas como quiser.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Verificação de IDs de evento específico

Depois de recuperar os eventos de registo de eventos do Windows, os problemas específicos podem ser detetados por verificar a existência de suas propriedades de ID e a mensagem de evento correspondente (veja exemplos abaixo).
Basta combine a consulta base acima com uma das consultas abaixo e é utilizadas combinados consulta ao definir a regra de alerta de registo.

> [!NOTE]
> Nos exemplos abaixo, irá ser detetado um problema se mais de três eventos encontrados durante a janela de tempo analisado. Este padrão pode ser configurado para alterar a sensibilidade de regra de alerta.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Criar um alerta

No menu de navegação dentro do seu recurso do Application Insights, aceda a **alertas**e, em seguida, selecione **nova regra de alerta**.

![Captura de ecrã de criar regra](./media/proactive-cloud-services/001.png)

Na **criar regra** janela, no **definir condição do alerta** secção, clique em **adicionar critérios**e, em seguida, selecione **pesquisa de registos personalizado**.

![Captura de ecrã de definir critérios de condição de alerta](./media/proactive-cloud-services/002.png)

Na **consulta de pesquisa** caixa, cole a consulta combinada que preparou no passo anterior.

Em seguida, continue para o **limiar** caixa e defina seu valor como 0. Pode, opcionalmente, otimizar a **período** e a frequência **campos**.
Clique em **Concluído**.

![Configurar a captura de ecrã da consulta de lógica de sinal](./media/proactive-cloud-services/003.png)

Sob o **definir detalhes do alerta** secção, forneça um **nome** e **Descrição** para a regra de alerta e defina seu **gravidade**.
Além disso, certifique-se de que o **ativar regra após a criação** botão é definido como **Sim**.

![Captura de ecrã detalhes do alerta](./media/proactive-cloud-services/004.png)

Sob o **grupo de ação de definir** secção, pode selecionar um existente **grupo de ação** ou criar um novo.
Pode optar por ter várias ações de vários tipos de conter o grupo de ação.

![Grupo de ação de captura de ecrã](./media/proactive-cloud-services/005.png)

Depois de definir o grupo de ação, confirme as suas alterações e clique em **criar regra de alerta**.

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre como detetar automaticamente:

[Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
[vazamentos de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
[anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)

