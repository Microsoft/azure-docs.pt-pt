---
title: Compreender como voluntária ferramenta de migração de alertas no Azure Monitor funciona
description: Compreender como funciona a ferramenta de migração de alerta e solucionar problemas caso se depare com problemas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347605"
---
# <a name="understand-how-the-migration-tool-works"></a>Compreender como funciona a ferramenta de migração

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Azure Monitor estão a ser descontinuadas em Julho de 2019. A ferramenta de migração para acionar a migração voluntariamente está disponível no portal do Azure e está a implementar para os clientes que utilizam regras de alerta clássicas.

Este artigo explica como funciona a ferramenta de migração voluntária. Ele também descreve a remediação para alguns problemas comuns.

## <a name="which-classic-alert-rules-can-be-migrated"></a>As regras de alerta clássicas podem ser migradas?

Embora quase todas as regras de alerta clássicas podem ser migradas usando a ferramenta, existem algumas exceções. As seguintes regras de alerta não serão migradas usando a ferramenta (ou durante a migração automática em Julho de 2019)

- Regras de alerta clássicas em métricas de convidado de máquina virtual (Windows e Linux). [Consulte a documentação de orientação sobre como recriar essas regras de alerta no novos alertas de métricas](#guest-metrics-on-virtual-machines)
- Regras de alerta clássicas em métricas de armazenamento clássicas. [Consulte a documentação de orientação sobre como monitorizar as suas contas de armazenamento clássicas](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- Regras de alerta clássicas em algumas métricas de conta de armazenamento. [Detalhes abaixo](#storage-account-metrics)

Se a sua subscrição tem tais regras clássicas, rest das regras será migrada, mas estas regras, terá de ser migrados manualmente. À medida que não é possível fornecer uma migração automática, qualquer tais existentes clássicas alertas de métricas irão continuar trabalhando em Junho de 2020 para fornecer o tempo para mudar para novos alertas. No entanto, não existem novos alertas clássicos podem ser criadas post de 2019 de Junho.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de convidado em máquinas virtuais

Para poder criar novos alertas de métricas em métricas de convidado, as métricas de convidado tem de ser enviados para o armazenamento do Azure Monitor métricas personalizadas. Siga as instruções abaixo para ativar o sink do Azure Monitor nas definições de diagnóstico.

- [Ativar as métricas de convidado para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Ativar as métricas de convidado para VMs do Linux](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

Depois de terminar os passos acima, os novos alertas de métricas podem ser criadas em métricas de convidado. Depois de ter recriado novos alertas de métricas, alertas clássicos podem ser eliminadas.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto os alertas nas métricas seguintes:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Regras de alerta clássicas em métricas de percentagem tem de ser migrados com base na [o mapeamento entre as métricas de armazenamento antigos e novos](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Limiares precisará ser modificado adequadamente como a nova métrica disponível é absoluto.

Regras de alerta clássicas AnonymousThrottlingError e SASThrottlingError terão de ser dividida em dois novos alertas porque existem é não existem métricas combinada que fornece a mesma funcionalidade. Limiares tem de ser adaptada adequadamente.

## <a name="roll-out-phases"></a>Fases de implementação

A ferramenta de migração está a implementar em fases para os clientes que utilizam regras de alerta clássicas. **Proprietários de subscrições** irão receber um e-mail quando a subscrição está pronta para ser migrada utilizando a ferramenta.

> [!NOTE]
> Como a ferramenta está a ser implementada em fases, nas fases iniciais, poderá ver que a maioria das suas subscrições ainda não estiver pronta para ser migrada.

Atualmente uma **subconjunto** de subscrições, que **apenas** ter regras de alerta clássicas no seguinte recurso tipos são marcados como prontos para migração. Suporte a mais tipos de recursos será adicionado em fases futuras.

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>Quem pode acionar a migração?

Qualquer utilizador que tenha a função incorporada de **Contribuidor de monitorização** na subscrição do nível poderão acionar a migração. Os utilizadores com uma função personalizada com as seguintes permissões também podem acionar a migração:

- * / leitura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>Problemas comuns e remediações

Uma vez que [acionar a migração](alerts-using-migration-tool.md), utilizaremos os endereços de e-mail fornecido para notificá-lo de conclusão da migração ou se existe uma ação necessária da sua. A seguinte secção descreve alguns problemas comuns e como resolvê-los

### <a name="validation-failed"></a>Falha na validação

Devido a algumas alterações recentes para regras de alerta clássicas na sua subscrição, não é possível migrar a subscrição. Este é um problema temporário. É possível reiniciar a migração assim que o estado de migração Retroceder **pronto para a migração** em alguns dias.

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>Impedir-nos de migrar as suas regras de bloqueio de política/escopo

Como parte da migração, serão criados novos alertas de métricas e novos grupos de ação e regras de alerta clássicas serão eliminadas (assim que forem criadas novas regras). No entanto, existe uma política ou o âmbito de bloqueio impedir-nos de criação de recursos. Consoante o bloqueio de política ou âmbito, não foi possível migrar algumas ou todas as regras. Pode resolver este problema ao remover a bloqueio/política de âmbito temporariamente e acionar novamente a migração.

## <a name="next-steps"></a>Passos Seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para migração](alerts-prepare-migration.md)
