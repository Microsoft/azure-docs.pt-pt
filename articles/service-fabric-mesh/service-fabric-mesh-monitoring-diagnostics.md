---
title: Monitoramento e diagnóstico em aplicativos de malha de Service Fabric do Azure
description: Saiba mais sobre como monitorar e diagnosticar aplicativos na malha de Service Fabric no Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498156"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Monitoramento e diagnóstico para Service Fabric malha é categorizado em três tipos principais de dados de diagnóstico:

- Logs de aplicativo-esses são definidos como os logs de seus aplicativos em contêineres, com base em como você instrumentou seu aplicativo (por exemplo, logs do Docker)
- Eventos de plataforma-eventos da plataforma de malha relevantes para sua operação de contêiner, atualmente incluindo ativação de contêiner, desativação e encerramento.
- Métricas de contêiner-utilização de recursos e métricas de desempenho para seus contêineres (estatísticas do Docker)

Este artigo discute as opções de monitoramento e diagnóstico para a versão de visualização mais recente disponível.

## <a name="application-logs"></a>Logs de aplicativo

Você pode exibir os logs do Docker de seus contêineres implantados, em uma base por contêiner. No modelo de aplicativo de malha Service Fabric, cada contêiner é um pacote de código em seu aplicativo. Para ver os logs associados a um pacote de códigos, use o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Você pode usar o comando "AZ mesh Service-Replica" para obter o nome da réplica. Os nomes de réplica estão incrementando inteiros de 0.

Esta é a aparência para ver os logs do contêiner VotingWeb. Code do aplicativo de votação:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contêiner 

O ambiente de malha expõe algumas métricas que indicam como os contêineres estão sendo executados. As métricas a seguir estão disponíveis por meio do portal do Azure e da CLI do Azure monitor:

| Métrica | Descrição | Unidades|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu como uma porcentagem | % |
| MemoryUtilization | ActualMem/AllocatedMem como uma porcentagem | % |
| AllocatedCpu | CPU alocada de acordo com o modelo de Azure Resource Manager | Milicores |
| AllocatedMemory | Memória alocada de acordo com o modelo de Azure Resource Manager | MB |
| ActualCpu | Utilização da CPU | Milicores |
| ActualMemory | Uso de memória | MB |
| ContainerStatus | 0-inválido: o status do contêiner é desconhecido <br> 1-pendente: o contêiner foi agendado para iniciar <br> 2-iniciando: o contêiner está no processo de início <br> 3-iniciado: o contêiner foi iniciado com êxito <br> 4-parando: o contêiner está sendo interrompido <br> 5-parado: o contêiner foi interrompido com êxito | N/A |
| ApplicationStatus | 0-desconhecido: o status não é recuperável <br> 1-pronto: o aplicativo está sendo executado com êxito <br> 2-Atualizando: há uma atualização em andamento <br> 3-Criando: o aplicativo está sendo criado <br> 4-excluindo: o aplicativo está sendo excluído <br> 5-falha: falha ao implantar o aplicativo | N/A |
| ServiceStatus | 0-inválido: o serviço atualmente não tem um status de integridade <br> 1-OK: o serviço está íntegro  <br> 2-AVISO: pode haver algo errado ao exigir investigação <br> 3-erro: há algo errado que precisa de investigação <br> 4-desconhecido: o status não é recuperável | N/A |
| ServiceReplicaStatus | 0-inválido: a réplica não tem um status de integridade no momento <br> 1-OK: o serviço está íntegro  <br> 2-AVISO: pode haver algo errado ao exigir investigação <br> 3-erro: há algo errado que precisa de investigação <br> 4-desconhecido: o status não é recuperável | N/A | 
| RestartCount | Número de reinicializações de contêiner | N/A |

> [!NOTE]
> Os valores de perstatus e ServiceReplicaStatus são os mesmos que o [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) em Service Fabric. 

Cada métrica está disponível em dimensões diferentes para que você possa ver agregações em diferentes níveis. A lista atual de dimensões é a seguinte:

* ApplicationName
* serviceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> A dimensão CodePackageName não está disponível para aplicativos Linux. 

Cada dimensão corresponde a diferentes componentes do [modelo de aplicativo Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>CLI do Azure Monitor

Uma lista completa de comandos está disponível nos [documentos da CLI do Azure monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , mas incluímos alguns exemplos úteis abaixo 

Em cada exemplo, a ID de recurso segue esse padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização da CPU dos contêineres em um aplicativo

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização de memória para cada réplica de serviço
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinicializações para cada contêiner em uma janela de 1 hora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média da CPU entre serviços denominados "VotingWeb" em uma janela de 1 hora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Gerenciador de métricas

O Metrics Explorer é uma folha no portal em que você pode visualizar todas as métricas para seu aplicativo de malha. Essa folha pode ser acessada na página do aplicativo no portal e na folha do Azure monitor, a última que você pode usar para exibir as métricas para todos os recursos do Azure que dão suporte a Azure Monitor. 

![Explorador de Métricas](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas Azure Monitor, confira os [documentos Azure monitor CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
