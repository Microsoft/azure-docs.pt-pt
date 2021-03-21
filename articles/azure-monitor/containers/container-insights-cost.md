---
title: Custos de monitorização dos insights do contentor | Microsoft Docs
description: Este artigo descreve o custo de monitorização das métricas & dados de inventário recolhidos por insights do Contentor para ajudar os clientes a gerir a sua utilização e os custos associados.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 78387e950d476126d7c2065a530844e44fd59b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728914"
---
# <a name="understand-monitoring-costs-for-container-insights"></a>Compreender os custos de monitorização dos insights do contentor

Este artigo fornece orientações de preços para informações sobre contentores para ajudá-lo a entender o seguinte:

* Como estimar os custos adiantado antes de ativar este Insight

* Como medir os custos após a informação do contentor ter sido habilitado para um ou mais contentores

* Como controlar a recolha de dados e fazer reduções de custos

O Azure Monitor Logs recolhe, indexa e armazena dados gerados pelo seu cluster Kubernetes. 

O modelo de preços do Azure Monitor baseia-se principalmente na quantidade de dados ingeridos em gigabytes por dia no seu espaço de trabalho Log Analytics. O custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados recolhidos, também depende do plano selecionado e quanto tempo escolheu para armazenar dados gerados a partir dos seus clusters.

>[!NOTE]
>Todos os tamanhos e preços são apenas para estimativa de amostragem. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/monitor/) do Azure Monitor para obter os preços mais recentes com base no seu modelo de preços Azure Monitor Log Analytics e na região de Azure.

Segue-se um resumo dos tipos de dados recolhidos a partir de um cluster Kubernetes com insights de contentores que influenciam o custo e podem ser personalizados com base na sua utilização:

- Stdout, registos de contentores stderr de todos os recipientes monitorizados em cada espaço de nome Kubernetes no cluster

- Variáveis do ambiente do contentor de cada recipiente monitorizado no cluster

- Trabalhos/cápsulas de Kubernetes concluídos no cluster que não requer monitorização

- Raspagem ativa das métricas prometheus

- Recolha de [registos](../../aks/view-control-plane-logs.md) de diagnóstico de registos de nó de nó de kubernetes no seu cluster AKS para analisar dados de registo gerados por componentes principais, tais como o *kube-apiserver* e *o kube-controller-manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>O que é recolhido dos clusters kubernetes

Os insights do contentor incluem um conjunto predefinido de métricas e itens de inventário recolhidos que são escritos como dados de registo no seu espaço de trabalho Log Analytics. Todas as métricas listadas abaixo são recolhidas por padrão a cada minuto.

### <a name="node-metrics-collected"></a>Métricas de nó recolhidos

Seguem-se as 24 métricas por nó que são recolhidas:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memóriaRssBytes
- memoryWorkingSetBytes
- memoryCapyBytes
- memoryAllocatableBytes
- reiniciarTimeEpoch
- usado (disco)
- livre (disco)
- used_percent (disco)
- io_time (diskio)
- escreve (diskio)
- leituras (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (líquido)
- err_out (líquido)
- bytes_recv (líquido)
- bytes_sent (líquido)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>Métricas de contentores

Seguem-se as oito métricas por contentor recolhidos:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memóriaRssBytes
- memoryWorkingSetBytes
- memóriaRequestBytes
- memoryLimitBytes
- reiniciarTimeEpoch

### <a name="cluster-inventory"></a>Inventário de cluster

Segue-se a seguinte lista dos dados de inventário de cluster recolhidos por padrão:

- KubePodInventory – 1 por minuto por recipiente
- KubeNodeInventory – 1 por nó por minuto
- KubeServices – 1 por serviço por minuto
- ContentorInventory – 1 por recipiente por minuto

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Estimando custos para monitorizar o seu cluster AKS

A estimativa abaixo baseia-se num cluster Azure Kubernetes Service (AKS) com o seguinte exemplo de tamanho. Além disso, a estimativa aplica-se apenas às métricas e dados de inventário recolhidos. Para os registos de contentores (stdout, stderr e variáveis ambientais), varia com base nos tamanhos de log gerados pela carga de trabalho, e são excluídos da nossa estimativa.

Se ativar a monitorização de um cluster AKS configurado da seguinte forma,

- Três nosdes
- Dois discos por nó
- Uma interface de rede por nó
- 20 cápsulas (um contentor em cada vagem = 20 contentores no total)
- Dois espaços de nome Kubernetes
- Cinco serviços Kubernetes (inclui cápsulas, serviços e espaço de nomes do sistema kube)
- Frequência de recolha = 60 segundos (padrão)

Pode ver as tabelas e o volume de dados gerados por hora no espaço de trabalho do Log Analytics atribuído. Para obter mais informações sobre cada uma destas tabelas, consulte [os registos do Contentor.](container-insights-log-search.md#container-records)

|Tabela | Estimativa de tamanho (MB/hora) |
|------|---------------|
|Des | 12,9 |
|InsightsMetrics | 11,3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContentorInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

Total = 31 MB/Hora = 23,1 GB/mês (um mês = 31 dias)

Utilizando o [preço](https://azure.microsoft.com/pricing/details/monitor/) padrão para Log Analytics, que é um modelo Pay-As-You-Go, pode estimar o custo do Azure Monitor por mês. Após a inclusão de uma reserva de capacidade, o preço seria mais elevado por mês, dependendo da reserva selecionada.

## <a name="controlling-ingestion-to-reduce-cost"></a>Controlo da ingestão para reduzir custos

Considere um cenário em que a diferente unidade de negócio da sua organização partilhe a infraestrutura kubernetes e um espaço de trabalho log analytics. Com cada unidade de negócio separada por um espaço de nomes Kubernetes. Pode visualizar a quantidade de dados que são ingeridos em cada espaço de trabalho utilizando o livro de **dados** que está disponível a partir do dropdown dos **Livros de Visualização.**

[![Ver livros dropdown](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Este livro ajuda-o a visualizar a origem dos seus dados sem ter de construir a sua própria biblioteca de consultas a partir do que partilhamos na nossa documentação. Neste livro, existem gráficos com os quais pode ver dados faturantes de perspetivas como:

- Total de dados faturados em GB por solução
- Dados faturados ingeridos por registos de contentores (registos de aplicações)
- Registos de contentores faturados dados ingeridos por Kubernetes namespace
- Registos de contentores faturados ingeridos segregados pelo nome cluster
- Dados de registo de contentores ressarjáveis ingeridos por entrada de logsource
- Dados de diagnóstico faturados por registos de nó de mestre de diagnóstico

[![Livro de trabalho de utilização de dados](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Para saber sobre a gestão de direitos e permissões no livro, reveja o [controlo de acesso.](../visualize/workbooks-access-control.md)

Depois de completar a sua análise para determinar que fonte ou fontes estão a gerar mais dados ou mais dados que estão a exceder os seus requisitos, pode reconfigurar a recolha de dados. Os detalhes sobre a recolha de configuração de variáveis stdout, stderr e ambiental são descritos no artigo de configuração de configurações de recolha de dados do [agente Configure.](container-insights-agent-config.md)

Seguem-se exemplos das alterações que pode aplicar ao seu cluster modificando o ficheiro ConfigMap para ajudar a controlar o custo.

1. Desative os registos de sepido em todos os espaços de nome do cluster modificando o seguinte no ficheiro ConfigMap:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Desative os registos stderr do seu espaço de nome de desenvolvimento (por exemplo, **teste de dev),** e continue a recolher registos stderr de outros espaços de nome (por exemplo, **prod** e **predefinido)** modificando o seguinte no ficheiro ConfigMap:

    >[!NOTE]
    >A recolha de registos do sistema kube é desativada por padrão. A definição predefinida é mantida, adicionando espaço de nome **dev-teste** à lista de espaços de nome de exclusão é aplicado na recolha de registo stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Desative a recolha variável ambiental em todo o cluster modificando o seguinte no ficheiro ConfigMap. Isto é aplicável a todos os recipientes em cada espaço de nome Kubernetes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Para limpar os postos de trabalho concluídos, especifique a política de limpeza na definição de trabalho modificando o seguinte no ficheiro ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Depois de aplicar uma ou mais destas alterações ao seu ConfigMaps, consulte [aplicar o ConfigMap atualizado](container-insights-prometheus-integration.md#applying-updated-configmap) para aplicá-lo no seu cluster.

### <a name="prometheus-metrics-scraping"></a>Métricas prometheus raspando

Se estiver a utilizar [a raspagem métrica de Prometheus,](container-insights-prometheus-integration.md)certifique-se de que considera o seguinte para limitar o número de métricas que recolhe do seu cluster:

- Certifique-se de que a frequência de raspagem está definida da melhor forma (o padrão é de 60 segundos). Embora possa aumentar a frequência para 15 segundos, tem de se certificar de que as métricas que está a raspar são publicadas nessa frequência. Caso contrário, haverá muitas métricas duplicadas raspadas e enviadas para o seu espaço de trabalho Log Analytics em intervalos adicionando aos custos de ingestão e retenção de dados, mas são de menor valor. 

- Os insights do contentor suportam a exclusão & listas de inclusão por nome métrico. Por exemplo, se estiver a raspar métricas de **kubedns** no seu cluster, pode haver centenas delas que são raspadas por defeito, mas provavelmente só está interessado num subconjunto. Confirme que especificou uma lista de métricas para raspar, ou excluir outras, exceto algumas para economizar no volume de ingestão de dados. É fácil permitir a raspagem e não utilizar muitas dessas métricas, que apenas adicionarão custos adicionais à sua conta de Log Analytics.

- Ao raspar as anotações de pod, certifique-se de que filtra pelo espaço de nomes para que exclua a raspagem de métricas de pod de espaços de nome que não utiliza (por exemplo, espaço de nome **de teste dev).**

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como entender quais os custos que são suscetíveis de se basear em padrões de utilização recentes a partir de dados recolhidos com insights do Contentor, consulte [Gerir os seus custos de utilização e estimar](../logs/manage-cost-storage.md).