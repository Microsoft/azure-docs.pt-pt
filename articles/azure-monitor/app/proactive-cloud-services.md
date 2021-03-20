---
title: Alerta sobre problemas nos Serviços Azure Cloud utilizando a integração do Azure Diagnostics com a Azure Application Insights | Microsoft Docs
description: Monitorize para problemas como falhas de startups, falhas e ciclos de reciclagem de papéis em Azure Cloud Services com Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87309295"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alerta sobre problemas nos Serviços Azure Cloud usando a integração de diagnósticos Azure com Azure Application Insights

Neste artigo, descreveremos como configurar regras de alerta que monitorizam questões como falhas de arranque, falhas e ciclos de reciclagem de papéis em Azure Cloud Services (funções web e trabalhadores).

O método descrito neste artigo baseia-se na integração do [Azure Diagnostics com o Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/), e nos alertas de registo recentemente lançados para a capacidade de Insights de [Aplicações.](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)

## <a name="define-a-base-query"></a>Definir uma consulta de base

Para começar, vamos definir uma consulta base que recupera os eventos de Registo de Eventos do Windows a partir do canal Windows Azure, que são capturados no Application Insights como registos de rastreios.
Estes registos podem ser usados para detetar uma variedade de problemas nos Serviços Azure Cloud, como falhas de arranque, falhas no tempo de funcionamento e ciclos de reciclagem.

> [!NOTE]
> A consulta de base abaixo verifica os problemas num intervalo de tempo de 30 minutos, e assume uma latência de 10 minutos na ingestão dos registos de telemetria. Estes predefinidos podem ser configurados como entender.

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

## <a name="check-for-specific-event-ids"></a>Verifique se existem IDs específicos do evento

Depois de recuperar os eventos do Windows Event Log, podem ser detetados problemas específicos verificando as respetivas propriedades de ID e de mensagem (ver exemplos abaixo).
Basta combinar a consulta de base acima com uma das consultas abaixo, e usou essa consulta combinada ao definir a regra de alerta de registo.

> [!NOTE]
> Nos exemplos abaixo, será detetado um problema se forem encontrados mais de três eventos durante a janela de tempo analisada. Este padrão pode ser configurado para alterar a sensibilidade da regra de alerta.

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

No menu de navegação dentro do seu recurso Application Insights, vá a **Alertas** e, em seguida, selecione **Nova Regra de Alerta**.

![Screenshot da regra criar](./media/proactive-cloud-services/001.png)

Na janela **regra 'Criar',** na secção **Defina o estado de alerta,** clique em **Adicionar critérios** e, em seguida, selecione **pesquisa de registo personalizado**.

![Screenshot de definir critérios de condição para alerta](./media/proactive-cloud-services/002.png)

Na caixa **de consulta de pesquisa,** cole a consulta combinada que preparou no passo anterior.

Em seguida, continue para a caixa **Threshold,** e definir o seu valor para 0. Pode alterar opcionalmente os **campos** **de Period** e Frequência .
Clique em **Concluído**.

![Screenshot da consulta lógica de sinal de configuração](./media/proactive-cloud-services/003.png)

Na secção **Desemapar os detalhes** do alerta, forneça um **Nome** e **Descrição** à regra de alerta e defina a sua **Severidade**.
Além disso, certifique-se de que a regra Enable após o botão **de criação** está definida como **Sim**.

![Detalhes do alerta de screenshot](./media/proactive-cloud-services/004.png)

Na secção De definir grupo **de ação,** pode selecionar um **grupo de Ação** existente ou criar um novo.
Pode optar por que o grupo de ação contenha múltiplas ações de vários tipos.

![Grupo de ação screenshot](./media/proactive-cloud-services/005.png)

Uma vez definido o grupo Ação, confirme as alterações e clique em **Criar a regra de alerta**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a deteção automática:

Anomalias de [falha](./proactive-failure-diagnostics.md) 
 [Fugas de](./proactive-potential-memory-leak.md) 
 memória [Anomalias de desempenho](./proactive-performance-diagnostics.md)

