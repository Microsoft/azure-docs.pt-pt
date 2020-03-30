---
title: Monitorização e diagnósticos em aplicações de malha de tecido de serviço Azure
description: Conheça a aplicação de monitorização e diagnóstico na Malha de Tecido de Serviço no Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498156"
---
# <a name="monitoring-and-diagnostics"></a>Monitorização e diagnóstico
O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. A monitorização e diagnóstico da malha de tecido de serviço é categorizado em três tipos principais de dados de diagnóstico:

- Registos de aplicação - estes são definidos como os registos das suas aplicações contentorizadas, com base na forma como instrumentou a sua aplicação (por exemplo, registos de estivadores)
- Eventos da plataforma - eventos da plataforma Mesh relevantes para o seu funcionamento do contentor, atualmente incluindo ativação de contentores, desativação e rescisão.
- Métricas do recipiente - utilização de recursos e métricas de desempenho para os seus recipientes (estatísticas de estivadores)

Este artigo discute as opções de monitorização e diagnóstico para a versão de pré-visualização mais recente disponível.

## <a name="application-logs"></a>Registos de aplicação

Pode ver os seus troncos de estivador dos seus contentores implantados, por recipiente. No modelo de aplicação de malha de tecido de serviço, cada recipiente é um pacote de código na sua aplicação. Para ver os registos associados com um pacote de código, utilize o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Pode usar o comando "az mesh service-replica" para obter o nome da réplica. Os nomes de réplicas estão a incrementar os inteiros a partir de 0.

Aqui está o que isto parece para ver os registos do contentor VotingWeb.Código da aplicação de votação:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contentores 

O ambiente de malha expõe um punhado de métricas indicando como os seus contentores estão a funcionar. As seguintes métricas estão disponíveis através do portal Azure e do monitor Azure CLI:

| Métrica | Descrição | Unidades|
|----|----|----|
| CpuUtilização | ActualCpu/AllocatedCpu em percentagem | % |
| Utilização da Memória | Mem/AlocadoMem em percentagem | % |
| Cpu atribuído | Cpu atribuído de acordo com o modelo de Gestor de Recursos Azure | Millicores |
| Memória Atribuída | Memória atribuída de acordo com o modelo de Gestor de Recursos Azure | MB |
| ActualCpu | Utilização da CPU | Millicores |
| Memória Real | Uso da memória | MB |
| Estatuto de Contentores | 0 - Inválido: O estado do contentor é desconhecido <br> 1 - Pendente: O contentor tem programado para começar <br> 2 - Início: O recipiente está em fase de arranque <br> 3 - Iniciado: O recipiente começou com sucesso <br> 4 - Paragem: O contentor está a ser parado <br> 5 - Parado: O contentor parou com sucesso | N/D |
| Estatuto de Aplicação | 0 - Desconhecido: O estado não é recuperável <br> 1 - Pronto: A aplicação está a funcionar com sucesso <br> 2 - Upgrade: Há uma atualização em curso <br> 3 - Criação: A aplicação está a ser criada <br> 4 - Eliminação: O pedido está a ser eliminado <br> 5 - Falha: A aplicação não foi implementada | N/D |
| Estatuto de Serviço | 0 - Inválido: O serviço não tem atualmente um estado de saúde <br> 1 - Ok: O serviço é saudável  <br> 2 - Aviso: Pode haver algo errado que exija investigação <br> 3 - Erro: Há algo errado que precisa de investigação <br> 4 - Desconhecido: O estado não é recuperável | N/D |
| Estatuto de Replicação de Serviço | 0 - Inválido: A réplica não tem atualmente um estado de saúde <br> 1 - Ok: O serviço é saudável  <br> 2 - Aviso: Pode haver algo errado que exija investigação <br> 3 - Erro: Há algo errado que precisa de investigação <br> 4 - Desconhecido: O estado não é recuperável | N/D | 
| Reiniciar Contagem | Número de contentores reinicia | N/D |

> [!NOTE]
> Os valores ServiceStatus e ServiceReplicaStatus são os mesmos que o [Estado de Saúde](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) em Tecido de Serviço. 

Cada métrica está disponível em diferentes dimensões para que possa ver agregados em diferentes níveis. A lista atual de dimensões é a seguinte:

* ApplicationName
* ServiceName
* Nome de Réplica de Serviço
* CódigoPacoteNome

> [!NOTE]
> A dimensão CodePackageName não está disponível para aplicações Linux. 

Cada dimensão corresponde a diferentes componentes do modelo de [aplicação](service-fabric-mesh-service-fabric-resources.md#applications-and-services) de tecido de serviço

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Uma lista completa de comandos está disponível nos [docs do Monitor Azure CLI,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) mas incluímos alguns exemplos úteis abaixo 

Em cada exemplo, o ID de recursos segue este padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização dos contentores da CPU numa aplicação

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização da memória para cada réplica de serviço
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinicia para cada recipiente numa janela de 1 hora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média do CPU entre serviços chamados "VotingWeb" numa janela de 1 hora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Explorador de métricas

O explorador de métricas é uma lâmina no portal em que pode visualizar todas as métricas para a sua aplicação Mesh. Esta lâmina é acessível na página da aplicação no portal e na lâmina de monitor Azure, esta última das quais pode usar para visualizar métricas para todos os seus recursos Azure que suportam o Monitor Azure. 

![Explorador de Métricas](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passos seguintes
* Para saber mais sobre o Service Fabric Mesh, consulte [Descrição geral do Service Fabric Mesh](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas do Monitor Azure, consulte os [docs CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)do Monitor Azure .
