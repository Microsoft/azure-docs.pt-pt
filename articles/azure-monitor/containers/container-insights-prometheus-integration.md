---
title: Configure Container insights Prometheus Integration | Microsoft Docs
description: Este artigo descreve como pode configurar o agente de insights do Contentor para raspar métricas de Prometeu com o seu cluster Kubernetes.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 8affeb472b9452e4d234e99e5ea6bb4509770fac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731736"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Configurar a raspagem das métricas prometheus com insights de contentores

[Prometheus](https://prometheus.io/) é uma solução popular de monitorização métrica de código aberto e faz parte da [Cloud Native Compute Foundation.](https://www.cncf.io/) Os insights do recipiente proporcionam uma experiência de embarque perfeita para recolher métricas de Prometeu. Normalmente, para utilizar o Prometheus, é necessário configurar e gerir um servidor Prometheus com uma loja. Ao integrar-se com o Azure Monitor, não é necessário um servidor Prometheus. Basta expor o ponto final das métricas Prometheus através dos seus exportadores ou cápsulas (aplicação), e o agente contentorizado para insights de contentores pode raspar as métricas para si. 

![Arquitetura de monitorização de contentores para Prometeu](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>A versão de agente mínimo suportada para raspar métricas prometheus é ciprod07092019 ou posterior, e a versão do agente suportada para a configuração de escrita e erros de agente na `KubeMonAgentEvents` tabela é ciprod10112019. Para Azure Red Hat OpenShift e Red Hat OpenShift v4, versão de agente ciprod04162020 ou superior. 
>
>Para obter mais informações sobre as versões do agente e o que está incluído em cada versão, consulte [as notas de lançamento](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)do agente . 
>Para verificar a versão do seu agente, a partir do separador **Nó** selecione um nó e no valor de nota de painel de propriedades da propriedade **De Imagem de Agente.**

A raspagem das métricas de Prometeu é suportada com clusters Kubernetes hospedados em:

- Azure Kubernetes Service (AKS)
- Pilha Azure ou no local
- Azure Red Hat OpenShift versão 3.x
- Azure Red Hat OpenShift e Red Hat OpenShift versão 4.x

### <a name="prometheus-scraping-settings"></a>Definições de raspagem prometheus

A raspagem ativa das métricas de Prometeu é realizada a partir de uma de duas perspetivas:

* Em todo o cluster - HTTP URL e descubra alvos a partir de pontos finais listados de um serviço. Por exemplo, serviços de k8s como kube-dns e kube-state-metrics, e anotações de pod específicas para uma aplicação. As métricas recolhidas neste contexto serão definidas na secção ConfigMap *[Prometheus data_collection_settings.cluster]*.
* Node-wide - HTTP URL e descubra alvos a partir de pontos finais listados de um serviço. As métricas recolhidas neste contexto serão definidas na secção ConfigMap *[Prometheus_data_collection_settings.node]*.

| Ponto final | Âmbito | Exemplo |
|----------|-------|---------|
| Anotação de pod | Em todo o agrupamento | anotações: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Serviço Kubernetes | Em todo o agrupamento | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/endpoint | Por nó e/ou em largura de cluster | `http://myurl:9101/metrics` |

Quando um URL é especificado, os insights do recipiente apenas raspam o ponto final. Quando o serviço Kubernetes é especificado, o nome de serviço é resolvido com o servidor DNS do cluster para obter o endereço IP e, em seguida, o serviço resolvido é raspado.

|Âmbito | Chave | Tipo de dados | Valor | Descrição |
|------|-----|-----------|-------|-------------|
| Em todo o agrupamento | | | | Especifique qualquer um dos três métodos seguintes para raspar pontos finais para métricas. |
| | `urls` | String | Matriz separada por vírgula | Ponto final HTTP (endereço IP ou caminho URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE_IP é um parâmetro específico de insights do contentor e pode ser usado em vez de um endereço IP de nó. Deve ser tudo maiúscula.) |
| | `kubernetes_services` | String | Matriz separada por vírgula | Uma série de serviços kubernetes para raspar métricas de kube-state-metrics. Por exemplo, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Booleano | true ou false | Quando definido `true` para as configurações de largura do cluster, o agente de insights de contentores raspará as cápsulas de Kubernetes em todo o cluster para as seguintes anotações prometeu:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleano | true ou false | Permite raspar a cápsula. `monitor_kubernetes_pods` deve ser definido para `true` . |
| | `prometheus.io/scheme` | String | http ou https | Predefinições para raspar em HTTP. Se necessário, dedão `https` . | 
| | `prometheus.io/path` | String | Matriz separada por vírgula | O caminho de recurso HTTP para obter métricas de. Se o caminho das métricas não `/metrics` for, defina-o com esta anotação. |
| | `prometheus.io/port` | String | 9102 | Especifique uma porta para raspar. Se a porta não estiver definida, ficará em incumprimento para o 9102. |
| | `monitor_kubernetes_pods_namespaces` | String | Matriz separada por vírgula | Uma lista de espaços de nome para raspar métricas de kubernetes pods.<br> Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Node-wide | `urls` | String | Matriz separada por vírgula | Ponto final HTTP (endereço IP ou caminho URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE_IP é um parâmetro específico de insights do contentor e pode ser usado em vez de um endereço IP de nó. Deve ser tudo maiúscula.) |
| Node-wide ou Cluster-wide | `interval` | String | Anos 60 | O intervalo de recolha é de um minuto (60 segundos). Pode modificar a coleção para as unidade *prometheus_data_collection_settings* *prometheus_data_collection_settings s* de tempo como s, m, h. |
| Node-wide ou Cluster-wide | `fieldpass`<br> `fielddrop`| String | Matriz separada por vírgula | Pode especificar determinadas métricas a serem recolhidas ou não a partir do ponto final, definindo a listagem de permitir ( `fieldpass` ) e não permitir ( `fielddrop` ). Tem de definir a lista de autorizações primeiro. |

ConfigMaps é uma lista global e pode haver apenas um ConfigMap aplicado ao agente. Não pode ter outro ConfigMaps a anular as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configurar e implementar ConfigMaps

Execute os seguintes passos para configurar o seu ficheiro de configuração ConfigMap para os seguintes clusters:

* Azure Kubernetes Service (AKS)
* Pilha Azure ou no local
* Azure Red Hat OpenShift versão 4.x e Red Hat OpenShift versão 4.x

1. [Descarregue](https://aka.ms/container-azm-ms-agentconfig) o ficheiro ConfigMap yaml do modelo e guarde-o como contentor-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Este passo não é necessário ao trabalhar com o Azure Red Hat OpenShift uma vez que o modelo ConfigMap já existe no cluster.

2. Edite o ficheiro ConfigMap yaml com as suas personalizações para raspar as métricas de Prometeu.

    >[!NOTE]
    >Se estiver a editar o ficheiro ConfigMap yaml para Azure Red Hat OpenShift, primeiro executar o comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o ficheiro num editor de texto.

    >[!NOTE]
    >A seguinte anotação `openshift.io/reconcile-protect: "true"` deve ser adicionada sob os metadados do *contentor-azm-ms-agentconfig* ConfigMap para evitar a reconciliação. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Para recolher os serviços kubernetes em todo o cluster, configuure o ficheiro ConfigMap usando o seguinte exemplo.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Para configurar a raspagem das métricas prometheus a partir de um URL específico em todo o cluster, configurar o ficheiro ConfigMap usando o seguinte exemplo.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Para configurar a raspagem das métricas prometheus do DaemonSet de um agente para cada nó individual no cluster, configurar o seguinte no ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP é um parâmetro específico de insights do contentor e pode ser usado em vez de um endereço IP de nó. Deve ser tudo maiúscula. 

    - Para configurar a raspagem das métricas de Prometeu, especificando uma anotação de vagem, execute os seguintes passos:

       1. No ConfigMap, especifique o seguinte:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Especificar a seguinte configuração para anotações de pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se pretende restringir a monitorização a espaços específicos para cápsulas que tenham anotações, por exemplo, inclua apenas cápsulas dedicadas a cargas de trabalho de produção, definir o `monitor_kubernetes_pod` `true` ConfigMap e adicionar o filtro de espaço de nome especificando os espaços de `monitor_kubernetes_pods_namespaces` nome para raspar. Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Executar o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` .
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

A alteração de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas omsagentes do cluster recomeçarão. O recomeço é um recomeço rolante para todas as cápsulas omsagent, nem todas reiniciam ao mesmo tempo. Quando o recomeço estiver terminado, é exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created` .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>Configurar e implementar ConfigMaps - Azure Red Hat OpenShift v3

Esta secção inclui os requisitos e passos para configurar com sucesso o seu ficheiro de configuração ConfigMap para o cluster Azure Red Hat OpenShift v3.x.

>[!NOTE]
>Para O Azure Red Hat OpenShift v3.x, um ficheiro ConfigMap do modelo é criado no espaço de nomes de *registo de azure-azure-azure.* Não está configurado para raspar ativamente métricas ou recolha de dados do agente.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que é membro do grupo de administração do Customer Cluster do seu cluster Azure Red Hat OpenShift para configurar as definições de raspagem do agente contentor e do Prometeu. Para verificar se é membro do grupo *osa-cliente-administradores,* executar o seguinte comando:

``` bash
  oc get groups
```

A saída assemelha-se-á ao seguinte:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Se for membro do grupo *osa-cliente-administração,* deverá ser capaz de listar o `container-azm-ms-agentconfig` ConfigMap utilizando o seguinte comando:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A saída assemelha-se-á ao seguinte:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Ativar monitorização

Execute os seguintes passos para configurar o seu ficheiro de configuração ConfigMap para o seu cluster Azure Red Hat OpenShift v3.x.

1. Edite o ficheiro ConfigMap yaml com as suas personalizações para raspar as métricas de Prometeu. O modelo ConfigMap já existe no cluster Red Hat OpenShift v3. Executar o comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o ficheiro num editor de texto.

    >[!NOTE]
    >A seguinte anotação `openshift.io/reconcile-protect: "true"` deve ser adicionada sob os metadados do *contentor-azm-ms-agentconfig* ConfigMap para evitar a reconciliação. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Para recolher os serviços kubernetes em todo o cluster, configuure o ficheiro ConfigMap usando o seguinte exemplo.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Para configurar a raspagem das métricas prometheus a partir de um URL específico em todo o cluster, configurar o ficheiro ConfigMap usando o seguinte exemplo.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Para configurar a raspagem das métricas prometheus do DaemonSet de um agente para cada nó individual no cluster, configurar o seguinte no ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE_IP é um parâmetro específico de insights do contentor e pode ser usado em vez de um endereço IP de nó. Deve ser tudo maiúscula. 

    - Para configurar a raspagem das métricas de Prometeu, especificando uma anotação de vagem, execute os seguintes passos:

       1. No ConfigMap, especifique o seguinte:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Especificar a seguinte configuração para anotações de pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se pretende restringir a monitorização a espaços específicos para cápsulas que tenham anotações, por exemplo, inclua apenas cápsulas dedicadas a cargas de trabalho de produção, definir o `monitor_kubernetes_pod` `true` ConfigMap e adicionar o filtro de espaço de nome especificando os espaços de `monitor_kubernetes_pods_namespaces` nome para raspar. Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Guarde as suas mudanças no editor.

A alteração de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas omsagentes do cluster recomeçarão. O recomeço é um recomeço rolante para todas as cápsulas omsagent, nem todas reiniciam ao mesmo tempo. Quando o recomeço estiver terminado, é exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created` .

Pode ver o ConfigMap atualizado executando o comando, `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` . 

## <a name="applying-updated-configmap"></a>Aplicação ConfigMap atualizado

Se já implementou um ConfigMap para o seu cluster e pretende atualizá-lo com uma configuração mais recente, pode editar o ficheiro ConfigMap que utilizou anteriormente e, em seguida, aplicar usando os mesmos comandos de antes.

Para os seguintes ambientes Kubernetes:

- Azure Kubernetes Service (AKS)
- Pilha Azure ou no local
- Azure Red Hat OpenShift e Red Hat OpenShift versão 4.x

executar o comando `kubectl apply -f <configmap_yaml_file.yaml` . 

Para um cluster Azure Red Hat OpenShift v3.x, executar o comando, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o ficheiro no seu editor predefinido para modificar e, em seguida, guardá-lo.

A alteração de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas omsagentes do cluster recomeçarão. O recomeço é um recomeço rolante para todas as cápsulas omsagent, nem todas reiniciam ao mesmo tempo. Quando o recomeço estiver terminado, é exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verify-configuration"></a>Verificar configuração

Para verificar se a configuração foi aplicada com sucesso a um cluster, utilize o seguinte comando para rever os registos de uma cápsula de agente: `kubectl logs omsagent-fdf58 -n=kube-system` . 

>[!NOTE]
>Este comando não é aplicável ao cluster Azure Red Hat OpenShift v3.x.
> 

Se houver erros de configuração das cápsulas omsagent, a saída mostrará erros semelhantes aos seguintes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Estão também disponíveis erros relacionados com a aplicação de alterações de configuração para revisão. Estão disponíveis as seguintes opções para efetuar uma resolução adicional de alterações de configuração e raspagem das métricas prometheus:

- A partir de registos de cápsulas de agente usando o mesmo `kubectl logs` comando 
    >[!NOTE]
    >Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
    > 

- A partir de Dados Ao Vivo (pré-visualização). Os registos de Dados Ao Vivo (pré-visualização) mostram erros semelhantes aos seguintes:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- A partir da tabela **KubeMonAgentEvents** no seu espaço de trabalho Log Analytics. Os dados são enviados a cada hora com a gravidade *do Aviso* para erros de raspagem e gravidade *de erro* para erros de configuração. Se não houver erros, a entrada na tabela terá dados com *informação* de gravidade , que não reporta erros. A propriedade **Tags** contém mais informações sobre o ID do casulo e do contentor no qual ocorreu o erro e também a primeira ocorrência, última ocorrência, e contagem na última hora.

- Para a azure Red Hat OpenShift v3.x e v4.x, verifique os registos omsagent, procurando na tabela **ContainerLog** para verificar se está ativada a recolha de registos de registo de registos de azure-azure..

Os erros impedem que o omsagent analise o ficheiro, fazendo com que reinicie e utilize a configuração predefinida. Depois de corrigir os erros em ConfigMap em clusters que não o Azure Red Hat OpenShift v3.x, guarde o ficheiro yaml e aplique os ConfigMaps atualizados executando o comando: `kubectl apply -f <configmap_yaml_file.yaml` . 

Para Azure Red Hat OpenShift v3.x, edite e guarde os ConfigMaps atualizados executando o comando: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` .

## <a name="query-prometheus-metrics-data"></a>Dados de métricas de Consulta Prometheus

Para visualizar as métricas prometheus raspadas pelo Azure Monitor e quaisquer erros de configuração/raspagem reportados pelo agente, reveja os [dados das métricas da Query Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) e [a Consulta config ou erros de raspagem](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Ver métricas de Prometeu em Grafana

As informações do contentor suportam métricas de visualização armazenadas no seu espaço de trabalho Log Analytics nos dashboards grafana. Fornecemos um modelo que você pode baixar do [repositório](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do painel de grafana para começar e referência para ajudá-lo a aprender a consultar dados adicionais dos seus clusters monitorizados para visualizar em dashboards grafana personalizados. 

## <a name="review-prometheus-data-usage"></a>Rever utilização de dados da Prometheus

Para identificar o volume de ingestão de cada tamanho de métrica em GB por dia para entender se é elevado, é fornecida a seguinte consulta.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

A saída apresentará resultados semelhantes aos seguintes:

![A screenshot mostra os resultados da consulta de registo do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Para estimar qual o tamanho de cada métrica em GB é por um mês para entender se o volume de dados ingeridos recebidos no espaço de trabalho é elevado, é fornecida a seguinte consulta.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

A saída apresentará resultados semelhantes aos seguintes:

![Resultados da consulta de registo do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Mais informações sobre como monitorizar a utilização dos dados e analisar o custo estão disponíveis na [Gestão de utilização e custos com registos do Monitor Azure](../logs/manage-cost-storage.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a configuração das configurações de recolha do agente para stdout, stderr e variáveis ambientais a partir de cargas de trabalho de contentores [aqui.](container-insights-agent-config.md)