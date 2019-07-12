---
title: Registo de instância de contentor com os registos do Azure Monitor
description: Saiba como enviar registos de instâncias de contentor do Azure para registos do Azure Monitor.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: cab0bc4d2d0491c70a1d2f11f3a5d5d831ade6cf
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722639"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Registo de instância de contentor com os registos do Azure Monitor

Áreas de trabalho do log Analytics fornecem um local centralizado para armazenar e consultar dados de registo de não apenas recursos do Azure, mas também recursos no local e de recursos noutras Clouds. O Azure Container Instances inclui suporte incorporado para enviar dados para os registos do Azure Monitor.

Para enviar dados de instância de contentor para os registos do Azure Monitor, tem de especificar uma chave de ID e a área de trabalho de área de trabalho do Log Analytics ao criar um grupo de contentor. As secções seguintes descrevem como criar um grupo de contentores com registo ativado e registos de consulta.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para ativar o registo nas instâncias de contentor, precisa do seguinte:

* [Área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [CLI do Azure](/cli/azure/install-azure-cli) (ou [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obter credenciais do Azure Log Analytics

O Azure Container Instances necessita de permissão para enviar dados para a sua área de trabalho do Log Analytics. Para conceder esta permissão e ativar o registo, tem de fornecer o ID da área de trabalho do Log Analytics e uma das respetivas chaves (primárias ou secundárias), quando criar o grupo de contentores.

Para obter o ID de área de trabalho do log analytics e a chave primária:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Sob **configurações**, selecione **definições avançadas**
1. Selecione **Origens ligadas** > **Windows Servers** (ou **Servidores Linux**– o ID e as chaves são os mesmos para os dois)
1. Tome nota de:
   * **ID DA ÁREA DE TRABALHO**
   * **CHAVE PRIMÁRIA**

## <a name="create-container-group"></a>Criar grupo de contentores

Agora que tem o ID de área de trabalho do log analytics e a chave primária, está pronto para criar um grupo de contentores de registo ativado.

Os exemplos seguintes demonstram duas formas de criar um grupo de contentores com um único [fluentd][fluentd] contentor: CLI do Azure e CLI do Azure com um modelo YAML. O contentor Fluentd produz várias linhas de saída na sua configuração predefinida. Uma vez que este resultado é enviado para a sua área de trabalho do Log Analytics, funciona bem para demonstrar a visualização e a consulta dos registos.

### <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implementar com a CLI do Azure, especifique a `--log-analytics-workspace` e `--log-analytics-workspace-key` parâmetros nos [criar o contentor de az][az-container-create] comando. Substitua os dois valores de área de trabalho pelos valores que obteve no passo anterior (e atualize o nome de grupo de recursos) antes de executar o seguinte comando.

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
apiVersion: 2018-10-01
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

Em seguida, execute o seguinte comando para implementar o grupo de contentores. Substitua `myResourceGroup` com um recurso de grupo na sua subscrição (ou crie primeiro um grupo de recursos com o nome "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Deverá receber uma resposta do Azure com detalhes de implementação poucos instantes após a emissão do comando.

## <a name="view-logs-in-azure-monitor-logs"></a>Vista regista nos registos do Azure Monitor

Após implementar o grupo de contentores, pode demorar vários minutos (até 10) para as primeiras entradas do registo aparecerem no portal do Azure. Para ver os registos do grupo de contentores:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Sob **gerais**, selecione **registos**  
1. Escreva a seguinte consulta: `search *`
1. Selecione **executar**

Deverá ver vários resultados apresentados pela consulta `search *`. Se não vir a quaisquer resultados em primeiro lugar, aguarde alguns minutos, em seguida, selecione o **executar** botão para executar a consulta novamente. Por predefinição, as entradas de registo são apresentadas na **tabela** formato. Em seguida, pode expandir uma linha para ver os conteúdos de uma entrada de registo individual.

![Resultados da Pesquisa de registos no portal do Azure][log-search-01]

## <a name="query-container-logs"></a>Consultar registos de contentor

Registos de Monitor do Azure inclui uma ampla [linguagem de consulta][query_lang] para extrair as informações de potencialmente milhares de linhas de saída de registo.

O agente de registo do Azure Container Instances envia entradas para a tabela `ContainerInstanceLog_CL` da sua área de trabalho do Log Analytics. A estrutura básica de uma consulta é a tabela de origem (`ContainerInstanceLog_CL`) seguida de uma série de operadores separados pelo caráter pipe (`|`). Pode encadear vários operadores para refinar os resultados e executar funções avançadas.

Para ver os resultados da consulta de exemplo, cole a consulta seguinte na caixa de texto de consulta (em "Mostrar conversor de linguagem legada)" e selecione o botão **EXECUTAR** para executar a consulta. Esta consulta apresenta todas as entradas de registo cujo campo "Mensagem" contenha a palavra "aviso":

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Também são suportadas consultas mais complexas. Por exemplo, esta consulta apresenta apenas as entradas de registo para o grupo de contentores "mycontainergroup001" gerado na última hora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Passos Seguintes

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Para obter mais informações sobre como consultar registos e configurar alertas nos registos do Azure Monitor, consulte:

* [Compreender a pesquisa nos registos do Azure Monitor](../log-analytics/log-analytics-log-search.md)
* [Alertas unificados no Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Monitorizar a CPU e a memória do contentor

Para obter informações sobre a monitorização da CPU do Container Instances e recursos de memória, consulte:

* [Monitorizar recursos de contentores no Azure Container Instances ](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create