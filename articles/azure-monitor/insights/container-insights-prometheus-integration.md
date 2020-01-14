---
title: Configurar Azure Monitor para a integração de Prometheus de contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o Azure Monitor para o agente de contêineres para recorte de métricas do Prometheus com o cluster kubernetes.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: b774bf042778ca9118a7bc9f051655b200d87659
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931429"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Configurar a recorte de métricas de Prometheus com Azure Monitor para contêineres

[Prometheus](https://prometheus.io/) é uma popular solução de monitoramento de métrica de software livre e faz parte da [base de computação nativa na nuvem](https://www.cncf.io/). Azure Monitor para contêineres fornece uma experiência de integração direta para coletar métricas de Prometheus. Normalmente, para usar o Prometheus, você precisa configurar e gerenciar um servidor Prometheus com um repositório. Ao integrar com o Azure Monitor, um servidor Prometheus não é necessário. Você só precisa expor o ponto de extremidade de métricas Prometheus por meio de exportadores ou pods (aplicativo), e o agente em contêiner para Azure Monitor para contêineres pode recorrer às métricas para você. 

![Arquitetura de monitoramento de contêiner para Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>A versão mínima do agente com suporte para métricas de Prometheus de sucata é ciprod07092019 ou posterior, e a versão do agente com suporte para gravar erros de configuração e de agente na tabela de `KubeMonAgentEvents` é ciprod10112019. Para obter mais informações sobre as versões do agente e o que está incluído em cada versão, consulte [notas de versão do agente](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Para verificar a versão do agente, na guia **nó** , selecione um nó e, no painel Propriedades, observe o valor da propriedade **marca da imagem do agente** .

Há suporte para a recorte de métricas Prometheus com clusters kubernetes hospedados em:

- Serviço Kubernetes do Azure (AKS)
- Azure Container Instances
- Azure Stack ou local
- Azure Red Hat OpenShift

>[!NOTE]
>Para o Azure Red Hat OpenShift, um arquivo de modelo ConfigMap é criado no namespace *OpenShift-Azure-Logging* . Ele não está configurado para recorte ativamente as métricas ou a coleta de dados do agente.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Pré-requisitos do Azure Red Hat OpenShift

Antes de começar, confirme que você é membro da função de administrador de cluster do cliente do seu cluster do Azure Red Hat OpenShift para configurar as configurações de recorte do agente e do Prometheus em contêineres. Para verificar se você é membro do grupo *OSA-Customer-admins* , execute o seguinte comando:

``` bash
  oc get groups
```

A saída terá a seguinte aparência:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Se você for membro do grupo *OSA-Customer-admins* , deverá ser capaz de listar o `container-azm-ms-agentconfig` ConfigMap usando o seguinte comando:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A saída terá a seguinte aparência:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Configurações de recorte de Prometheus

A recorte ativa de métricas de Prometheus é executada de uma das duas perspectivas:

* URL de todo o cluster-HTTP e descobrir destinos de pontos de extremidade listados de um serviço. Por exemplo, serviços k8ss, como Kube-DNS e Kube-State-métricas, e anotações de Pod específicas para um aplicativo. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus data_collection_settings. cluster]* .
* URL em todo o nó-HTTP e descobrir destinos de pontos de extremidade listados de um serviço. As métricas coletadas neste contexto serão definidas na seção ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Ponto Final | Âmbito | Exemplo |
|----------|-------|---------|
| Anotação de Pod | Em todo o cluster | anotações <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Serviço kubernetes | Em todo o cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/ponto de extremidade | Por nó e/ou em todo o cluster | `http://myurl:9101/metrics` |

Quando uma URL é especificada, Azure Monitor para contêineres apenas captura o ponto de extremidade. Quando o serviço kubernetes é especificado, o nome do serviço é resolvido com o servidor DNS do cluster para obter o endereço IP e, em seguida, o serviço resolvido é recapturado.

|Âmbito | Chave | Data type | Valor | Descrição |
|------|-----|-----------|-------|-------------|
| Em todo o cluster | | | | Especifique qualquer um dos três métodos a seguir para recorte de pontos de extremidade para métricas. |
| | `urls` | String | Matriz separada por vírgulas | Ponto de extremidade HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE _IP é um Azure Monitor específico para o parâmetro de contêineres e pode ser usado em vez do endereço IP do nó. Deve estar tudo em maiúsculas.) |
| | `kubernetes_services` | String | Matriz separada por vírgulas | Uma matriz de serviços Kubernetess para recorte de métricas de métricas de Kube-State. Por exemplo,`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Booleano | true ou false | Quando definido como `true` nas configurações de todo o cluster, Azure Monitor para o agente de contêineres irá recriar o pods kubernetes em todo o cluster para as seguintes anotações Prometheus:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleano | true ou false | Habilita a recorte do pod. `monitor_kubernetes_pods` deve ser definido como `true`. |
| | `prometheus.io/scheme` | String | http ou https | O padrão é a sucateação sobre HTTP. Se necessário, defina como `https`. | 
| | `prometheus.io/path` | String | Matriz separada por vírgulas | O caminho do recurso HTTP para o qual buscar métricas. Se o caminho de métrica não for `/metrics`, defina-o com esta anotação. |
| | `prometheus.io/port` | String | 9102 | Especifique uma porta da qual a sucata será recortada. Se a porta não estiver definida, o padrão será 9102. |
| | `monitor_kubernetes_pods_namespaces` | String | Matriz separada por vírgulas | Uma lista de permissões de namespaces para recorte de métricas de kubernetes pods.<br> Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Em todo o nó | `urls` | String | Matriz separada por vírgulas | Ponto de extremidade HTTP (endereço IP ou caminho de URL válido especificado). Por exemplo: `urls=[$NODE_IP/metrics]`. ($NODE _IP é um Azure Monitor específico para o parâmetro de contêineres e pode ser usado em vez do endereço IP do nó. Deve estar tudo em maiúsculas.) |
| Em todo o nó ou cluster | `interval` | String | 60 s | O padrão de intervalo de coleta é de um minuto (60 segundos). Você pode modificar a coleção para *[prometheus_data_collection_settings. Node]* e/ou *[prometheus_data_collection_settings. cluster]* para unidades de tempo como s, m, h. |
| Em todo o nó ou cluster | `fieldpass`<br> `fielddrop`| String | Matriz separada por vírgulas | Você pode especificar determinadas métricas a serem coletadas ou não no ponto de extremidade, definindo a listagem permitir (`fieldpass`) e não permitir (`fielddrop`). Você deve definir a lista de permissões primeiro. |

ConfigMaps é uma lista global e pode haver apenas um ConfigMap aplicado ao agente. Você não pode ter outro ConfigMaps que se refaça com as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configurar e implantar ConfigMaps

Execute as etapas a seguir para configurar o arquivo de configuração do ConfigMap para clusters kubernetes.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o arquivo do modelo ConfigMap YAML e salve-o como contêiner-AZM-MS-agentconfig. YAML.

   >[!NOTE]
   >Esta etapa não é necessária ao trabalhar com o Azure Red Hat OpenShift, pois o modelo ConfigMap já existe no cluster.

2. Edite o arquivo ConfigMap YAML com suas personalizações para recorte de métricas de Prometheus. Se você estiver editando o arquivo ConfigMap YAML para o Red Hat OpenShift do Azure, primeiro execute o comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o arquivo em um editor de texto.

    >[!NOTE]
    >O `openshift.io/reconcile-protect: "true"` de anotação a seguir deve ser adicionado nos metadados de *contêiner-AZM-MS-agentconfig* ConfigMap para impedir a reconciliação. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Para coletar os serviços Kubernetess em todo o cluster, configure o arquivo ConfigMap usando o exemplo a seguir.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Para configurar a recorte de métricas de Prometheus de uma URL específica pelo cluster, configure o arquivo ConfigMap usando o exemplo a seguir.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Para configurar a recorte de métricas de Prometheus do Daemonset de um agente para cada nó individual no cluster, configure o seguinte no ConfigMap:
    
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
        >$NODE _IP é um Azure Monitor específico para o parâmetro containers e pode ser usado em vez do endereço IP do nó. Ele deve estar em letras maiúsculas. 

    - Para configurar a recorte de métricas de Prometheus especificando uma anotação de Pod, execute as seguintes etapas:

       1. No ConfigMap, especifique o seguinte:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Especifique a seguinte configuração para anotações de Pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se você quiser restringir o monitoramento a namespaces específicos para os pods que têm anotações, por exemplo, inclua apenas pods dedicados para cargas de trabalho de produção, defina o `monitor_kubernetes_pod` como `true` em ConfigMap e adicione o filtro de namespace `monitor_kubernetes_pods_namespaces` especificando os namespaces dos quais fazer a sucata. Por exemplo, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Para clusters diferentes do Azure Red Hat OpenShift, execute o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Para o Azure Red Hat OpenShift, salve suas alterações no editor.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created`.

Você pode exibir o ConfigMap atualizado para o Azure Red Hat OpenShift executando o comando `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`. 

## <a name="applying-updated-configmap"></a>Aplicando ConfigMap atualizado

Se você já tiver implantado um ConfigMap em seu cluster e quiser atualizá-lo com uma configuração mais recente, poderá editar o arquivo ConfigMap que você usou anteriormente e, em seguida, aplicar usando os mesmos comandos que antes.

Para clusters kubernetes diferentes do Azure Red Hat OpenShift, execute o comando `kubectl apply -f <configmap_yaml_file.yaml`. 

Para o cluster do Azure Red Hat OpenShift, execute o comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o arquivo no editor padrão para modificá-lo e salvá-lo.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Verificar configuração

Para verificar se a configuração foi aplicada com êxito a um cluster, use o seguinte comando para examinar os logs de um pod do agente: `kubectl logs omsagent-fdf58 -n=kube-system`. 

>[!NOTE]
>Este comando não é aplicável ao cluster do Azure Red Hat OpenShift.
> 

Se houver erros de configuração do pods omsagent, a saída mostrará erros semelhantes ao seguinte:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Os erros relacionados à aplicação de alterações de configuração também estão disponíveis para revisão. As opções a seguir estão disponíveis para executar a solução de problemas adicional de alterações de configuração e a recorte de métricas de Prometheus:

- De logs de pod de agente usando o mesmo comando `kubectl logs` 
    >[!NOTE]
    >Este comando não é aplicável ao cluster do Azure Red Hat OpenShift.
    > 

- De dados dinâmicos (versão prévia). Os logs de dados dinâmicos (versão prévia) mostram erros semelhantes ao seguinte:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Na tabela **KubeMonAgentEvents** em seu espaço de trabalho log Analytics. Os dados são enviados a cada hora com severidade de *aviso* para erros de recorte e severidade de *erro* para erros de configuração. Se não houver erros, a entrada na tabela terá dados com *informações*de severidade, que não relatam erros. A propriedade **Tags** contém mais informações sobre o pod e a ID do contêiner em que o erro ocorreu e também a primeira ocorrência, última ocorrência e contagem na última hora.

- Para o Azure Red Hat OpenShift, verifique os logs do omsagent pesquisando a tabela **ContainerLog** para verificar se a coleta de log de OpenShift-Azure-Logging está habilitada.

Os erros impedem que o omsagent analise o arquivo, fazendo com que ele seja reiniciado e use a configuração padrão. Depois de corrigir os erros no ConfigMap em clusters diferentes do Azure Red Hat OpenShift, salve o arquivo YAML e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml`. 

Para o Azure Red Hat OpenShift, edite e salve o ConfigMaps atualizado executando o comando: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`.

## <a name="query-prometheus-metrics-data"></a>Consultar dados de métricas do Prometheus

Para exibir as métricas de Prometheus recortadas por Azure Monitor e quaisquer erros de configuração/sucata relatados pelo agente, examine os [dados de métrica de Prometheus de consulta](container-insights-log-search.md#query-prometheus-metrics-data) e os erros de configuração de [consulta ou de recorte](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Exibir métricas de Prometheus no Grafana

Azure Monitor para contêineres dá suporte à exibição de métricas armazenadas em seu espaço de trabalho Log Analytics em painéis do Grafana. Fornecemos um modelo que você pode baixar do [repositório do painel](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) do Grafana para começar e fazer referência para ajudá-lo a aprender a consultar dados adicionais de seus clusters monitorados para visualizar em painéis de Grafana personalizados. 

## <a name="review-prometheus-data-usage"></a>Examinar o uso de dados do Prometheus

Para identificar o volume de ingestão de cada tamanho de métrica em GB por dia para entender se ele é alto, a consulta a seguir é fornecida.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
A saída mostrará resultados semelhantes ao seguinte:

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![Resultados da consulta de log do volume de ingestão de dados](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Mais informações sobre como monitorar o uso de dados e analisar o custo estão disponíveis em [gerenciar o uso e os custos com os logs de Azure monitor](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como definir as configurações de coleta de agente para as variáveis de stdout, stderr e ambientais de cargas de trabalho de contêineres [aqui](container-insights-agent-config.md). 
