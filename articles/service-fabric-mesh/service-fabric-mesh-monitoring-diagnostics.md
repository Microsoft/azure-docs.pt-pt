---
title: Monitorização e diagnósticos em aplicativos de malha de tecido de serviço Azure
description: Saiba mais sobre monitorização e diagnóstico de aplicação na malha de tecido de serviço em Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02de8ea5dd5c53192d2b8c7beba8bc36143beac6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626999"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. A monitorização e diagnóstico da malha de tecido de serviço é categorizada em três tipos principais de dados de diagnóstico:

- Registos de aplicações - estes são definidos como os registos das suas aplicações contentorizadas, com base na forma como instrumentou a sua aplicação (por exemplo, registos de estivadores)
- Eventos da plataforma - eventos da plataforma Mesh relevantes para o seu funcionamento do contentor, atualmente incluindo ativação de contentores, desativação e rescisão.
- Métricas de contentores - métricas de utilização de recursos e desempenho para os seus recipientes (estatísticas de estivadores)

Este artigo discute as opções de monitorização e diagnóstico para a versão de pré-visualização mais recente disponível.

## <a name="application-logs"></a>Registos de aplicações

Pode ver os seus registos de estivadores dos seus contentores implantados, numa base por contentor. No modelo de aplicação de malha de tecido de serviço, cada recipiente é um pacote de código na sua aplicação. Para ver os registos associados com um pacote de código, utilize o seguinte comando:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Pode utilizar o comando "az mesh-replica" para obter o nome da réplica. Os nomes das réplicas estão a aumentar os números inteiros a partir de 0.

Aqui está o que isto parece para ver os registos do recipiente VoteWeb.Code a partir da aplicação de voto:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contentores 

O ambiente de malha expõe um punhado de métricas indicando como os seus recipientes estão a funcionar. As seguintes métricas estão disponíveis através do portal Azure e do monitor Azure CLI:

| Metric | Descrição | Unidades|
|----|----|----|
| CpuUtilização | ActualCpu/AlocadoCpu em percentagem | % |
| Utilização da memória | ActualMem/AlocadoMem em percentagem | % |
| Cpu atribuído | CPU atribuído de acordo com o modelo de Gestor de Recursos Azure | Millicores |
| Roteiro Atribuído | Memória atribuída de acordo com o modelo do Gestor de Recursos Azure | MB |
| ActualCpu | Utilização da CPU | Millicores |
| ActualMemory | Utilização de memória | MB |
| Estatística do Contentor | 0 - Inválido: O estado do contentor é desconhecido <br> 1 - Pendente: O contentor tem início previsto <br> 2 - Início: O contentor está em fase de arranque <br> 3 - Iniciado: O contentor começou com sucesso <br> 4 - Paragem: O contentor está a ser parado <br> 5 - Parado: O contentor parou com sucesso | N/D |
| AplicaçãoStatus | 0 - Desconhecido: O estatuto não é recuperável <br> 1 - Pronto: A aplicação está a funcionar com sucesso <br> 2 - Upgrade: Há uma atualização em curso <br> 3 - Criação: A aplicação está a ser criada <br> 4 - Eliminação: O pedido está a ser suprimido <br> 5 - Falha: A aplicação falhou em implantar | N/D |
| ServiceStatus | 0 - Inválido: O serviço não tem atualmente um estado de saúde <br> 1 - Ok: O serviço é saudável  <br> 2 - Aviso: Pode haver algo errado que exija investigação <br> 3 - Erro: Há algo de errado que precisa de investigação <br> 4 - Desconhecido: O estado não é recuperável | N/D |
| ServiceReplicaStatus | 0 - Inválido: A réplica não tem atualmente um estado de saúde <br> 1 - Ok: O serviço é saudável  <br> 2 - Aviso: Pode haver algo errado que exija investigação <br> 3 - Erro: Há algo de errado que precisa de investigação <br> 4 - Desconhecido: O estado não é recuperável | N/D | 
| RestartCount | Número de contentores reinicia | N/D |

> [!NOTE]
> Os valores de ServiceStatus e ServiceReplicaStatus são os mesmos que o [HealthState](/dotnet/api/system.fabric.health.healthstate) in Service Fabric.

Cada métrica está disponível em diferentes dimensões para que possa ver agregados em diferentes níveis. A lista atual de dimensões é a seguinte:

* ApplicationName
* ServiceName
* ServiceReplicaName
* Nome codepackage

> [!NOTE]
> A dimensão CodePackageName não está disponível para aplicações Linux. 

Cada dimensão corresponde a diferentes componentes do modelo de [Aplicação de Tecido de Serviço](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Uma lista completa de comandos está disponível nos [docs CLI do Monitor Azure,](/cli/azure/monitor/metrics#az-monitor-metrics-list) mas incluímos alguns exemplos úteis abaixo 

Em cada exemplo, o ID de recurso segue este padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização do CPU dos contentores numa aplicação

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização da memória para cada réplica de serviço
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinicia para cada recipiente numa janela de 1 hora 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média do CPU em serviços denominados "VotingWeb" numa janela de 1 hora
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Explorador de Métricas

O explorador de métricas é uma lâmina no portal no qual pode visualizar todas as métricas para a sua aplicação de Malha. Esta lâmina está acessível na página da aplicação no portal e na lâmina do monitor Azure, a última das quais pode utilizar para visualizar métricas para todos os seus recursos Azure que suportam o Azure Monitor. 

![Explorador de Métricas](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas do Azure Monitor, consulte os [docs CLI do Monitor Azure](/cli/azure/monitor/metrics#az-monitor-metrics-list).
