---
title: Políticas de rede Azure Kubernetes | Microsoft Docs
description: Saiba mais sobre as políticas de rede kubernetes para garantir o seu cluster Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a68e1a3f60930e290e97084ff2ec9350b18e2873
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594973"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Visão geral das políticas de rede de Azure Kubernetes

As Políticas de Rede fornecem micro-segmentação para cápsulas, tal como os Grupos de Segurança de Rede (NSGs) fornecem micro-segmentação para VMs. A implementação do Azure Network Policy Manager (também conhecido como Azure NPM) suporta a especificação padrão da Política de Rede Kubernetes. Pode utilizar etiquetas para selecionar um grupo de cápsulas e definir uma lista de regras de entrada e saída para filtrar o tráfego de e para estas cápsulas. Saiba mais sobre as políticas de rede Kubernetes na [documentação kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Visão geral das políticas de rede de Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

A implementação do Azure NPM funciona em conjunto com o Azure CNI que proporciona integração VNet para contentores. A NPM só é apoiada em Linux hoje. A implementação impõe a filtragem do tráfego configurando permitir e negar regras de IP nos IPTables Linux com base nas políticas definidas. Estas regras são agrupadas através de IPSets Linux.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Segurança de planeamento para o seu cluster Kubernetes
Ao implementar a segurança do seu cluster, utilize grupos de segurança de rede (NSGs) para filtrar a entrada de tráfego e deixar a sua sub-rede de cluster (tráfego Norte-Sul). Utilize O Azure NPM para o tráfego entre as cápsulas do seu cluster (tráfego Leste-Oeste).

## <a name="using-azure-npm"></a>Usando Azure NPM
O Azure NPM pode ser utilizado das seguintes formas para fornecer micro-segmentação para cápsulas.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
O NPM está disponível nativamente em AKS e pode ser ativado no momento da criação do cluster. Saiba mais sobre isso no [tráfego seguro entre cápsulas utilizando políticas de rede no Serviço Azure Kubernetes (AKS)](../aks/use-network-policies.md).

### <a name="aks-engine"></a>Motor AKS
AKS-Engine é uma ferramenta que gera um modelo de Gestor de Recursos Azure para a implementação de um cluster Kubernetes em Azure. A configuração do cluster está especificada num ficheiro JSON transmitido à ferramenta ao gerar o modelo. Para saber mais sobre a lista completa de definições de cluster suportadas e as respetivas descrições, veja Motor do Microsoft Azure Container Service – Definição do Cluster.

Para permitir políticas em clusters implantados utilizando o motor acs, especifique o valor da definição de redePolicy no ficheiro de definição de cluster como "azul".

#### <a name="example-configuration"></a>Configuração de exemplo

A configuração de exemplo JSON abaixo cria uma nova rede virtual e sub-rede, e implementa um cluster Kubernetes nele com Azure CNI. Recomendamos que utilize o "Notepad" para editar o ficheiro JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Faça-o por si mesmo (DIY) Aglomerados kubernetes em Azure
 Para os clusters DIY, instale primeiro o plug-in CNI e ative-o em todas as máquinas virtuais de um cluster. Para obter instruções detalhadas, veja [Implementar o plug-in para um cluster do Kubernetes que implementar por conta própria](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Uma vez implantado o cluster, executar o seguinte `kubectl` comando para descarregar e aplicar o *daemon* Azure NPM definido para o cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A solução também é de código aberto e o código está disponível no [repositório de rede de contentores Azure.](https://github.com/Azure/azure-container-networking/tree/master/npm)

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Monitore e visualize configurações de rede com Azure NPM
O Azure NPM inclui métricas prometeu informativas que permitem monitorizar e entender melhor as suas configurações. Fornece visualizações incorporadas no portal Azure ou na Grafana Labs. Pode começar a recolher estas métricas utilizando o Azure Monitor ou o Prometheus Server.

### <a name="benefits-of-azure-npm-metrics"></a>Benefícios das Métricas Azure NPM
Anteriormente, os utilizadores só conseguiam aprender sobre a sua Configuração de Rede com o comando `iptables -L` executado dentro de um nó de cluster, o que produz uma saída verbosa e difícil de entender. As métricas de NPM fornecem os seguintes benefícios relacionados com políticas de rede, regras iptables e IPSets.
- Fornece uma visão da relação entre os três e uma dimensão temporal para depurar uma configuração.
- Número de entradas em todos os IPSets e em cada IPSet.
- Tempo demorado a aplicar uma política com granularidade de nível IPTable/IPSet.
 
### <a name="supported-metrics"></a>Métricas Suportadas
Segue-se a lista de métricas suportadas:

|Nome da Métrica |Description  |Tipo métrico Prometheus  |Etiquetas  |
|---------|---------|---------|---------|
|`npm_num_policies`     |número de políticas de rede          |Medidor         |-         |
|`npm_num_iptables_rules`     | número de regras IPTables     | Medidor        |-         |         
|`npm_num_ipsets`     |número de IPSets         |Medidor            |-         |
|`npm_num_ipset_entries`     |número de entradas de endereços IP em todos os IPSets         |Medidor         |-         |
|`npm_add_policy_exec_time`     |tempo de execução para adicionar uma política de rede         |Resumo         |quântico (0,5, 0,9 ou 0,99)         |
|`npm_add_iptables_rule_exec_time`     |tempo de execução para adicionar uma regra IPTables         |Resumo         |quântico (0,5, 0,9 ou 0,99)         |
|`npm_add_ipset_exec_time`     |tempo de execução para adicionar um IPSet         |Resumo         |quântico (0,5, 0,9 ou 0,99)         |
|`npm_ipset_counts` (avançado)     |número de entradas dentro de cada IPSet individual         |GaugeVec         |nome definido & haxixe         |

Os diferentes níveis quânticos nas métricas "exec_time" ajudam-no a diferenciar entre os cenários gerais e os piores cenários.

Há também uma métrica "exec_time_count" e "exec_time_sum" para cada métrica de resumo "exec_time".

As métricas podem ser raspadas através do Monitor Azure para contentores ou através de Prometeu.

### <a name="setup-for-azure-monitor"></a>Configuração para Monitor Azure
O primeiro passo é ativar o Azure Monitor para contentores para o seu cluster Kubernetes. As etapas podem ser encontradas no [Azure Monitor para recipientes visão geral](../azure-monitor/containers/container-insights-overview.md). Uma vez ativado o Azure Monitor para contentores, configuure o [Monitor Azure para os recipientes ConfigMap](https://aka.ms/container-azm-ms-agentconfig) para permitir a integração e recolha de métricas de NPM de NPM. Monitor azul para contentores ConfigMap tem uma ```integrations``` secção com configurações para recolher métricas de NPM. Estas definições são desativadas por padrão no ConfigMap. Ativando a definição ```collect_basic_metrics = true``` básica, recolherá métricas básicas de NPM. Permitir uma configuração avançada ```collect_advanced_metrics = true``` irá recolher métricas avançadas para além das métricas básicas. 

Depois de editar o ConfigMap, guarde-o localmente e aplique o ConfigMap no seu cluster da seguinte forma.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Abaixo está um corte do [monitor Azure para os contentores ConfigMap,](https://aka.ms/container-azm-ms-agentconfig)que mostra a integração NPM possibilitada com a recolha de métricas avançadas.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
As métricas avançadas são opcionais, e ligá-las automaticamente ligará a recolha de métricas básicas. As métricas avançadas atualmente incluem apenas `npm_ipset_counts`

Saiba mais sobre [o monitor Azure para configurações de recolha de contentores no mapa config](../azure-monitor/containers/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Opções de visualização para monitor Azure
Uma vez ativada a recolha de métricas NPM, pode ver as métricas no portal Azure utilizando o Container Insights ou em Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Visualização no portal Azure em Insights para o cluster
Abre o Portal do Azure. Uma vez no seu cluster's Insights, navegue para "Workbooks" e abra a configuração "Network Policy Manager (NPM)".

Além de visualizar o livro (imagens abaixo), também pode consultar diretamente as métricas prometheus em "Logs" na secção Insights. Por exemplo, esta consulta devolverá todas as métricas recolhidas.
| onde o tempogenerado há > (5h) | onde o nome contém "npm_"

Também pode consultar o Log Analytics diretamente para as métricas. Saiba mais sobre isso com [Começar com Consultas de Log Analytics](../azure-monitor/containers/container-insights-log-search.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Visualização no Painel grafana
Configure o seu Servidor Grafana e configuure uma Fonte de Dados de Análise de Registo, conforme descrito [aqui.](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource) Em seguida, [importe Grafana Dashboard com um backend Log Analytics](https://grafana.com/grafana/dashboards/10956) para os seus Laboratórios Grafana.

O painel tem visuais semelhantes ao Livro Azure. Pode adicionar painéis para traçar & visualizar as métricas de NPM da tabela InsightsMetrics.

### <a name="setup-for-prometheus-server"></a>Configuração para Prometeu Server
Alguns utilizadores podem optar por recolher métricas com um Servidor Prometheus em vez de Azure Monitor para contentores. Só precisa de adicionar dois empregos ao seu raspanete para recolher métricas de NPM.

Para instalar um servidor Prometheus simples, adicione este repo de leme no seu cluster
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
em seguida, adicionar um servidor
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
onde `prometheus-server-scrape-config.yaml` consiste em
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Também pode substituir o `azure-npm-node-metrics` trabalho pelo conteúdo abaixo ou incorporá-lo num trabalho pré-existente para cápsulas Kubernetes:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Opções de Visualização de Prometeu
Ao utilizar um Servidor Prometheus, apenas o Painel Grafana é suportado. 

Se ainda não o fez, configure o seu Servidor Grafana e configuure uma Fonte de Dados Prometheus. Em seguida, importe o nosso [Painel Grafana com um Prometeu backend](https://grafana.com/grafana/dashboards/13000) para os seus Laboratórios Grafana.

Os visuais deste painel são idênticos ao painel de instrumentos com um backend Container Insights/Log Analytics.

### <a name="sample-dashboards"></a>Dashboards de Exemplo
Seguem-se alguns dashboards de amostra para métricas de NPM em Insights de Contentores (CI) e Grafana

#### <a name="ci-summary-counts"></a>Ci Resumo Conta
![Resumo do livro de Azure conta](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>CI Conta ao longo do tempo
[![Livro de Azure conta ao longo do tempo](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>Entradas do CI IPSet
[![Entradas IPSet do Livro Azure](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI Runtime Quantiles
![Azure Workbook runtime quantiles](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Contagem de resumo do painel de Grafana
![Conta o resumo do Painel grafana](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Grafana Dashboard Conta ao longo do tempo
[![Grafana Dashboard conta ao longo do tempo](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Entradas IPSet do painel de grafana
[![Entradas IPSet do Painel de Grafana](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana Dashboard Runtime Quantiles
[![Quantiles de execução do painel de grafana](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Serviço Azure Kubernetes](../aks/intro-kubernetes.md).
-  Saiba mais sobre [a rede de contentores.](container-networking-overview.md)
- [Insiú-lo](deploy-container-networking.md) para os aglomerados kubernetes ou para os recipientes Docker.

