---
title: Configure Monitor Azure para recolha de dados de agente de contentores Microsoft Docs
description: Este artigo descreve como pode configurar o Monitor Azure para o agente de contentores controlar a recolha de registos de variáveis stdout/stderr e ambiente.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: f21b841bc129012b684d2a1c59eb72989fe9e0e0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890501"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurar a recolha de dados do agente para o Azure Monitor para contentores

O Monitor Azure para contentores recolhe stdout, stderr e variáveis ambientais a partir de cargas de trabalho de contentores implantadas para gerir aglomerados kubernetes do agente contentorizado. Pode configurar as definições de recolha de dados de agentes criando um Kubernetes ConfigMaps personalizado para controlar esta experiência. 

Este artigo demonstra como criar a ConfigMap e configurar a recolha de dados com base nos seus requisitos.

>[!NOTE]
>Para O Azure Red Hat OpenShift, um ficheiro ConfigMap de modelo é criado no espaço *de nomes de registo de azure-azure..* 
>

## <a name="configmap-file-settings-overview"></a>Visão geral das definições de ficheiros ConfigMap

É fornecido um ficheiro ConfigMap de modelo que lhe permite editá-lo facilmente com as suas personalizações sem ter de o criar de raiz. Antes de começar, deve rever a documentação da Kubernetes sobre [o ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e familiarizar-se com a forma de criar, configurar e implementar ConfigMaps. Isto permitir-lhe-á filtrar stderr e stdout por espaço de nome ou em todo o cluster, e variáveis ambientais para qualquer recipiente que percorda por todas as cápsulas/nós do cluster.

>[!IMPORTANT]
>A versão de agente mínimo suportada para recolher variáveis stdout, stderr e ambiental a partir de cargas de trabalho de contentores é ciprod06142019 ou posterior. Para verificar a versão do seu agente, a partir do separador **Nó** selecione um nó e no valor de nota de painel de propriedades da propriedade **De Imagem de Agente.** Para obter informações adicionais sobre as versões do agente e o que está incluído em cada versão, consulte [as notas de lançamento](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)do agente .

### <a name="data-collection-settings"></a>Definições de recolha de dados

A tabela a seguir descreve as definições que pode configurar para controlar a recolha de dados:

| Chave | Tipo de dados | Valor | Descrição |
|--|--|--|--|
| `schema-version` | Corda (sensível a maiôs) | v1 | Esta é a versão de esquema usada pelo agente<br> ao analisar este ConfigMap.<br> A versão de esquema suportada atualmente é v1.<br> Modificar este valor não é suportado e será<br> rejeitado quando configMap é avaliado. |
| `config-version` | String |  | Suporta a capacidade de acompanhar a versão deste ficheiro config no seu sistema/repositório de controlo de origem.<br> Os caracteres máximos permitidos são 10, e todos os outros caracteres são truncados. |
| `[log_collection_settings.stdout] enabled =` | Booleano | true ou false | Isto controla se a recolha do registo do contentor de stdout estiver ativada. Quando `true` definidos e nenhum espaço de nome é excluído para a coleção de registos de stdout<br> `log_collection_settings.stdout.exclude_namespaces`(definição abaixo), os registos de estatido serão recolhidos de todos os recipientes em todas as cápsulas/nós do cluster. Se não for especificado em ConfigMaps,<br> o valor predefinido é `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | String | Matriz separada por vírgula | Array de espaços de nome Kubernetes para os quais os registos de stdout não serão recolhidos. Esta definição só é eficaz se<br> `log_collection_settings.stdout.enabled`<br> está definido para `true` .<br> Se não for especificado no ConfigMap, o valor predefinido é<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Booleano | true ou false | Isto controla se a recolha de registos de contentores stderr estiver ativada.<br> Quando `true` definidos e nenhum espaço de nome é excluído para a coleção de registos de stdout<br> `log_collection_settings.stderr.exclude_namespaces`(regulação), os registos stderr serão recolhidos de todos os recipientes em todas as cápsulas/nós do cluster.<br> Se não for especificado em ConfigMaps, o valor predefinido é<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | String | Matriz separada por vírgula | Array de espaços de nomes Kubernetes para os quais os registos stderr não serão recolhidos.<br> Esta definição só é eficaz se<br> `log_collection_settings.stdout.enabled` está definido para `true` .<br> Se não for especificado no ConfigMap, o valor predefinido é<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Booleano | true ou false | Esta definição controla a coleção variável ambiental<br> em todas as cápsulas/nós no cluster<br> e incumprimentos para `enabled = true` quando não especificado<br> em ConfigMaps.<br> Se a recolha de variáveis ambientais estiver ativada globalmente, pode desativá-la para um recipiente específico<br> definindo a variável ambiental<br> `AZMON_COLLECT_ENV` para **Falso** quer com uma definição de Dockerfile quer no [ficheiro de configuração para o Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) sob o **env:** secção.<br> Se a recolha de variáveis ambientais for globalmente desativada, então não é possível permitir a recolha de um recipiente específico (isto é, a única sobreposição que pode ser aplicada ao nível do contentor é desativar a recolha quando já está ativada globalmente). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Booleano | true ou false | Esta definição controla o enriquecimento de registo de contentores para povoar os valores de propriedade name e Image<br> para cada registo escrito na tabela ContainerLog para todos os registos de contentores do cluster.<br> É padrão quando `enabled = false` não especificado no ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Booleano | true ou false | Esta definição permite a recolha de eventos Kube de todos os tipos.<br> Por predefinição, os eventos Kube com o tipo *Normal* não são recolhidos. Quando esta definição está definida para `true` , os eventos *normais* já não são filtrados e todos os eventos são recolhidos.<br> Por predefinição, isto está definido para `false` . |

### <a name="metric-collection-settings"></a>Configurações de recolha métrica

A tabela a seguir descreve as definições que pode configurar para controlar a recolha métrica:

| Chave | Tipo de dados | Valor | Descrição |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Booleano | true ou false | Esta definição permite que as métricas de utilização persistentes (PV) sejam recolhidas no espaço de nomes do sistema kube. Por padrão, as métricas de utilização para volumes persistentes com alegações de volume persistentes no espaço de nome do sistema kube não são recolhidas. Quando esta definição é definida para `true` , métricas de utilização de PV para todos os espaços de nome são recolhidas. Por predefinição, isto está definido para `false` . |

ConfigMaps é uma lista global e pode haver apenas um ConfigMap aplicado ao agente. Não pode ter outro ConfigMaps a anular as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configurar e implementar ConfigMaps

Execute os seguintes passos para configurar e implementar o seu ficheiro de configuração ConfigMap no seu cluster.

1. Descarregue o [ficheiro ConfigMap YAML](https://aka.ms/container-azm-ms-agentconfig) do modelo e guarde-o como contentor-azm-ms-agentconfig.yaml. 

   > [!NOTE]
   > Este passo não é necessário quando se trabalha com o Azure Red Hat OpenShift porque o modelo ConfigMap já existe no cluster.

2. Edite o ficheiro ConfigMap yaml com as suas personalizações para recolher variáveis stdout, stderr e/ou ambientais. Se estiver a editar o ficheiro ConfigMap yaml para Azure Red Hat OpenShift, primeiro executar o comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` para abrir o ficheiro num editor de texto.

    - Para excluir espaços de nome específicos para a recolha de registos de stdout, configura a chave/valor utilizando o seguinte exemplo: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Para desativar a recolha variável ambiental de um recipiente específico, desinsu pouco a chave/valor `[log_collection_settings.env_var] enabled = true` para permitir a recolha variável globalmente e, em seguida, siga os passos [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para completar a configuração para o recipiente específico.
    
    - Para desativar o aglomerado de recolha de registos stderr em toda a extensão, configura a chave/valor utilizando o seguinte exemplo: `[log_collection_settings.stderr] enabled = false` .

3. Para agrupamentos que não o Azure Red Hat OpenShift, crie o ConfigMap executando o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` em clusters que não o Azure Red Hat OpenShift. 
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Para Azure Red Hat OpenShift, guarde as suas alterações no editor.

A alteração de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas omsagentes do cluster recomeçarão. O recomeço é um recomeço rolante para todas as cápsulas omsagent, nem todas reiniciam ao mesmo tempo. Quando o recomeço estiver terminado, é exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Verificar configuração

Para verificar se a configuração foi aplicada com sucesso a um cluster diferente do Azure Red Hat OpenShift, utilize o seguinte comando para rever os registos de uma cápsula de agente: `kubectl logs omsagent-fdf58 -n kube-system` . Se houver erros de configuração das cápsulas omsagent, a saída mostrará erros semelhantes aos seguintes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Estão também disponíveis erros relacionados com a aplicação de alterações de configuração para revisão. As seguintes opções estão disponíveis para realizar uma resolução adicional de alterações de configuração:

- A partir de registos de cápsulas de agente usando o mesmo `kubectl logs` comando. 

    >[!NOTE]
    >Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
    > 

- De registos ao vivo. Os registos ao vivo mostram erros semelhantes aos seguintes:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- A partir da tabela **KubeMonAgentEvents** no seu espaço de trabalho Log Analytics. Os dados são enviados a cada hora com gravidade *de Erro* para erros de configuração. Se não houver erros, a entrada na tabela terá dados com *informação* de gravidade , que não reporta erros. A propriedade **Tags** contém mais informações sobre o ID do casulo e do contentor no qual ocorreu o erro e também a primeira ocorrência, última ocorrência e contagem na última hora.

- Com o Azure Red Hat OpenShift, verifique os registos omsagent, procurando na tabela **ContainerLog** para verificar se está ativada a recolha de registos de registos de registos de registos de registos de azure-azure-azure..

Depois de corrigir os erros em ConfigMap em clusters que não o Azure Red Hat OpenShift, guarde o ficheiro yaml e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml` . Para Azure Red Hat OpenShift, edite e guarde os ConfigMaps atualizados executando o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Aplicação ConfigMap atualizado

Se já implementou um ConfigMap em clusters que não o Azure Red Hat OpenShift e pretende atualizá-lo com uma configuração mais recente, pode editar o ficheiro ConfigMap que utilizou anteriormente e depois aplicar usando o mesmo comando de antes, `kubectl apply -f <configmap_yaml_file.yaml` . Para Azure Red Hat OpenShift, edite e guarde os ConfigMaps atualizados executando o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A alteração de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas omsagentes do cluster recomeçarão. O recomeço é um recomeço rolante para todas as cápsulas omsagent, nem todas reiniciam ao mesmo tempo. Quando o recomeço estiver terminado, é exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Verificação da versão do esquema

As versões de esquema de configuração suportadas estão disponíveis como anotação de pod (versões de esquema) no pod omsagent. Pode vê-los com o seguinte comando kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

A saída apresentará semelhantes às seguintes com as versões de esquema de anotação:

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

## <a name="next-steps"></a>Passos seguintes

- O Monitor Azure para contentores não inclui um conjunto de alertas predefinidos. Reveja os [alertas de desempenho da Create com o Azure Monitor para que os recipientes](./container-insights-log-alerts.md) aprendam a criar alertas recomendados para alta utilização de CPU e memória para suportar os seus DevOps ou processos e procedimentos operacionais.

- Com a monitorização habilitada a recolher a saúde e a utilização de recursos do seu cluster AKS ou híbrido e cargas de trabalho a funcionar sobre eles, aprenda [a utilizar o](container-insights-analyze.md) Azure Monitor para recipientes.

- Consulte [exemplos de consulta](container-insights-log-search.md#search-logs-to-analyze-data) de registos para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.