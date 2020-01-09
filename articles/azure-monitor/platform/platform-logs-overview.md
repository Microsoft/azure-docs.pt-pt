---
title: Visão geral dos logs da plataforma Azure | Microsoft Docs
description: Visão geral dos logs em Azure Monitor que fornecem dados avançados e frequentes sobre a operação de um recurso do Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f02368bfb0c084691376300980d4cdee0d9b3be
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530889"
---
# <a name="overview-of-azure-platform-logs"></a>Visão geral dos logs da plataforma Azure
Os logs de plataforma fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e a plataforma do Azure da qual dependem. Eles são gerados automaticamente, embora você precise configurar determinados logs da plataforma para serem encaminhados a um ou mais destinos a serem retidos. Este artigo fornece uma visão geral dos logs de plataforma, incluindo as informações que eles fornecem e como você pode configurá-los para coleta e análise.

## <a name="types-of-platform-logs"></a>Tipos de logs de plataforma
A tabela a seguir lista os logs de plataforma específicos que estão disponíveis em diferentes camadas do Azure.

| Registo | Camada | Descrição |
|:---|:---|:---|
| Registos do recurso | Recursos do Azure | Forneça informações sobre as operações que foram executadas em um recurso do Azure (o *plano de dados*), por exemplo, obter um segredo de um Key Vault ou fazer uma solicitação para um banco de dado. O conteúdo dos logs de recursos varia de acordo com o serviço do Azure e o tipo de recurso.<br><br>*Os logs de recursos foram anteriormente referidos como logs de diagnóstico.*  |
| Registo de atividades | Subscrição do Azure | Fornece informações sobre as operações em cada recurso do Azure na assinatura de fora (*o plano de gerenciamento*), além de atualizações em eventos de integridade do serviço. Use o log de atividades para determinar o _que_, _quem_e _quando_ para qualquer operação de gravação (put, post, Delete) realizada nos recursos em sua assinatura. Também é possível compreender o estado da operação e outras propriedades relevantes.  Há um único log de atividades para cada assinatura do Azure. |
| Logs de Azure Active Directory | Inquilino do Azure |  Contém o histórico de atividade de entrada e a trilha de auditoria das alterações feitas no Azure Active Directory para um locatário específico. Consulte [o que são Azure Active Directory relatórios?](../../active-directory/reports-monitoring/overview-reports.md) para obter uma descrição completa dos Logs de Azure Active Directory.   |

> [!NOTE]
> O log de atividades do Azure destina-se principalmente a atividades que ocorrem em Azure Resource Manager. Ele não controla os recursos usando o modelo clássico/RDFE. Alguns tipos de recursos clássicos têm um provedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft. ClassicCompute). Se você interagir com um tipo de recurso clássico por meio de Azure Resource Manager usando esses provedores de recursos de proxy, as operações aparecerão no log de atividades. Se você interagir com um tipo de recurso clássico fora dos proxies de Azure Resource Manager, suas ações só serão registradas no log de operação. O log de operação pode ser pesquisado em uma seção separada do Portal.

![Descrição geral dos registos da plataforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Exibindo logs da plataforma
Há diferentes opções para exibir e analisar os diferentes logs da plataforma Azure.

- Exiba o log de atividades no portal do Azure e acesse eventos do PowerShell e da CLI. Consulte [Exibir e recuperar eventos do log de atividades do Azure](activity-log-view.md) para obter detalhes. 
- Exiba Azure Active Directory relatórios de segurança e atividade no portal do Azure. Confira [o que são Azure Active Directory relatórios?](../../active-directory/reports-monitoring/overview-reports.md)  para obter detalhes.
- Os logs de recursos são gerados automaticamente por recursos do Azure com suporte, mas não estão disponíveis para serem exibidos, a menos que você os envie para um [destino](#destinations). 

## <a name="destinations"></a>Destinos
Você pode enviar os logs da plataforma para um ou mais dos destinos na tabela a seguir, dependendo dos seus requisitos de monitoramento. Configure destinos para logs da plataforma [criando uma configuração de diagnóstico](diagnostic-settings.md).

| Destino | Cenário | Referências |
|:---|:---|:---|:---|
| Área de trabalho do Log Analytics | Analise os logs com outros dados de monitoramento e aproveite Azure Monitor recursos como consultas de log e alertas. | [Log de atividades e logs de recursos](resource-logs-collect-workspace.md)<br>[Logs do diretório de atividades do Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Storage do Azure | Arquive os logs para auditoria, análise estática ou backup. |[Log de atividades e logs de recursos](archive-diagnostic-logs.md)<br>[Logs do diretório de atividades do Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de eventos | Transmita os logs para sistemas de registro em log e telemetria de terceiros.  |[Log de atividades e logs de recursos](resource-logs-stream-event-hubs.md)<br>[Logs do diretório de atividades do Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Passos seguintes

* [Exibir o esquema do log de atividades para diferentes categorias](activity-log-schema.md)
* [Exibir o esquema de log de recursos para diferentes serviços do Azure](diagnostic-logs-schema.md)
