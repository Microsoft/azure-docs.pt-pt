---
title: Configurar Azure Monitor para coleta de dados de agente de contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o Azure Monitor para agente de contêineres para controlar stdout/stderr e a coleção de logs de variáveis de ambiente.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: magoedte
ms.openlocfilehash: ada573cc919d775af52abc5a75004866aebbeddb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72033945"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurar a coleta de dados do agente para Azure Monitor para contêineres

Azure Monitor para contêineres coleta variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner implantadas em clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure) do agente em contêiner. Esse agente também pode coletar dados de série temporal (também conhecidos como métricas) do Prometheus usando o agente em contêiner sem precisar configurar e gerenciar um servidor e um banco de dados do Prometheus. Você pode definir as configurações de coleta de dados do agente criando um ConfigMaps kubernetes personalizado para controlar essa experiência. 

Este artigo demonstra como criar ConfigMap e configurar a coleta de dados com base em seus requisitos.

>[!NOTE]
>O suporte para Prometheus é um recurso em visualização pública no momento.
>

## <a name="configmap-file-settings-overview"></a>Visão geral das configurações do arquivo ConfigMap

Um arquivo de modelo ConfigMap é fornecido para que você possa editá-lo facilmente com suas personalizações sem precisar criá-lo do zero. Antes de começar, você deve examinar a documentação do kubernetes sobre [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e se familiarizar com como criar, configurar e implantar o ConfigMaps. Isso permitirá que você filtre stderr e stdout por namespace ou por todo o cluster e variáveis de ambiente para qualquer contêiner em execução em todos os pods/nós no cluster.

>[!IMPORTANT]
>A versão mínima do agente com suporte para coletar variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner é ciprod06142019 ou posterior. A versão mínima do agente com suporte para métricas de Prometheus de sucata é ciprod07092019 ou posterior. Para verificar a versão do agente, na guia **nó** , selecione um nó e, no painel Propriedades, observe o valor da propriedade **marca da imagem do agente** .  

### <a name="data-collection-settings"></a>Configurações de coleta de dados

A seguir estão as configurações que podem ser definidas para controlar a coleta de dados.

|Chave |Tipo de dados |Value |Descrição |
|----|----------|------|------------|
|`schema-version` |Cadeia de caracteres (diferencia maiúsculas de minúsculas) |v1 |Esta é a versão do esquema usada pelo agente ao analisar este ConfigMap. A versão de esquema com suporte atualmente é v1. Não há suporte para a modificação desse valor e ele será rejeitado quando ConfigMap for avaliado.|
|`config-version` |Cadeia | | Dá suporte à capacidade de controlar a versão deste arquivo de configuração no sistema/repositório do controle do código-fonte. Os caracteres máximos permitidos são 10 e todos os outros caracteres são truncados. |
|`[log_collection_settings.stdout] enabled =` |Booleano | true ou false | Isso controla se a coleta de log de contêiner stdout está habilitada. Quando definido como `true` e nenhum namespace é excluído para a coleta de log de stdout (configuração de `log_collection_settings.stdout.exclude_namespaces` abaixo), os logs de stdout serão coletados de todos os contêineres em todos os pods/nós no cluster. Se não for especificado em ConfigMaps, o valor padrão será `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Cadeia | Matriz separada por vírgulas |Matriz de namespaces kubernetes para os quais os logs de stdout não serão coletados. Essa configuração só será eficaz se `log_collection_settings.stdout.enabled` estiver definida como `true`. Se não for especificado em ConfigMap, o valor padrão será `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Booleano | true ou false |Isso controla se a coleta de log de contêiner stderr está habilitada. Quando definido como `true` e nenhum namespace é excluído para a coleta de log de stdout (configuração `log_collection_settings.stderr.exclude_namespaces`), os logs de stderr serão coletados de todos os contêineres em todos os pods/nós no cluster. Se não for especificado em ConfigMaps, o valor padrão será `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Cadeia |Matriz separada por vírgulas |Matriz de namespaces kubernetes para os quais os logs de stderr não serão coletados. Essa configuração só será eficaz se `log_collection_settings.stdout.enabled` estiver definida como `true`. Se não for especificado em ConfigMap, o valor padrão será `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Booleano | true ou false | Essa configuração controla a coleção de variáveis de ambiente em todos os pods/nós no cluster e usa como padrão `enabled = true` quando não especificado em ConfigMaps. Se a coleção de variáveis de ambiente for habilitada globalmente, você poderá desabilitá-la para um contêiner específico definindo a variável de ambiente `AZMON_COLLECT_ENV` como **false** com uma configuração Dockerfile ou no [arquivo de configuração para o Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) sob a  **env:** seção. Se a coleção de variáveis de ambiente for desabilitada globalmente, você não poderá habilitar a coleta para um contêiner específico (ou seja, a única substituição que pode ser aplicada no nível de contêiner será desabilitar a coleta quando ela já estiver habilitada globalmente.). |

### <a name="prometheus-scraping-settings"></a>Configurações de recorte de Prometheus

![Arquitetura de monitoramento de contêiner para Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

O Azure Monitor para contêineres fornece uma experiência simples para habilitar a coleta de métricas Prometheus por meio de vários recortes por meio dos mecanismos a seguir, conforme mostrado na tabela a seguir. As métricas são coletadas por meio de um conjunto de configurações especificado em um único arquivo ConfigMap, que é o mesmo arquivo usado para configurar a coleta de variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner. 

A recorte ativa de métricas de Prometheus é executada de uma das duas perspectivas:

* URL de todo o cluster-HTTP e descobrir destinos de pontos de extremidade listados de um serviço, serviços k8ss como Kube-DNS e Kube-State-métricas e anotações de Pod específicas para um aplicativo. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus data_collection_settings. cluster]* .
* URL em todo o nó-HTTP e descobrir destinos de pontos de extremidade listados de um serviço. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Ponto Final | Scope | Exemplo |
|----------|-------|---------|
| Anotação de Pod | Em todo o cluster | anotações <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Serviço kubernetes | Em todo o cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/ponto de extremidade | Por nó e/ou em todo o cluster | `http://myurl:9101/metrics` |

Quando uma URL é especificada, Azure Monitor para contêineres apenas captura o ponto de extremidade. Quando o serviço kubernetes é especificado, o nome do serviço é resolvido com o servidor DNS do cluster para obter o endereço IP e, em seguida, o serviço resolvido é recapturado.

|Scope | Chave | Tipo de dados | Value | Descrição |
|------|-----|-----------|-------|-------------|
| Em todo o cluster | | | | Especifique qualquer um dos três métodos a seguir para recorte de pontos de extremidade para métricas. |
| | `urls` | Cadeia | Matriz separada por vírgulas | Ponto de extremidade HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE _IP é um Azure Monitor específico para o parâmetro containers e pode ser usado em vez do endereço IP do nó. Deve estar tudo em maiúsculas.) |
| | `kubernetes_services` | Cadeia | Matriz separada por vírgulas | Uma matriz de serviços Kubernetess para recorte de métricas de métricas de Kube-State. Por exemplo, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Booleano | true ou false | Quando definido como `true` nas configurações de todo o cluster, Azure Monitor para o agente de contêineres irá recriar os pods de kubernetes em todo o cluster para as seguintes anotações Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleano | true ou false | Habilita a recorte do pod. `monitor_kubernetes_pods` deve ser definido como `true`. |
| | `prometheus.io/scheme` | Cadeia | http ou https | O padrão é a sucateação sobre HTTP. Se necessário, defina como `https`. | 
| | `prometheus.io/path` | Cadeia | Matriz separada por vírgulas | O caminho do recurso HTTP para o qual buscar métricas. Se o caminho de métrica não for `/metrics`, defina-o com esta anotação. |
| | `prometheus.io/port` | Cadeia | 9102 | Especifique uma porta para escuta. Se a porta não estiver definida, o padrão será 9102. |
| Em todo o nó | `urls` | Cadeia | Matriz separada por vírgulas | Ponto de extremidade HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE _IP é um Azure Monitor específico para o parâmetro containers e pode ser usado em vez do endereço IP do nó. Deve estar tudo em maiúsculas.) |
| Em todo o nó ou cluster | `interval` | Cadeia | 60 s | O padrão de intervalo de coleta é de um minuto (60 segundos). Você pode modificar a coleção para *[prometheus_data_collection_settings. Node]* e/ou *[prometheus_data_collection_settings. cluster]* para unidades de tempo como ns, US (ou Âμs), MS, s, m, h. |
| Em todo o nó ou cluster | `fieldpass`<br> `fielddrop`| Cadeia | Matriz separada por vírgulas | Você pode especificar determinadas métricas a serem coletadas ou não no ponto de extremidade, definindo a listagem permitir (`fieldpass`) e não permitir (`fielddrop`). Você deve definir a lista de permissões primeiro. |

ConfigMaps é uma lista global e pode haver apenas um ConfigMap aplicado ao agente. Você não pode ter outro ConfigMaps que se refaça com as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configurar e implantar ConfigMaps

Execute as etapas a seguir para configurar e implantar o arquivo de configuração do ConfigMap no cluster.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o arquivo do modelo ConfigMap YAML e salve-o como contêiner-AZM-MS-agentconfig. YAML.  
1. Edite o arquivo ConfigMap YAML com suas personalizações.

    - Para excluir namespaces específicos para coleta de log de stdout, configure a chave/valor usando o exemplo a seguir: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Para desabilitar a coleção de variáveis de ambiente para um contêiner específico, defina a chave/valor `[log_collection_settings.env_var] enabled = true` para habilitar a coleção de variáveis globalmente e siga as etapas [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para concluir a configuração para o contêiner específico.
    
    - Para desabilitar a coleção de logs stderr em todo o cluster, configure a chave/valor usando o seguinte exemplo: `[log_collection_settings.stderr] enabled = false`.
    
    - Os exemplos a seguir demonstram como configurar as métricas de arquivo ConfigMap de uma URL em todo o cluster, do DameonSet de todo o nó do agente e especificando uma anotação de Pod

        - Recorte as métricas de Prometheus de uma URL específica pelo cluster.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - Recorte as métricas de Prometheus do Daemonset de um agente em execução em cada nó no cluster.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - Recorte as métricas do Prometheus especificando uma anotação de Pod.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. Crie ConfigMap executando o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created`.

Para verificar se a configuração foi aplicada com êxito, use o seguinte comando para examinar os logs de um pod do agente: `kubectl logs omsagent-fdf58 -n=kube-system`. Se houver erros de configuração do pods omsagent, a saída mostrará erros semelhantes ao seguinte:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Os erros relacionados à aplicação de alterações de configuração para Prometheus também estão disponíveis para revisão.  Seja dos logs de um pod de agente usando o mesmo comando `kubectl logs` ou de logs dinâmicos. Os logs ao vivo mostram erros semelhantes ao seguinte:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Os erros impedem que o omsagent analise o arquivo, fazendo com que ele seja reiniciado e use a configuração padrão. Depois de corrigir os erros em ConfigMap, salve o arquivo YAML e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Aplicando ConfigMap atualizado

Se você já tiver implantado um ConfigMap em seu cluster e quiser atualizá-lo com uma configuração mais recente, poderá editar o arquivo ConfigMap que você usou anteriormente e, em seguida, aplicar usando o mesmo comando que antes, `kubectl apply -f <configmap_yaml_file.yaml`.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verificando a versão do esquema

As versões de esquema de configuração com suporte estão disponíveis como uma anotação de Pod (versões de esquema) no pod omsagent. Você pode vê-los com o seguinte comando kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

A saída será exibida de forma semelhante à seguinte com a anotação Schema-Versions:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="review-prometheus-data-usage"></a>Examinar o uso de dados do Prometheus

Para exibir as métricas de Prometheus recortadas por Azure Monitor, especifique "Prometheus" como o namespace. Aqui está uma consulta de exemplo para exibir as métricas de Prometheus do namespace `default` kubernetes.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| extend tags=parse_json(Tags)
| where tostring(tags.namespace) == "default" 
```

Os dados de Prometheus também podem ser consultados diretamente por nome.

```
InsightsMetrics 
| where Name contains "some_prometheus_metric"
```

Para identificar o volume de ingestão de cada tamanho de métrica em GB por dia para entender se ele é alto, a consulta a seguir é fornecida.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A saída mostrará resultados semelhantes ao seguinte:

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-agent-config/log-query-example-usage-03.png)

Para estimar o que cada métrica tamanho em GB é para um mês para entender se o volume de dados ingeridos recebidos no espaço de trabalho é alto, a consulta a seguir é fornecida.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

A saída mostrará resultados semelhantes ao seguinte:

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-agent-config/log-query-example-usage-02.png)

Mais informações sobre como monitorar o uso de dados e analisar o custo estão disponíveis em [gerenciar o uso e os custos com os logs de Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Passos seguintes

Azure Monitor para contêineres não inclui um conjunto predefinido de alertas. Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps.

- Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
