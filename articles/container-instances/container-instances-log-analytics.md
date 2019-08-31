---
title: Log de instância de contêiner com logs de Azure Monitor
description: Saiba como enviar logs de instâncias de contêiner do Azure para Azure Monitor logs.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: 9b57775040251312c8afbff5983a52ae9d14e6c6
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172500"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Log de instância de contêiner com logs de Azure Monitor

Os espaços de trabalho Log Analytics fornecem um local centralizado para armazenar e consultar dados de log de recursos não apenas do Azure, mas também recursos e recursos locais em outras nuvens. As instâncias de contêiner do Azure incluem suporte interno para enviar dados a logs de Azure Monitor.

Para enviar dados de instância de contêiner para logs de Azure Monitor, você deve especificar uma ID de espaço de trabalho Log Analytics e uma chave de espaço de trabalho ao criar um grupo de contêineres. As secções seguintes descrevem como criar um grupo de contentores com registo ativado e registos de consulta.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para ativar o registo nas instâncias de contentor, precisa do seguinte:

* [Área de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [CLI do Azure](/cli/azure/install-azure-cli) (ou [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obter credenciais do Azure Log Analytics

O Azure Container Instances necessita de permissão para enviar dados para a sua área de trabalho do Log Analytics. Para conceder esta permissão e ativar o registo, tem de fornecer o ID da área de trabalho do Log Analytics e uma das respetivas chaves (primárias ou secundárias), quando criar o grupo de contentores.

Para obter a ID do espaço de trabalho do log Analytics e a chave primária:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Em **configurações**, selecione **Configurações avançadas**
1. Selecione **Origens ligadas** > **Windows Servers** (ou **Servidores Linux**– o ID e as chaves são os mesmos para os dois)
1. Tome nota de:
   * **ID DA ÁREA DE TRABALHO**
   * **CHAVE PRIMÁRIA**

## <a name="create-container-group"></a>Criar grupo de contentores

Agora que você tem a ID do espaço de trabalho do log Analytics e a chave primária, você está pronto para criar um grupo de contêineres habilitado para registro em log.

Os exemplos a seguir demonstram duas maneiras de criar um grupo de contêineres com um único contêiner [fluente][fluentd] : CLI do Azure e CLI do Azure com um modelo YAML. O contentor Fluentd produz várias linhas de saída na sua configuração predefinida. Uma vez que este resultado é enviado para a sua área de trabalho do Log Analytics, funciona bem para demonstrar a visualização e a consulta dos registos.

### <a name="deploy-with-azure-cli"></a>Implementar com a CLI do Azure

Para implantar com o CLI do Azure, especifique os `--log-analytics-workspace` parâmetros `--log-analytics-workspace-key` e no comando [AZ container Create][az-container-create] . Substitua os dois valores de área de trabalho pelos valores que obteve no passo anterior (e atualize o nome de grupo de recursos) antes de executar o seguinte comando.

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

Em seguida, execute o comando a seguir para implantar o grupo de contêineres. Substitua `myResourceGroup` por um grupo de recursos em sua assinatura (ou primeiro crie um grupo de recursos chamado "MyResource Group"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Deverá receber uma resposta do Azure com detalhes de implementação poucos instantes após a emissão do comando.

## <a name="view-logs-in-azure-monitor-logs"></a>Exibir logs em logs de Azure Monitor

Após implementar o grupo de contentores, pode demorar vários minutos (até 10) para as primeiras entradas do registo aparecerem no portal do Azure. Para exibir os logs do grupo de contêineres:

1. Navegue para a sua área de trabalho do Log Analytics no portal do Azure
1. Em **geral**, selecione **logs**  
1. Digite a seguinte consulta:`search *`
1. Selecione **executar**

Deverá ver vários resultados apresentados pela consulta `search *`. Se, a princípio, você não vir nenhum resultado, aguarde alguns minutos e, em seguida, selecione o botão **executar** para executar a consulta novamente. Por padrão, as entradas de log são exibidas em formato de **tabela** . Em seguida, pode expandir uma linha para ver os conteúdos de uma entrada de registo individual.

![Resultados da Pesquisa de registos no portal do Azure][log-search-01]

## <a name="query-container-logs"></a>Consultar registos de contentor

Os logs de Azure Monitor incluem uma [linguagem de consulta][query_lang] extensiva para extrair informações de potencialmente milhares de linhas de saída de log.

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

## <a name="next-steps"></a>Passos seguintes

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Para obter mais informações sobre como consultar logs e configurar alertas em logs de Azure Monitor, consulte:

* [Noções básicas sobre pesquisas de log em logs de Azure Monitor](../log-analytics/log-analytics-log-search.md)
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