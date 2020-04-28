---
title: Configure Monitor Azure para recipientes Integração Prometheus [ Microsoft Docs
description: Este artigo descreve como pode configurar o Monitor Azure para o agente de contentores para raspar métricas de Prometeu com o seu cluster Kubernetes.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: fcf1a2e5d2cf11cd9d612506e1ec56a392309121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186497"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Configure a raspagem das métricas prometheus com o Monitor Azure para contentores

[Prometheus](https://prometheus.io/) é uma solução popular de monitorização métrica de código aberto e faz parte da [Cloud Native Compute Foundation.](https://www.cncf.io/) O Azure Monitor para contentores proporciona uma experiência de embarque perfeita para recolher as métricas de Prometeu. Normalmente, para usar o Prometeu, é necessário configurar e gerir um servidor Prometheus com uma loja. Ao integrar-se com o Monitor Azure, não é necessário um servidor Prometheus. Basta expor o ponto final das métricas Prometheus através dos seus exportadores ou cápsulas (aplicação), e o agente contentorizado para o Monitor Azure para contentores pode raspar as métricas para si. 

![Arquitetura de monitorização de contentores para Prometeu](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>A versão de agente mínimo suportada para raspar as métricas prometheus é ciprod07092019 ou posterior, e a versão do agente suportada para escrever configuração e erros de `KubeMonAgentEvents` agente na tabela é ciprod10112019. Para o Azure Red Hat OpenShift e red hat OpenShift v4, a versão do agente ciprod04162020 ou superior. 
>
>Para obter mais informações sobre as versões do agente e o que está incluído em cada versão, consulte [as notas](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)de lançamento do agente . 
>Para verificar a versão do seu agente, a partir do separador **Nó** selecione um nó e nas propriedades painele valor da propriedade De imagem tag do **agente.**

A raspagem das métricas de Prometeu é suportada com aglomerados kubernetes hospedados em:

- Serviço de Kubernetes do Azure (AKS)
- Pilha Azure ou no local
- Azure Red Hat OpenShift versão 3.x
- Chapéu Vermelho Azure OpenShift e Red Hat OpenShift versão 4.x

### <a name="prometheus-scraping-settings"></a>Definições de raspagem de Prometeu

A raspagem ativa das métricas de Prometeu é realizada de uma de duas perspetivas:

* Cluster-wide - HTTP URL e descubra alvos a partir de pontos finais listados de um serviço. Por exemplo, serviços k8s como kube-dns e kube-state-metrics, e anotações de pod específicas de uma aplicação. As métricas recolhidas neste contexto serão definidas na secção ConfigMap *[Prometheus data_collection_settings.cluster]*.
* URL HTTP e descubra alvos a partir de pontos finais listados de um serviço. As métricas recolhidas neste contexto serão definidas na secção ConfigMap *[Prometheus_data_collection_settings.nó]*.

| Ponto Final | Âmbito | Exemplo |
|----------|-------|---------|
| Anotação de pod | Em todo o aglomerado | anotações: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Serviço Kubernetes | Em todo o aglomerado | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/ponto final | Por-nó e/ou cluster-wide | `http://myurl:9101/metrics` |

Quando um URL é especificado, o Monitor Azure para recipientes apenas raspa o ponto final. Quando o serviço Kubernetes é especificado, o nome do serviço é resolvido com o servidor DNS do cluster para obter o endereço IP e, em seguida, o serviço resolvido é raspado.

|Âmbito | Chave | Tipo de dados | Valor | Descrição |
|------|-----|-----------|-------|-------------|
| Em todo o aglomerado | | | | Especifique qualquer um dos três métodos seguintes para raspar pontos finais para métricas. |
| | `urls` | String | Matriz separada de vírina | Ponto final http (endereço IP ou endereço URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE_IP é um monitor azure específico para parâmetros de contentores e pode ser utilizado em vez de endereço IP do nó. Deve ser toda maiúscula.) |
| | `kubernetes_services` | String | Matriz separada de vírina | Uma série de serviços kubernetes para raspar métricas de kube-state-metrics. Por exemplo,`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Booleano | true ou false | Quando definido `true` nas configurações em todo o cluster, o Monitor Azure para o agente de contentores irá raspar cápsulas Kubernetes em todo o cluster para as seguintes anotações Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleano | true ou false | Permite raspar a cápsula. `monitor_kubernetes_pods`deve ser `true`definido para . |
| | `prometheus.io/scheme` | String | http ou https | Incumprimentos para raspar http. Se necessário, `https`definido para . | 
| | `prometheus.io/path` | String | Matriz separada de vírina | O caminho dos recursos HTTP para recolher métricas. Se o caminho das `/metrics`métricas não for, defina-o com esta anotação. |
| | `prometheus.io/port` | String | 9102 | Especifique uma porta para raspar. Se a porta não estiver definida, será deprecinada para 9102. |
| | `monitor_kubernetes_pods_namespaces` | String | Matriz separada de vírina | Uma lista de espaços de nome para raspar métricas de cápsulas Kubernetes.<br> Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Node-to-wide | `urls` | String | Matriz separada de vírina | Ponto final http (endereço IP ou endereço URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE_IP é um monitor azure específico para parâmetros de contentores e pode ser utilizado em vez de endereço IP do nó. Deve ser toda maiúscula.) |
| Node-wide ou Cluster-wide | `interval` | String | Anos 60 | O intervalo de recolha é de um minuto (60 segundos). Pode modificar a coleção para o *[prometheus_data_collection_settings.nó]* e/ou *[prometheus_data_collection_settings.cluster]* para unidades temporais como s, m, h. |
| Node-wide ou Cluster-wide | `fieldpass`<br> `fielddrop`| String | Matriz separada de vírina | Pode especificar certas métricas a serem recolhidas ou não`fieldpass`a partir do`fielddrop`ponto final, definindo a listagem de permitir ( e não permitir ). Tem de definir a lista de autorizações primeiro. |

ConfigMaps é uma lista global e só pode haver um ConfigMap aplicado ao agente. Não pode ter outro ConfigMaps a anular as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configure e implemente configMaps

Execute os seguintes passos para configurar o ficheiro de configuração Do ConfigMap para os seguintes clusters:

* Serviço de Kubernetes do Azure (AKS)
* Pilha Azure ou no local
* Azure Red Hat OpenShift versão 4.x e Red Hat OpenShift versão 4.x

1. [Descarregue](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o ficheiro de yaml do modelo ConfigMap e guarde-o como contentor-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Este passo não é necessário quando se trabalha com o Azure Red Hat OpenShift uma vez que o modelo ConfigMap já existe no cluster.

2. Edite o ficheiro de yaml ConfigMap com as suas personalizações para raspar as métricas prometheus.

    >[!NOTE]
    >Se estiver a editar o ficheiro de yaml ConfigMap para O `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Hat OpenShift azure Red Hat, primeiro executa o comando para abrir o ficheiro num editor de texto.

    >[!NOTE]
    >A seguinte anotação `openshift.io/reconcile-protect: "true"` deve ser adicionada sob os metadados do *contentor-azm-ms-agentconfig* ConfigMap para evitar a reconciliação. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Para recolher os serviços kubernetes em todo o cluster, configure o ficheiro ConfigMap utilizando o exemplo seguinte.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Para configurar a raspagem das métricas prometheus a partir de um URL específico através do cluster, configure o ficheiro ConfigMap utilizando o exemplo seguinte.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Para configurar a raspagem das métricas prometheus a partir do DaemonSet de um agente para cada nó individual do cluster, configure o seguinte no ConfigMap:
    
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
        >$NODE_IP é um Monitor Azure específico para parâmetros de contentores e pode ser utilizado em vez de endereço IP do nó. Deve ser tudo maiúsculo. 

    - Para configurar a raspagem das métricas de Prometeu especificando uma anotação de pod, execute os seguintes passos:

       1. No ConfigMap, especifique o seguinte:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Especifique a seguinte configuração para anotações de pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se pretender restringir a monitorização a espaços de nome específicos para cápsulas que tenham anotações, `monitor_kubernetes_pod` por `true` exemplo, apenas inclua cápsulas dedicadas para cargas de trabalho de produção, detetete o para no ConfigMap e adicione o filtro `monitor_kubernetes_pods_namespaces` espaço de nome especificando os espaços de nome para raspar. Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Executar o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

A mudança de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas de omsagent no cluster recomeçam. O reinício é um reinício rolante para todas as cápsulas de omsagent, nem todas reiniciam ao mesmo tempo. Quando os reinícios estiverem terminados, é apresentada uma mensagem semelhante `configmap "container-azm-ms-agentconfig" created`à seguinte e inclui o resultado: .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>Configure e implemente ConfigMaps - Chapéu Vermelho Azure OpenShift v3

Esta secção inclui os requisitos e passos para configurar com sucesso o seu ficheiro de configuração ConfigMap para o cluster Azure Red Hat OpenShift v3.x.

>[!NOTE]
>Para o Azure Red Hat OpenShift v3.x, um ficheiro Modelo ConfigMap é criado no espaço de nomes *openshift-azure-logging.* Não está configurado para raspar ativamente métricas ou recolha de dados do agente.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que é membro do papel de Administrador do Cluster de Clientes do seu cluster Azure Red Hat OpenShift para configurar o agente contentorizado e as definições de raspagem prometheus. Para verificar se é membro do grupo *osa-cliente-administradores,* execute o seguinte comando:

``` bash
  oc get groups
```

A saída assemelhar-se-á ao seguinte:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Se é membro do grupo *osa-cliente-administradores,* deverá ser `container-azm-ms-agentconfig` capaz de listar o ConfigMap utilizando o seguinte comando:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A saída assemelhar-se-á ao seguinte:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>Ativar monitorização

Execute os seguintes passos para configurar o ficheiro de configuração ConfigMap para o seu cluster OpenShift v3.x do chapéu vermelho Azure.

1. Edite o ficheiro de yaml ConfigMap com as suas personalizações para raspar as métricas prometheus. O modelo ConfigMap já existe no cluster V3 Do Chapéu Vermelho. Execute `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` o comando para abrir o ficheiro num editor de texto.

    >[!NOTE]
    >A seguinte anotação `openshift.io/reconcile-protect: "true"` deve ser adicionada sob os metadados do *contentor-azm-ms-agentconfig* ConfigMap para evitar a reconciliação. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Para recolher os serviços kubernetes em todo o cluster, configure o ficheiro ConfigMap utilizando o exemplo seguinte.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Para configurar a raspagem das métricas prometheus a partir de um URL específico através do cluster, configure o ficheiro ConfigMap utilizando o exemplo seguinte.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Para configurar a raspagem das métricas prometheus a partir do DaemonSet de um agente para cada nó individual do cluster, configure o seguinte no ConfigMap:
    
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
        >$NODE_IP é um Monitor Azure específico para parâmetros de contentores e pode ser utilizado em vez de endereço IP do nó. Deve ser tudo maiúsculo. 

    - Para configurar a raspagem das métricas de Prometeu especificando uma anotação de pod, execute os seguintes passos:

       1. No ConfigMap, especifique o seguinte:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Especifique a seguinte configuração para anotações de pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se pretender restringir a monitorização a espaços de nome específicos para cápsulas que tenham anotações, `monitor_kubernetes_pod` por `true` exemplo, apenas inclua cápsulas dedicadas para cargas de trabalho de produção, detetete o para no ConfigMap e adicione o filtro `monitor_kubernetes_pods_namespaces` espaço de nome especificando os espaços de nome para raspar. Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Guarde as suas mudanças no editor.

A mudança de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas de omsagent no cluster recomeçam. O reinício é um reinício rolante para todas as cápsulas de omsagent, nem todas reiniciam ao mesmo tempo. Quando os reinícios estiverem terminados, é apresentada uma mensagem semelhante `configmap "container-azm-ms-agentconfig" created`à seguinte e inclui o resultado: .

Pode visualizar o ConfigMap atualizado `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`executando o comando, . 

## <a name="applying-updated-configmap"></a>Aplicação do ConfigMap atualizado

Se já implementou um ConfigMap para o seu cluster e pretende atualizá-lo com uma configuração mais recente, pode editar o ficheiro ConfigMap que já utilizou anteriormente e depois aplicar usando os mesmos comandos que antes.

Para os seguintes ambientes Kubernetes:

- Serviço de Kubernetes do Azure (AKS)
- Pilha Azure ou no local
- Chapéu Vermelho Azure OpenShift e Red Hat OpenShift versão 4.x

executar o `kubectl apply -f <configmap_yaml_file.yaml`comando . 

Para um cluster Azure Red Hat OpenShift v3.x, execute o comando, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o ficheiro no seu editor predefinido para modificar e, em seguida, guardá-lo.

A mudança de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas de omsagent no cluster recomeçam. O reinício é um reinício rolante para todas as cápsulas de omsagent, nem todas reiniciam ao mesmo tempo. Quando os reinícios estiverem terminados, é apresentada uma mensagem semelhante `configmap "container-azm-ms-agentconfig" updated`à seguinte e inclui o resultado: .

## <a name="verify-configuration"></a>Verificar a configuração

Para verificar se a configuração foi aplicada com sucesso a um cluster, `kubectl logs omsagent-fdf58 -n=kube-system`utilize o seguinte comando para rever os registos de uma cápsula de agente: . 

>[!NOTE]
>Este comando não é aplicável ao cluster Azure Red Hat OpenShift v3.x.
> 

Se houver erros de configuração das cápsulas omsagent, a saída mostrará erros semelhantes aos seguintes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Erros relacionados com a aplicação de alterações de configuração também estão disponíveis para revisão. As seguintes opções estão disponíveis para realizar uma resolução adicional de problemas de alterações de configuração e raspagem das métricas prometheus:

- A partir de um registo `kubectl logs` de cápsula de agente usando o mesmo comando 
    >[!NOTE]
    >Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
    > 

- A partir de Dados Ao Vivo (pré-visualização). Os registos de Dados Ao Vivo (pré-visualização) mostram erros semelhantes aos seguintes:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Da tabela **KubeMonAgentEvents** no seu espaço de trabalho Log Analytics. Os dados são enviados a cada hora com a gravidade do *aviso* para erros de raspagem e gravidade de *erro* para erros de configuração. Caso não haja erros, a entrada na tabela terá dados com severity *Info*, que não reporta erros. A propriedade **Tags** contém mais informações sobre o id do casulo e do recipiente em que ocorreu o erro e também a primeira ocorrência, última ocorrência, e contagem na última hora.

- Para o Azure Red Hat OpenShift v3.x e v4.x, verifique os registos do omsagent, procurando na tabela **ContainerLog** para verificar se a recolha de registos de exploração madeireira openshift-azure está ativada.

Os erros impedem que o agente omsagent analise o ficheiro, fazendo com que ele reinicie e utilize a configuração predefinida. Depois de corrigir o erro(s) no ConfigMap em clusters diferentes do Azure Red Hat OpenShift v3.x, guarde o ficheiro yaml e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml`. 

Para o Azure Red Hat OpenShift v3.x, edite e `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`guarde os ConfigMaps atualizados executando o comando: .

## <a name="query-prometheus-metrics-data"></a>Dados das métricas de Prometheus

Para ver as métricas prometheus raspadas pelo Monitor Azure e quaisquer erros de configuração/raspagem relatados pelo agente, reveja os dados das [métricas de Query Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) e [a Config ou erros](container-insights-log-search.md#query-config-or-scraping-errors)de raspagem .

## <a name="view-prometheus-metrics-in-grafana"></a>Ver métricas de Prometeu em Grafana

O Monitor Azure para contentores suporta métricas de visualização armazenadas no seu espaço de trabalho Log Analytics nos dashboards Grafana. Fornecemos um modelo que você pode baixar do [repositório](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) de dashboard da Grafana para começar e referência para ajudá-lo a aprender a consultar dados adicionais dos seus clusters monitorizados para visualizar em dashboards grafana personalizados. 

## <a name="review-prometheus-data-usage"></a>Rever o uso de dados de Prometheus

Para identificar o volume de ingestão de cada tamanho de cada métrica em GB por dia para entender se é alto, é fornecida a seguinte consulta.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A saída apresentará resultados semelhantes aos seguintes:

![Resultados da consulta de registo do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Para estimar o tamanho de cada métrica em GB durante um mês para entender se o volume de dados ingeridos no espaço de trabalho é elevado, a seguinte consulta é fornecida.

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

Mais informações sobre como monitorizar a utilização de dados e analisar os custos estão disponíveis na [Gestão de utilização e custos com registos do Monitor Azure](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a configuração das definições de recolha de agentes para variáveis de stdout, stderr e ambientais a partir de cargas de trabalho de contentores [aqui](container-insights-agent-config.md). 
