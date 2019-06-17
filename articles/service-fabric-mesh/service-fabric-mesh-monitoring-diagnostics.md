---
title: Monitorização e diagnóstico em aplicativos de malha do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre monitorização e diagnóstico de aplicação no Service Fabric malha no Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939829"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Monitorização e diagnóstico para a malha de recursos de infraestrutura do serviço é categorizado em três tipos principais de dados de diagnóstico:

- Registos de aplicações - estes são definidos como os registos das suas aplicações em contentores, com base na forma como tiver instrumentado a sua aplicação (por exemplo, registos do docker)
- Eventos de plataforma - eventos a partir da plataforma de malha relevante para a operação de contentor, incluindo atualmente ativação de contentor, a desativação e cessação.
- Métricas de contentor - métricas de desempenho e a utilização de recursos para os seus contentores (estatísticas de docker)

Este artigo discute as opções de monitorização e diagnóstico para a versão de pré-visualização mais recente disponível.

## <a name="application-logs"></a>Registos de aplicações

Pode ver os registos de docker dos contentores implementados, numa base por contentor. No modelo de aplicativo de malha do Service Fabric, cada contentor é um pacote de código em seu aplicativo. Para ver os registos associados com um pacote de código, utilize o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Pode utilizar o comando "az mesh réplica service" para obter o nome de réplica. Nomes de réplica são incrementando números inteiros de 0.

Eis o que isso é semelhante, para ver os registos do contêiner VotingWeb.Code da aplicação de voto:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contentor 

O ambiente de malha expõe um punhado de métricas que indica como os contentores estão a funcionar. As métricas seguintes estão disponíveis através do Azure portal e o Azure CLI de monitorizar:

| Métrica | Descrição | Unidades|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu como uma percentagem | % |
| MemoryUtilization | ActualMem/AllocatedMem como uma percentagem | % |
| AllocatedCpu | CPU alocados de acordo com o modelo Azure Resource Manager | Milinúcleos |
| AllocatedMemory | Memória alocada de acordo com o modelo Azure Resource Manager | MB |
| ActualCpu | Utilização da CPU | Milinúcleos |
| ActualMemory | Utilização da memória | MB |
| ContainerStatus | 0 - inválido: O estado do contentor é desconhecido <br> 1 - pendente: O contentor está agendado para começar <br> 2 - a partir de: O contentor está no processo de início <br> 3 - ao: O contentor foi iniciado com êxito <br> 4 - parar: O contentor está a ser parado <br> 5 - parado: O contentor foi parado com êxito | N/A |
| ApplicationStatus | 0 - desconhecida: O estado não é recuperável <br> 1 - ready: A aplicação é executada com êxito <br> 2 - atualizar: Existe uma atualização em curso <br> 3 - criar: A aplicação está a ser criada <br> 4 - a eliminar: A aplicação está a ser eliminada <br> 5 - falha: Falha na implementação da aplicação | N/A |
| ServiceStatus | 0 - inválido: O serviço não tem atualmente um Estado de funcionamento <br> 1 - ok: O serviço está em bom estado  <br> 2 - aviso: Pode haver um problema que requerem investigação <br> 3 - erro: Existe um problema de que precisa de investigação <br> 4 - desconhecido: O estado não é recuperável | N/A |
| ServiceReplicaStatus | 0 - inválido: A réplica não tem atualmente um Estado de funcionamento <br> 1 - ok: O serviço está em bom estado  <br> 2 - aviso: Pode haver um problema que requerem investigação <br> 3 - erro: Existe um problema de que precisa de investigação <br> 4 - desconhecido: O estado não é recuperável | N/A | 
| RestartCount | Número de reinicializações de contentor | N/A |

> [!NOTE]
> Os valores ServiceStatus e ServiceReplicaStatus são os mesmos que o [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) no Service Fabric. 

Cada métrica está disponível em dimensões diferentes para que possa ver agregados em diferentes níveis. A lista atual de dimensões são os seguintes:

* ApplicationName
* serviceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> A dimensão de CodePackageName não está disponível para aplicações do Linux. 

Cada dimensão corresponde a diferentes componentes do [modelo de aplicação do Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Monitor do Azure CLI

Uma lista completa de comandos está disponível na [documentos CLI do Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , mas a incluímos alguns exemplos úteis abaixo 

Cada exemplo, o ID de recurso segue este padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização da CPU dos contentores numa aplicação

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização da memória para cada réplica de serviço
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinício de cada contentor numa janela de 1 hora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média da CPU em todos os serviços com o nome "VotingWeb" numa janela de 1 hora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Explorador de métricas

Explorador de métricas é um painel no portal no qual pode ver todas as métricas para a sua aplicação de malha. Este painel está acessível na página do aplicativo no portal e o painel do Azure monitor, a última opção que pode utilizar para visualizar as métricas para todos os seus recursos do Azure que suportam o Azure Monitor. 

![Explorador de Métricas](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas do Azure Monitor, consulte a [documentos CLI do Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
