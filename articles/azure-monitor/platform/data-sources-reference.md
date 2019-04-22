---
title: Origens de dados no Azure de monitorização
description: Saiba mais sobre todas as monitorização origens de dados disponíveis no Azure hoje mesmo.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848532"
---
# <a name="consume-monitoring-data-from-azure"></a>Consumir dados de monitorização do Azure

Nas plataformas do Azure, estamos a juntar dados de monitorização num único local com o Azure Monitor do pipeline, mas praticamente reconhece que hoje nem todos os dados de monitorização está disponível nesse pipeline ainda. Neste artigo, resumimos as várias formas de como é possível acessar programaticamente dados de monitorização dos serviços do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>Opções para consumo de dados

| Tipo de dados | Categoria | Serviços suportados | Métodos de acesso |
| --- | --- | --- | --- |
| Métricas de nível de plataforma do Azure Monitor | Métricas | [Ver lista aqui](metrics-supported.md) | <ul><li>**REST API:** [API de métricas de Monitor do Azure](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Hub de eventos ou blob de armazenamento:** [Definições de diagnóstico](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Computação (por exemplo, o métricas de SO convidado contadores de desempenho) | Métricas | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) e máquinas virtuais do Linux (v2), [serviços Cloud](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [do Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela de armazenamento ou um blob:** [Diagnóstico do Windows ou Linux do Azure](diagnostics-extension-to-storage.md)</li><li>**Hub de eventos:** [Diagnóstico do Windows Azure](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Métricas personalizadas ou aplicação | Métricas | Qualquer aplicativo instrumentado com o Application Insights | <ul><li>**REST API:** [REST API do Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Métricas de armazenamento | Métricas | Storage do Azure | <ul><li>**Tabela de armazenamento:** [Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dados de faturação | Métricas | Todos os serviços do Azure | <ul><li>**REST API:** [Utilização de recursos do Azure e RateCard APIs](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Registo de Atividades | Eventos | Todos os serviços do Azure | <ul><li>**REST API:** [API de eventos de Monitor do Azure](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Hub de eventos ou blob de armazenamento:** [Perfil de registo](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Registos de diagnóstico do Azure Monitor | Eventos | [Ver lista aqui](diagnostic-logs-schema.md) | <ul><li>**Hub de eventos ou blob de armazenamento:** [Definições de diagnóstico](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Computação de registos do SO convidado (ex. IIS, o ETW, syslogs) | Eventos | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) e máquinas virtuais do Linux (v2), [serviços Cloud](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [do Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela de armazenamento ou um blob:** [Diagnóstico do Windows ou Linux do Azure](diagnostics-extension-to-storage.md)</li><li>**Hub de eventos:** [Diagnóstico do Windows Azure](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Registos do serviço de aplicações | Eventos | Serviços aplicacionais | <ul><li>**Ficheiro, tabela ou armazenamento de BLOBs:** [Diagnóstico de aplicação Web](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Registos de armazenamento | Eventos | Storage do Azure | <ul><li>**Tabela de armazenamento:** [Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertas do Centro de segurança | Eventos | Centro de Segurança do Azure | <ul><li>**REST API:** [Alertas de segurança](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Relatórios do Active Directory | Eventos | Azure Active Directory | <ul><li>**REST API:** [Graph API do Azure Active Directory](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Estado de recurso do Centro de segurança | Estado | [Recursos de todos com suporte](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API:** [Estados de segurança](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Estado de Funcionamento de Recursos | Estado | Serviços suportados | <ul><li>**REST API:** [REST API do Estado de funcionamento do recurso](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alertas de métricas de Monitor do Azure | Notificações | [Ver lista aqui](metrics-supported.md) | <ul><li>**Webhook:** [Alertas de métricas do Azure](alerts-webhooks.md)</li></ul> |
| Alertas de registo de atividade do Monitor do Azure | Notificações | Todos os serviços do Azure | <ul><li>**Webhook:** Alertas de registo de atividades do Azure</li></ul> |
| Notificações de dimensionamento automático | Notificações | [Ver lista aqui](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Esquema de payload de webhook de notificação de dimensionamento automático](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Alertas de consulta de pesquisa de registo | Notificações | Registos do Azure Monitor | <ul><li>**Webhook:** [Ação do Webhook para regras de alerta de registo](alerts-log-webhook.md)</li></ul> |
| Alertas de métricas do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [Alertas das informações de aplicação](../../azure-monitor/app/alerts.md)</li></ul> |
| Testes web do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [Alertas das informações de aplicação](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [métricas do Azure Monitor](data-platform.md)
- Saiba mais sobre [o registo de atividades do Azure](activity-logs-overview.md)
- Saiba mais sobre [registos de diagnóstico do Azure](diagnostic-logs-overview.md)

