---
title: Colete & analisar registos de recursos
description: Saiba como enviar registos de recursos e dados de eventos de grupos de contentores em instâncias de contentores Azure para registos do Azure Monitor
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: b39cf31dc28ece7d4e4f938dae21a4fbf6a1a832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566675"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Grupo de contentores e registo de casos com registos do Monitor Azure

Os espaços de trabalho do Log Analytics fornecem uma localização centralizada para armazenar e consultar dados de registo não só a partir de recursos Azure, mas também recursos e recursos no local em outras nuvens. As instâncias do contentor Azure incluem suporte incorporado para envio de registos e dados de eventos para registos do Monitor Azure.

Para enviar dados de registo de grupo de contentores e dados de eventos para registos do Azure Monitor, especifique uma chave de espaço de trabalho e espaço de trabalho do Log Analytics existente ao configurar um grupo de contentores. 

As secções seguintes descrevem como criar um grupo de contentores ativados por registo e como consultar registos. Também pode [atualizar um grupo de contentores](container-instances-update.md) com um ID de espaço de trabalho e uma chave de espaço de trabalho para permitir a exploração madeireira.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Atualmente, só pode enviar dados de eventos de instâncias de contentores Linux para Log Analytics.

## <a name="prerequisites"></a>Pré-requisitos

Para ativar o registo nas instâncias de contentor, precisa do seguinte:

* [Log Analytics espaço de trabalho](../azure-monitor/learn/quick-create-workspace.md)
* [CLI do Azure](/cli/azure/install-azure-cli) (ou [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Obter credenciais do Azure Log Analytics

O Azure Container Instances necessita de permissão para enviar dados para a sua área de trabalho do Log Analytics. Para conceder esta permissão e ativar o registo, tem de fornecer o ID da área de trabalho do Log Analytics e uma das respetivas chaves (primárias ou secundárias), quando criar o grupo de contentores.

Para obter o ID do espaço de trabalho de análise de registo e a chave primária:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Em **Definições**, selecione **Gestão de Agentes**
1. Tome nota de:
   * **ID da Área de Trabalho**
   * **Chave primária**

## <a name="create-container-group"></a>Criar grupo de contentores

Agora que tem o ID do espaço de trabalho de análise de registo e a chave primária, está pronto para criar um grupo de contentores ativado por registo.

Os exemplos a seguir demonstram duas formas de criar um grupo de contentores que consiste num único recipiente [fluente:][fluentd] Azure CLI e Azure CLI com um modelo YAML. O recipiente fluente produz várias linhas de saída na sua configuração padrão. Uma vez que este resultado é enviado para a sua área de trabalho do Log Analytics, funciona bem para demonstrar a visualização e a consulta dos registos.

### <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implementar com a CLI do Azure, especifique os parâmetros `--log-analytics-workspace` e `--log-analytics-workspace-key` no comando [az container create][az-container-create]. Substitua os dois valores de área de trabalho pelos valores que obteve no passo anterior (e atualize o nome de grupo de recursos) antes de executar o seguinte comando.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implementar com o YAML

Utilize este método de preferir implementar grupos de contentores com o YAML. O seguinte YAML define um grupo de contentores com um único contentor. Copie o ficheiro YAML para um novo ficheiro e substitua `LOG_ANALYTICS_WORKSPACE_ID` e `LOG_ANALYTICS_WORKSPACE_KEY` pelos valores que obteve no passo anterior. Guarde o ficheiro como **deploy-aci.yaml**.

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Em seguida, execute o seguinte comando para implantar o grupo de contentores. Substitua `myResourceGroup` por um grupo de recursos na sua subscrição (ou crie primeiro um grupo de recursos chamado "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Deverá receber uma resposta do Azure com detalhes de implementação poucos instantes após a emissão do comando.

## <a name="view-logs"></a>Ver registos

Após implementar o grupo de contentores, pode demorar vários minutos (até 10) para as primeiras entradas do registo aparecerem no portal do Azure. 

Para ver os registos do grupo de contentores na `ContainerInstanceLog_CL` tabela:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Em **Geral**, selecione **Registos**  
1. Digite a seguinte consulta: `ContainerInstanceLog_CL | limit 50`
1. Selecione **Run**

Deverá ver vários resultados apresentados pela consulta. Se no início não vir nenhum resultado, aguarde alguns minutos e, em seguida, selecione o botão **Executar** para executar novamente a consulta. Por predefinição, as entradas de registo são apresentadas no formato **Tabela.** Em seguida, pode expandir uma linha para ver os conteúdos de uma entrada de registo individual.

![Resultados da Pesquisa de registos no portal do Azure][log-search-01]

## <a name="view-events"></a>Ver eventos

Também pode ver eventos para casos de contentores no portal Azure. Os eventos incluem o tempo que o caso é criado e quando é iniciado. Para ver os dados do evento na `ContainerEvent_CL` tabela:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Em **Geral**, selecione **Registos**  
1. Digite a seguinte consulta: `ContainerEvent_CL | limit 50`
1. Selecione **Run**

Deverá ver vários resultados apresentados pela consulta. Se no início não vir nenhum resultado, aguarde alguns minutos e, em seguida, selecione o botão **Executar** para executar novamente a consulta. Por predefinição, as entradas são apresentadas no formato **Tabela.** Em seguida, pode expandir uma linha para ver o conteúdo de uma entrada individual.

![Resultados da Pesquisa de Eventos no portal Azure][log-search-02]

## <a name="query-container-logs"></a>Consultar registos de contentor

Os registos do Azure Monitor incluem uma [extensa linguagem de consulta][query_lang] para extrair informações de potencialmente milhares de linhas de saída de log.

A estrutura básica de uma consulta é a tabela-fonte (neste artigo, `ContainerInstanceLog_CL` ou ) seguida por uma série de `ContainerEvent_CL` operadores separados pelo caractere do tubo ( `|` ). Pode encadear vários operadores para refinar os resultados e executar funções avançadas.

Para ver os resultados da consulta de exemplo, cole a seguinte consulta na caixa de texto de consulta e selecione o botão **Executar** para executar a consulta. Esta consulta apresenta todas as entradas de registo cujo campo "Mensagem" contenha a palavra "aviso":

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Também são suportadas consultas mais complexas. Por exemplo, esta consulta apresenta apenas as entradas de registo para o grupo de contentores "mycontainergroup001" gerado na última hora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Passos seguintes

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Para obter mais informações sobre a consulta de registos e configurar alertas nos registos do Monitor Azure, consulte:

* [Compreender pesquisas de registos em registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Alertas unificados no Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorizar a CPU e a memória do contentor

Para obter informações sobre a monitorização da CPU do Container Instances e recursos de memória, consulte:

* [Monitorizar recursos de contentores no Azure Container Instances ](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
