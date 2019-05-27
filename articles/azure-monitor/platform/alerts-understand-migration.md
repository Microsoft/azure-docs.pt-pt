---
title: Compreender como a ferramenta de migração voluntária funciona para os alertas do Azure Monitor
description: Compreender como funciona a ferramenta de migração de alertas e resolução de problemas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015595"
---
# <a name="understand-how-the-migration-tool-works"></a>Compreender como funciona a ferramenta de migração

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Azure Monitor estão a ser descontinuadas em Setembro de 2019 (foi originalmente de 2019 de Julho). Uma ferramenta de migração está disponível no portal do Azure para os clientes que utilizam regras de alerta clássicas e que desejam acionar a migração propriamente ditas.

Este artigo explica como funciona a ferramenta de migração voluntária. Ele também descreve as soluções para alguns problemas comuns.

> [!NOTE]
> Devido a atraso no faseada da ferramenta de migração, tem sido a data de retirada para a migração de alertas clássicos [estendido para 31 de Agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a contar da data originalmente anunciado de 30 de Junho de 2019.

## <a name="which-classic-alert-rules-can-be-migrated"></a>As regras de alerta clássicas podem ser migradas?

Embora a ferramenta pode migrar quase todos os clássicas regras de alerta, existem algumas exceções. As seguintes regras de alerta não serão migradas utilizando a ferramenta (ou durante a migração automática em Setembro de 2019):

- Regras de alerta clássicas em métricas de convidado de máquina virtual (Windows e Linux). Consulte a [orientações para recriar essas regras de alerta no novos alertas de métricas](#guest-metrics-on-virtual-machines) mais adiante neste artigo.
- Regras de alerta clássicas em métricas de armazenamento clássicas. Consulte a [documentação de orientação para monitorizar as suas contas de armazenamento clássicas](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regras de alerta clássicas em algumas métricas de conta de armazenamento. Ver [detalhes](#storage-account-metrics) mais adiante neste artigo.

Se a sua subscrição tiver tais regras clássicas, tem de migrá-los manualmente. Uma vez que não é possível fornecer uma migração automática, qualquer alertas de métricas existentes, clássicas desses tipos irão continuar a funcionar até de 2020 de Junho. Esta extensão dá-lhe tempo para mover para novos alertas. No entanto, não existem novos alertas clássicos podem ser criadas depois de Agosto de 2019.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de convidado em máquinas virtuais

Antes de poder criar novos alertas de métricas em métricas de convidado, as métricas de convidado têm de ser enviadas para o arquivo de métricas personalizadas do Azure Monitor. Siga estas instruções para ativar o sink do Azure Monitor nas definições de diagnóstico:

- [Ativar as métricas de convidado para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Ativar as métricas de convidado para VMs do Linux](collect-custom-metrics-linux-telegraf.md)

Depois de concluído estes passos, pode criar novos alertas de métricas em métricas de convidado. E, depois de criar novos alertas de métricas, pode eliminar alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto os alertas nestas métricas:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

Alerta clássica de regras em percentagem métricas devem ser migradas com base na [o mapeamento entre as métricas de armazenamento antigos e novos](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Limiares precisará ser modificado adequadamente porque a nova métrica disponível é absoluto.

Regras de alerta clássicas em AnonymousThrottlingError e SASThrottlingError têm de ser divididas em dois novos alertas porque não existe nenhuma métrica combinada que fornece a mesma funcionalidade. Limiares tem de ser adaptada adequadamente.

## <a name="rollout-phases"></a>Fases de implementação

A ferramenta de migração está a implementar em fases para os clientes que utilizam regras de alerta clássicas. Proprietários de subscrições irão receber um e-mail quando a subscrição está pronta para ser migrada utilizando a ferramenta.

> [!NOTE]
> Uma vez que a ferramenta está a ser implementada em fases, poderá ver que a maioria das suas subscrições ainda não está pronta a serem migrados durante as fases iniciais.

Atualmente um subconjunto de subscrições é marcado como pronta para migração. O subconjunto inclui as subscrições com regras de alerta clássicas apenas nos seguintes tipos de recursos. Suporte a mais tipos de recursos será adicionado em fases futuras.

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

Qualquer utilizador que tenha a função incorporada de Contribuidor de monitorização ao nível da subscrição pode acionar a migração. Os utilizadores que têm uma função personalizada com as seguintes permissões também podem acionar a migração:

- * / leitura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>Problemas e soluções comuns

Depois de [acionar a migração](alerts-using-migration-tool.md), irá receber um e-mail nos endereços que indicou para notificá-lo que a migração estiver concluída, ou se é necessária qualquer ação do utilizador. Esta secção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Falha na validação

Devido a algumas alterações recentes para regras de alerta clássicas na sua subscrição, não é possível migrar a subscrição. Esse problema é temporário. Pode reiniciar a migração, depois do Estado de migração Retroceder **pronto para a migração** em alguns dias.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Impedir-nos de migrar as suas regras de bloqueio de política ou âmbito

Como parte da migração, serão criados novos alertas de métricas e novos grupos de ação e, em seguida, as regras de alerta clássicas serão eliminadas. No entanto, existe uma política ou o âmbito de bloqueio impedir-nos de criação de recursos. Consoante o bloqueio de política ou âmbito, não foi possível migrar algumas ou todas as regras. Pode resolver este problema ao remover o bloqueio de âmbito ou a política temporariamente e acionar novamente a migração.

## <a name="next-steps"></a>Passos Seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
