---
title: Visão geral dos logs da plataforma Azure | Microsoft Docs
description: Visão geral dos logs de diagnóstico no Azure que fornecem dados avançados e frequentes sobre a operação de um recurso do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262573"
---
# <a name="overview-of-azure-platform-logs"></a>Visão geral dos logs da plataforma Azure
Os logs de plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e a plataforma do Azure da qual dependem. Eles são gerados automaticamente, embora você precise configurar determinados logs da plataforma para serem encaminhados a um ou mais destinos a serem retidos. Este artigo fornece uma visão geral dos logs de plataforma, incluindo as informações que eles fornecem e como você pode configurá-los para coleta e análise.

## <a name="types-of-platform-logs"></a>Tipos de logs de plataforma
A tabela a seguir lista os logs de plataforma específicos que estão disponíveis em diferentes camadas do Azure.

| Camada | Registos | Descrição |
|:---|:---|:---|
| Recursos do Azure | [Logs de recursos](resource-logs-overview.md) | Forneça informações sobre as operações que foram executadas em um recurso do Azure (o *plano de dados*), por exemplo, obter um segredo de um Key Vault ou fazer uma solicitação para um banco de dado. O conteúdo dos logs de recursos varia de acordo com o serviço do Azure e o tipo de recurso.<br>*Os logs de recursos foram anteriormente referidos como logs de diagnóstico.*  |
| Subscrição do Azure | [Registo de atividades](activity-logs-overview.md) | Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (*o plano de gerenciamento*), além de atualizações em eventos de integridade do serviço. Há um único log de atividades para cada assinatura do Azure.   |
| Locatário do Azure | [Logs de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | Contém o histórico de atividade de entrada e a trilha de auditoria das alterações feitas no Azure Active Directory para um locatário específico.   |


![Visão geral dos logs da plataforma](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Exibindo logs da plataforma
Você pode exibir o [log de atividades](activity-log-view.md) e os [logs de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) no portal do Azure. Você deve enviar logs de recursos para um [destino](#destinations) para exibi-los.


## <a name="destinations"></a>Destinos
Você pode enviar os logs da plataforma para um ou mais dos destinos na tabela a seguir, dependendo dos seus requisitos de monitoramento. 

| Destination | Cenário | Referências |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Analise os logs com outros dados de monitoramento e aproveite Azure Monitor recursos como consultas de log e alertas. | [Logs de recursos](resource-logs-collect-storage.md)<br>[Registo de atividades](activity-log-collect.md)<br>[Logs do diretório de atividades do Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Arquive os logs para auditoria, análise estática ou backup. |[Logs de recursos](archive-diagnostic-logs.md)<br>[Registo de atividades](activity-log-export.md)<br>[Logs do diretório de atividades do Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de eventos | Transmita os logs para sistemas de registro em log e telemetria de terceiros.  |[Logs de recursos](resource-logs-stream-event-hubs.md)<br>[Registo de atividades](activity-log-export.md)<br>[Logs do diretório de atividades do Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Configurações de diagnóstico e perfis de log
Configure destinos para logs de recursos e logs de Azure Active Directory [criando uma configuração de diagnóstico](diagnostic-settings.md). Configure os destinos para o log de atividades [criando um perfil de log](activity-log-export.md) ou [conectando-o a um espaço de trabalho log Analytics](activity-log-collect.md).

A configuração de diagnóstico e o perfil de log definem o seguinte:

- Um ou mais destinos para enviar as métricas e os logs selecionados.
- Quais categorias e métricas de log do recurso são enviadas para os destinos.
- Se uma conta de armazenamento for selecionada como um destino, por quanto tempo cada categoria de log deve ser mantida.



## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o log de atividades](activity-logs-overview.md)
* [Saiba mais sobre os logs de recursos](resource-logs-overview.md)
* [Exibir o esquema de log de recursos para diferentes serviços do Azure](diagnostic-logs-schema.md)
