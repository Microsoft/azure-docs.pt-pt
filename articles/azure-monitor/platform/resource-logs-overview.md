---
title: Visão geral dos logs de recursos do Azure | Microsoft Docs
description: Entenda os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989609"
---
# <a name="azure-resource-logs-overview"></a>Visão geral dos logs de recursos do Azure
Os logs de recursos do Azure são [os logs de plataforma](platform-logs-overview.md) emitidos pelos recursos do Azure que descrevem sua operação interna. Todos os logs de recursos compartilham um esquema comum de nível superior com a flexibilidade de cada serviço para emitir propriedades exclusivas para seus próprios eventos.

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico.

## <a name="collecting-resource-logs"></a>Coletando logs de recursos
Os logs de recursos são gerados automaticamente por recursos do Azure com suporte, mas não são coletados a menos que você os Configure usando uma [configuração de diagnóstico](diagnostic-settings.md). Crie uma configuração de diagnóstico para cada recurso do Azure para encaminhar os logs para os seguintes destinos:

| Destino | Cenário |
|:---|:---|:---|
| [Área de trabalho do Log Analytics](resource-logs-collect-workspace.md) | Analise os logs com outros dados de monitoramento e aproveite Azure Monitor recursos como consultas de log e alertas de log. |
| [Armazenamento do Azure](resource-logs-collect-storage.md) | Arquive os logs para auditoria ou backup. |
| [Hub de eventos](resource-logs-stream-event-hubs.md) | Transmita os logs para sistemas de registro em log e telemetria de terceiros.  |

## <a name="compute-resources"></a>Recursos de computação
Os logs de recursos diferem dos logs no nível do sistema operacional convidado nos recursos de computação do Azure. Os recursos de computação exigem um agente para coletar logs e métricas de seu sistema operacional convidado, incluindo dados como logs de eventos, syslog e contadores de desempenho. Use a [extensão de diagnóstico](agents-overview.md#azure-diagnostic-extension) para rotear dados de log de máquinas virtuais do Azure e o [agente de log Analytics](agents-overview.md#log-analytics-agent) para coletar logs e métricas de máquinas virtuais no Azure, em outras nuvens e localmente em um espaço de trabalho log Analytics. Consulte [fontes de dados de monitoramento para obter Azure monitor](data-sources.md) para obter detalhes.

## <a name="resource-logs-schema"></a>Esquema de logs de recursos
Para obter mais informações sobre o esquema e as categorias dos logs de recursos, consulte [esquema do log de recursos](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Próximos Passos

* [Saiba mais sobre outros logs da plataforma Azure](platform-logs-overview.md) que você pode usar para monitorar o Azure.
