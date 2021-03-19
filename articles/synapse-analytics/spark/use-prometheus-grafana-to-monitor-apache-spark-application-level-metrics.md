---
title: Tutorial - Monitor Apache Spark Application-level metrics with Prometheus and Grafana
description: Tutorial - Aprenda a implementar a solução de métricas de aplicação Apache Spark para um cluster Azure Kubernetes Service (AKS) e aprenda a integrar os dashboards grafana.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 6a0b63dc7fda25e3911ae713a0bea7ae7a0969f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602303"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Tutorial: Monitor Apache Spark Application-level metrics with Prometheus and Grafana

## <a name="overview"></a>Descrição Geral

Neste tutorial, você aprenderá a implementar a solução de métricas de aplicação Apache Spark para um cluster Azure Kubernetes Service (AKS) e aprender a integrar os dashboards grafana.

Pode utilizar esta solução para recolher e consultar os dados das métricas Apache Spark perto de tempo real. Os dashboards integrados de Grafana permitem diagnosticar e monitorizar a sua aplicação Apache Spark. O código-fonte e as configurações foram de origem aberta no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

1.  [CLI do Azure](/cli/azure/install-azure-cli)
2.  [Cliente de leme 3.30+](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Ou use o [Azure Cloud Shell](https://shell.azure.com/), que já inclui o Azure CLI, cliente Helm e kubectl fora da caixa.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Criar uma instância de serviço Azure Kubernetes (AKS)

Utilize o comando Azure CLI para criar um cluster Kubernetes na sua subscrição.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Nota: Este passo pode ser ignorado se já tiver um cluster AKS.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Crie um chefe de serviço e conceda permissão ao espaço de trabalho da Sinapse

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

O resultado deve parecer:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Note o appId, senha e inquilinoID.

[![autorização de concessão de screenshot srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Inicie sessão no seu [espaço de trabalho Azure Synapse Analytics](https://web.azuresynapse.net/) como Administrador da Sinapse

2. No Synapse Studio, no painel do lado esquerdo, **selecione Gerir > controlo de acesso**

3. Clique no botão Adicionar na parte superior esquerda para **adicionar uma atribuição de função**

4. Para âmbito, escolha **espaço de trabalho**

5. Para função, escolha **o Operador de Computação Synapse**

6. Para selecionar o utilizador, insira o seu **<service_principal_name>** e clique no seu principal serviço

7. Clique **em Aplicar** (aguarde 3 minutos para que a permissão entre em vigor.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Instalar conector, servidor Prometheus, tablier Grafana

1. Adicione o repo de gráficos de sinapse ao cliente Helm.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Instalar componentes através do cliente Helm:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: Nome do espaço de trabalho da Sinapse.
 - subscription_id: ID de subscrição do espaço de trabalho sinaapse.
 - workspace_resource_group_name: Nome do grupo de recursos do espaço de trabalho sinaapse.
 - tenant_id: Identificação do inquilino do espaço de trabalho da Sinaapse.
 - service_principal_app_id: O principal serviço "appId"
 - service_principal_password: A senha principal do serviço que criou.

## <a name="log-in-to-grafana"></a>Faça login em Grafana

Obtenha a senha e endereço padrão de Grafana. Pode alterar a palavra-passe nas definições de Grafana.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Obtenha o serviço ip, copie & cole o ip externo para o navegador, e faça login com o nome de utilizador "admin" e a senha.

## <a name="use-grafana-dashboards"></a>Use Grafana Dashboards

Encontre o Painel synapse no canto superior esquerdo da página Grafana (Home -> Synapse Workspace / Synapse Application), tente executar um código de exemplo no Synapse Studio e aguarde alguns segundos para que as métricas se puxassem.

Além disso, pode utilizar os dashboards "Synapse Workspace / Workspace" e "Synapse Workspace / Spark pools" para obter uma visão geral do seu espaço de trabalho e das suas piscinas Apache Spark.

## <a name="uninstall"></a>Desinstalar

Retire os componentes pelo comando Helm da seguinte forma.

```bash
helm delete <release_name> -n <namespace>
```

Apague o cluster AKS.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Introdução de componentes

A Azure Synapse Analytics fornece um [gráfico helm](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) baseado no Operador Prometheus e no Conector Synapse Prometheus. O gráfico Helm inclui servidor Prometheus, servidor Grafana e dashboards Grafana para métricas de nível de aplicação Apache Spark. Você pode usar [Prometheus](https://prometheus.io/), um popular sistema de monitorização de código aberto, para recolher estas métricas em quase tempo real e usar [Grafana](https://github.com/grafana/grafana) para visualização.

### <a name="synapse-prometheus-connector"></a>Conector Synapse Prometheus

O Synapse Prometheus Connector ajuda a ligar a piscina Apache Spark Azure Synapse e o seu servidor Prometheus. Implementa:

1.  Autenticação: É autenticação baseada em AAD e pode atualizar automaticamente o token AAD do principal do serviço para a descoberta de aplicações, ingestão de métricas e outras funções.
2.  Descoberta da aplicação Apache Spark: Quando submete aplicações no espaço de trabalho alvo, o Synapse Prometheus Connector pode descobrir automaticamente estas aplicações.
3.  Metadados de aplicação Apache Spark: Recolhe informações básicas de aplicação e exporta os dados para a Prometheus.

O Synapse Prometheus Connector é lançado como uma imagem de estiva acolhida no [Registo do Contentor da Microsoft](https://github.com/microsoft/containerregistry). É de código aberto e está localizado em [métricas de aplicação Azure Synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Servidor Prometheus

Prometheus é um kit de ferramentas de monitorização e alerta de código de código aberto. Prometheus formou-se na Cloud Native Computing Foundation (CNCF) e tornou-se o padrão de facto para a monitorização nativa da nuvem. A Prometheus pode ajudar-nos a recolher, consultar e armazenar quantidades massivas de dados de séries de tempo, e pode ser facilmente integrado com grafana. Nesta solução, implantamos o componente Prometheus com base no gráfico de leme.

### <a name="grafana-and-dashboards"></a>Grafana e painéis

Grafana é um software de visualização e análise de código aberto. Permite-lhe consultar, visualizar, alertar e explorar as suas métricas. A Azure Synapse Analytics fornece um conjunto de dashboards Grafana padrão para visualizar métricas de nível de aplicação Apache Spark.

O dashboard "Synapse Workspace / Workspace" proporciona uma visão de nível de espaço de trabalho de todas as piscinas Apache Spark, contagem de aplicações, núcleos de cpu, etc.

[![espaço de trabalho do painel de imagens](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

O dashboard "Synapse Workspace / Spark pools" contém as métricas das aplicações Apache Spark em execução na piscina Apache Spark selecionada durante o período de tempo.

[![sparkpool do painel de screenshot](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

O painel "Synapse Workspace / Spark Application" contém a aplicação Apache Spark selecionada.

[![aplicação do painel de screenshot](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

Os modelos acima do painel foram abertos nas [métricas de aplicação Azure Synapse Spark](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).