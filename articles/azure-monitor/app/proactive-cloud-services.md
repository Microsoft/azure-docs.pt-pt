---
title: Alerta sobre questões nos Serviços Azure Cloud utilizando a integração de Diagnósticos Azure com insights de aplicação Azure  Microsoft Docs
description: Monitor para problemas como falhas de startups, falhas e ciclos de reciclagem de papéis em Azure Cloud Services com Insights de Aplicação Azure
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 997c5e063c4181a597520e60e2a7669401b9677d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669748"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alerta sobre questões nos Serviços Azure Cloud utilizando a integração de diagnósticos Azure com insights de aplicação Azure

Neste artigo, descreveremos como criar regras de alerta que monitorizam questões como falhas de startups, falhas e ciclos de reciclagem de papéis nos Serviços azure cloud (funções web e trabalhadores).

O método descrito neste artigo baseia-se na integração de [Diagnósticos Azure com Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)de Aplicação , e nos recentes alertas de log para a capacidade de Insights de [Aplicação.](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)

## <a name="define-a-base-query"></a>Defina uma consulta base

Para começar, vamos definir uma consulta base que recupera os eventos do Windows Event Log do canal Windows Azure, que são capturados em Application Insights como registos de vestígios.
Estes registos podem ser usados para detetar uma variedade de problemas nos Serviços Azure Cloud, como falhas de arranque, falhas no tempo de execução e ciclos de reciclagem.

> [!NOTE]
> A consulta de base abaixo verifica os problemas numa janela temporal de 30 minutos, e assume uma latência de 10 minutos na ingestão dos registos de telemetria. Estes predefinições podem ser configurados como entender.

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

## <a name="check-for-specific-event-ids"></a>Verifique se existem iDs de eventos específicos

Após a recuperação dos eventos do Windows Event Log, problemas específicos podem ser detetados verificando as suas propriedades de ID e mensagem de eventos respetivos (ver exemplos abaixo).
Basta combinar a consulta de base acima com uma das consultas abaixo, e usou essa consulta combinada ao definir a regra de alerta de registo.

> [!NOTE]
> Nos exemplos abaixo, será detetado um problema se forem encontrados mais de três eventos durante a janela de tempo analisada. Esta predefinição pode ser configurada para alterar a sensibilidade da regra de alerta.

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

No menu de navegação dentro do seu recurso Application Insights, vá a **Alertas**e, em seguida, selecione **Nova Regra**de Alerta .

![Screenshot da regra Criar](./media/proactive-cloud-services/001.png)

Na janela de **regra Criar,** sob a secção de condição de **alerta Definir,** clique nos **critérios adicionar**e, em seguida, selecione procurar registo **personalizado**.

![Screenshot dos critérios de condição de definição para alerta](./media/proactive-cloud-services/002.png)

Na caixa de **consulta de pesquisa,** colhe a consulta combinada que preparou no passo anterior.

Em seguida, continue para a caixa **Threshold,** e definir o seu valor para 0. Pode ajustar opcionalmente **os** **campos**periode e frequência .
Clique em **Concluído**.

![Screenshot da consulta lógica de sinal configurar](./media/proactive-cloud-services/003.png)

Na secção de detalhes de **alerta Definido,** forneça um **Nome** e **Descrição** à regra de alerta e defina a sua **gravidade**.
Além disso, **certifique-se** de que a regra Enable sobre o botão de criação está definida para **Sim**.

![Detalhes de alerta de screenshot](./media/proactive-cloud-services/004.png)

Na secção de grupo de **ação Define,** pode selecionar um grupo de **Ação** existente ou criar um novo.
Pode optar por que o grupo de ação contenha múltiplas ações de vários tipos.

![Grupo de ação de screenshot](./media/proactive-cloud-services/005.png)

Depois de definir o grupo Action, confirme as suas alterações e clique em **Criar a regra**de alerta .

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a deteção automática:

[Anomalias](../../azure-monitor/app/proactive-failure-diagnostics.md) de falha
fugas de [memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
[anomalias](../../azure-monitor/app/proactive-performance-diagnostics.md) de desempenho

