---
title: Custo de monitorização do Monitor Azure para contentores Microsoft Docs
description: Este artigo descreve o custo de monitorização das métricas & dados de inventário recolhidos pelo Azure Monitor para os contentores para ajudar os clientes a gerir a sua utilização e os custos associados.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 44802f611919fbf88894576eecf581a668e05b5b
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122466"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>Compreender os custos de monitorização do Monitor Azure para contentores

Este artigo fornece orientação de preços para o Monitor Azure para os contentores para ajudá-lo a entender o seguinte:

* Como estimar os custos adiantados antes de ativar este Insight

* Como medir os custos após o Monitor Azure para contentores ter sido ativado para um ou mais contentores

* Como controlar a recolha de dados e fazer reduções de custos

O Azure Monitor Logs recolhe, indexa e armazena dados gerados pelo seu cluster Kubernetes. 

O modelo de preços Do Monitor Azure baseia-se principalmente na quantidade de dados ingeridos em gigabytes por dia no seu espaço de trabalho Log Analytics. O custo de um espaço de trabalho log Analytics não se baseia apenas no volume de dados recolhidos, está também dependente do plano selecionado e do tempo que escolheu para armazenar dados gerados a partir dos seus clusters.

>[!NOTE]
>Todos os tamanhos e preços são apenas para estimativa de amostras. Consulte a página de [preços](https://azure.microsoft.com/pricing/details/monitor/) do Monitor Azure para os preços mais recentes com base no modelo de preços do Log Analytics do Monitor Azure e na região do Azure.

Segue-se um resumo dos tipos de dados recolhidos a partir de um cluster Kubernetes com o Monitor Azure para recipientes que influenciam o custo e podem ser personalizados com base na sua utilização:

- Stdout, troncos de contentores stderr de cada recipiente monitorizado em cada espaço de nome kubernetes no cluster

- Variáveis ambientais de contentores de todos os recipientes monitorizados no cluster

- Kubernetes concluídos empregos/casulos no cluster que não requer monitorização

- Raspagem ativa das métricas de Prometeu

- Recolha de [registos](../../aks/view-master-logs.md) de diagnóstico de registos de nó mestre kubernetes no seu cluster AKS para analisar dados de registo gerados por componentes mestres como o *kube-apiserver* e *o gestor do controlador kube*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>O que é recolhido dos aglomerados kubernetes

O Monitor Azure para contentores inclui um conjunto predefinido de métricas e itens de inventário recolhidos que são escritos como dados de registo no seu espaço de trabalho Log Analytics. Todas as métricas listadas abaixo são recolhidas por padrão a cada minuto.

### <a name="node-metrics-collected"></a>Métricas do nó recolhidas

A seguinte lista são as 24 métricas por nó que são recolhidas:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memóriaWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- reiniciarTimeEpoch
- utilizado (disco)
- grátis (disco)
- used_percent (disco)
- io_time (diskio)
- escreve (diskio)
- leituras (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (rede)
- err_out (rede)
- bytes_recv (rede)
- bytes_sent (rede)
- Kubelet_docker_operations (kubelet)

### <a name="container-metrics"></a>Métricas de contentores

A seguinte lista são as oito métricas por recipiente recolhidas:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memóriaWorkingSetBytes
- memóriaRequestBytes
- memóriaLimitBytes
- reiniciarTimeEpoch

### <a name="cluster-inventory"></a>Inventário de clusters

A lista que se segue são os dados de inventário do cluster recolhidos por padrão:

- KubePodInventory – 1 por minuto por recipiente
- KubeNodeInventário – 1 por nó por minuto
- KubeServices – 1 por serviço por minuto
- ContentorInventário – 1 por recipiente por minuto

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Estimativa de custos para monitorizar o seu cluster AKS

A estimativa abaixo baseia-se num cluster do Serviço Azure Kubernetes (AKS) com o seguinte exemplo de tamanho. Além disso, a estimativa aplica-se apenas às métricas e dados de inventário recolhidos. Para os registos de contentores (variáveis stdout, stderr e ambiental), varia em função dos tamanhos de registo gerados pela carga de trabalho, e estão excluídos da nossa estimativa.

Se ativar a monitorização de um cluster AKS configurado da seguinte forma,

- Três nódosos
- Dois discos por nó
- Uma interface de rede por nó
- 20 cápsulas (um recipiente em cada vagem = 20 contentores no total)
- Dois espaços de nome Kubernetes
- Cinco serviços Kubernetes (inclui cápsulas, serviços e espaço para o sistema kube)
- Frequência de recolha = 60 segundos (padrão)

Pode ver as tabelas e o volume de dados gerados por hora no espaço de trabalho de Log Analytics atribuído. Para obter mais informações sobre cada uma destas tabelas, consulte [os registos do Contentor.](container-insights-log-search.md#container-records)

|Tabela | Estimativa de tamanho (MB/hora) |
|------|---------------|
|Des | 12.9 |
|InsightsMetrics | 11.3 |
|Inventário KubePod | 1.5 |
|Inventário KubeNode | 0,75 |
|Serviços Kube | 0.13 |
|Inventário de Contentores | 3.6 |
|KubeHealth | 0.1 |
|Eventos KubeMonAgentEvents |0.005 |

Total = 31 MB/Hora = 23,1 GB/mês (um mês = 31 dias)

Utilizando os [preços predefinidos](https://azure.microsoft.com/pricing/details/monitor/) para o Log Analytics, que é um modelo Pay-As-You-Go, pode estimar o custo do Monitor Azure por mês. Após a inclusão de uma reserva de capacidade, o preço seria mais alto por mês, dependendo da reserva selecionada.

## <a name="controlling-ingestion-to-reduce-cost"></a>Controlo da ingestão para reduzir custos

Considere um cenário em que a unidade de negócio diferente da sua organização partilha a infraestrutura kubernetes e um espaço de trabalho log Analytics. Com cada unidade de negócio separada por um espaço de nome Kubernetes. Pode visualizar a quantidade de dados que são ingeridos em cada espaço de trabalho usando um livro recentemente lançado. O livro de utilização de Insights de **Contentores,** encontrado na galeria dos livros de [livros,](../platform/workbooks-overview.md#getting-started)ajuda-o a visualizar a origem dos seus dados sem ter de construir a sua própria biblioteca de consultas a partir do que partilhamos na nossa documentação. Neste livro, existem gráficos com os quais pode ver dados faturados de perspetivas como:

- Total de dados faturados ingeridos em GB por solução

- Dados faturados ingeridos por registos de contentores (registos de aplicações)

- Os registos de registos de contentores faturados são ingeridos por kubernetes espaço de nome

- Os registos de contentores faturados são ingeridos por nome cluster

- Dados de registo de contentores faturados ingeridos por entrada de logsource

- Dados de diagnóstico faturados ingeridos por registos de nós de diagnóstico

Depois de concluir a sua análise para determinar qual a fonte ou fontes que estão a gerar mais dados ou mais dados que excedem os seus requisitos, pode reconfigurar a recolha de dados. Os detalhes sobre a configuração da recolha de variáveis stdout, stderr e ambiental são descritos no artigo de definições de recolha de dados do [agente Configure.](container-insights-agent-config.md)

Seguem-se exemplos das alterações que pode aplicar ao seu cluster modificando o ficheiro ConfigMap para ajudar a controlar o custo.

1. Desative os registos stdout em todos os espaços de nome no cluster modificando o seguinte no ficheiro ConfigMap:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Desative a recolha de registos stderr do seu espaço de nome de desenvolvimento (por exemplo, **teste de v)** e continue a recolher registos de stderr de outros espaços de nome (por exemplo, **prod** e **predefinidos)** modificando o seguinte no ficheiro ConfigMap:

    >[!NOTE]
    >A recolha de registos do sistema kube é desativada por padrão. A definição predefinida é mantida, adicionando espaço de nome de **teste de v** à lista de espaços de nome de exclusão é aplicado à recolha de registos stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Desative a recolha de variáveis ambientais em todo o cluster modificando o seguinte no ficheiro ConfigMap. Isto é aplicável a todos os recipientes em cada espaço de nome kubernetes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Para limpar os postos de trabalho concluídos, especifique a política de limpeza na definição de emprego, modificando o seguinte no ficheiro ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Depois de aplicar uma ou mais destas alterações ao seu ConfigMaps, consulte [a Aplicação do ConfigMap atualizado](container-insights-prometheus-integration.md#applying-updated-configmap) para o aplicar ao seu cluster.

### <a name="prometheus-metrics-scraping"></a>Medição das métricas de Prometeu

Se estiver a utilizar a [raspagem métrica prometheus,](container-insights-prometheus-integration.md)certifique-se de que considera o seguinte para limitar o número de métricas que recolhe do seu cluster:

- Certifique-se de que a frequência de raspagem está óptimamente definida (a predefinição é de 60 segundos). Embora possa aumentar a frequência para 15 segundos, tem de se certificar de que as métricas que está a raspar são publicadas nessa frequência. Caso contrário, haverá muitas métricas duplicadas raspadas e enviadas para o seu espaço de trabalho log Analytics em intervalos adicionando aos custos de ingestão e retenção de dados, mas são de menor valor. 

- O Monitor Azure para contentores suporta exclusão & listas de inclusão por nome métrico. Por exemplo, se estiver a raspar métricas **de kubedns** no seu cluster, pode haver centenas delas que são raspadas por defeito, mas provavelmente só está interessado num subconjunto. Confirme que especificou uma lista de métricas para raspar, ou excluir outras, exceto algumas para economizar no volume de ingestão de dados. É fácil permitir raspar e não usar muitas dessas métricas, que só adicionarão taxas adicionais à sua conta de Log Analytics.

- Ao raspar anotações de pod, certifique-se de filtrar por espaço de nome para que exclua a raspagem de métricas de pod de espaços de nome que não utiliza (por exemplo, espaço de nome de **v-test).**

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como entender quais os custos que serão baseados em padrões de utilização recentes a partir de dados recolhidos com o Monitor Azure para contentores, consulte Gerir o [seu uso e estimar custos](../platform/manage-cost-storage.md).