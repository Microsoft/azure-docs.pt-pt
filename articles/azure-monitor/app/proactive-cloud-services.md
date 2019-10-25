---
title: Alerta sobre problemas nos serviços de nuvem do Azure usando a integração de Diagnóstico do Azure com o Aplicativo Azure insights | Microsoft Docs
description: Monitorar problemas como falhas de inicialização, panes e loops de reciclagem de função nos serviços de nuvem do Azure com informações de Aplicativo Azure
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: b4404f033f5bdf221590e155640e4c0442601e18
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820630"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Alerta sobre problemas nos serviços de nuvem do Azure usando a integração do diagnóstico do Azure com o Aplicativo Azure insights

Neste artigo, descreveremos como configurar as regras de alerta que monitoram problemas como falhas de inicialização, panes e loops de reciclagem de função nos serviços de nuvem do Azure (funções Web e de trabalho).

O método descrito neste artigo baseia-se na [integração de diagnóstico do Azure com o Application insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)e nos alertas de log lançados recentemente [para Application insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) recurso.

## <a name="define-a-base-query"></a>Definir uma consulta base

Para começar, definiremos uma consulta base que recupera os eventos do log de eventos do Windows do canal do Windows Azure, que são capturados em Application Insights como registros de rastreamento.
Esses registros podem ser usados para detectar uma variedade de problemas nos serviços de nuvem do Azure, como falhas de inicialização, falhas de tempo de execução e loops de reciclagem.

> [!NOTE]
> A consulta base abaixo verifica problemas em uma janela de tempo de 30 minutos e pressupõe uma latência de 10 minutos para ingerir os registros de telemetria. Esses padrões podem ser configurados como você pode ver adequado.

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

## <a name="check-for-specific-event-ids"></a>Verificar identificações de evento específicas

Depois de recuperar os eventos do log de eventos do Windows, problemas específicos podem ser detectados verificando suas respectivas IDs de evento e propriedades de mensagem (veja os exemplos abaixo).
Basta combinar a consulta base acima com uma das consultas abaixo e usar essa consulta combinada ao definir a regra de alerta de log.

> [!NOTE]
> Nos exemplos abaixo, um problema será detectado se mais de três eventos forem encontrados durante a janela de tempo analisada. Esse padrão pode ser configurado para alterar a sensibilidade da regra de alerta.

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

No menu de navegação do recurso Application Insights, vá para **alertas**e selecione **nova regra de alerta**.

![Captura de tela de criar regra](./media/proactive-cloud-services/001.png)

Na janela **criar regra** , na seção **definir condição de alerta** , clique em **Adicionar critérios**e selecione pesquisa de **logs personalizada**.

![Captura de tela de definir critérios de condição para o alerta](./media/proactive-cloud-services/002.png)

Na caixa **Pesquisar consulta** , Cole a consulta combinada que você preparou na etapa anterior.

Em seguida, continue até a caixa de **limite** e defina seu valor como 0. Você pode, opcionalmente, ajustar os **campos**de **período** e frequência.
Clique em **Concluído**.

![Captura de tela de configurar consulta lógica de sinal](./media/proactive-cloud-services/003.png)

Na seção **definir detalhes do alerta** , forneça um **nome** e uma **Descrição** para a regra de alerta e defina sua **gravidade**.
Além disso, verifique se o botão **habilitar regra no momento da criação** está definido como **Sim**.

![Detalhes do alerta de captura de tela](./media/proactive-cloud-services/004.png)

Na seção **definir grupo de ações** , você pode selecionar um **grupo de ações** existente ou criar um novo.
Você pode optar por fazer com que o grupo de ações contenha várias ações de vários tipos.

![Grupo de ações de captura de tela](./media/proactive-cloud-services/005.png)

Depois de definir o grupo de ações, confirme as alterações e clique em **criar regra de alerta**.

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre a detecção automática de:

[Anomalias de falha](../../azure-monitor/app/proactive-failure-diagnostics.md)
[vazamentos de memória](../../azure-monitor/app/proactive-potential-memory-leak.md)
[anomalias de desempenho](../../azure-monitor/app/proactive-performance-diagnostics.md)

