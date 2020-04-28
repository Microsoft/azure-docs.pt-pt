---
title: Configure Azure Monitor para recolha de dados de agentes de contentores Microsoft Docs
description: Este artigo descreve como pode configurar o Monitor Azure para o agente de contentores para controlar a recolha de log stdout/stderr e variáveis ambientais.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75933026"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configure recolha de dados de agente para o Monitor Azure para contentores

O Monitor Azure para contentores recolhe variáveis de stdout, stderr e ambientais de cargas de trabalho de contentores implantadas para gerir os aglomerados kubernetes do agente contentorizado. Pode configurar as definições de recolha de dados do agente criando um Kubernetes ConfigMaps personalizado para controlar esta experiência. 

Este artigo demonstra como criar o ConfigMap e configurar a recolha de dados com base nos seus requisitos.

>[!NOTE]
>Para o Azure Red Hat OpenShift, um ficheiro De modelo ConfigMap é criado no espaço de nomes *openshift-azure-logging.* 
>

## <a name="configmap-file-settings-overview"></a>Visão geral das definições de ficheiro sintetidade do ConfigMap

É fornecido um ficheiro De modelo ConfigMap que lhe permite editá-lo facilmente com as suas personalizações sem ter de o criar de raiz. Antes de começar, deve rever a documentação da Kubernetes sobre o [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e familiarizar-se com a forma de criar, configurar e implementar o ConfigMaps. Isto permitirá filtrar stderr e stdout por espaço de nome ou em todo o cluster, e variáveis ambientais para qualquer recipiente que atravesse todas as cápsulas/nós do cluster.

>[!IMPORTANT]
>A versão de agente mínimo suportada para recolher variáveis de stdout, stderr e ambientais de cargas de trabalho de contentores é ciprod06142019 ou posterior. Para verificar a versão do seu agente, a partir do separador **Nó** selecione um nó e nas propriedades painele valor da propriedade De imagem tag do **agente.** Para obter informações adicionais sobre as versões do agente e o que está incluído em cada versão, consulte [as notas](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)de lançamento do agente .

### <a name="data-collection-settings"></a>Definições de recolha de dados

Seguem-se as definições que podem ser configuradas para controlar a recolha de dados.

|Chave |Tipo de dados |Valor |Descrição |
|----|----------|------|------------|
|`schema-version` |Corda (sensível ao caso) |v1 |Esta é a versão schema usada pelo agente ao analisar este ConfigMap. Atualmente suportada a versão de esquema é v1. A modificação deste valor não é suportada e será rejeitada quando o ConfigMap for avaliado.|
|`config-version` |String | | Suporta a capacidade de acompanhar a versão deste ficheiro config no seu sistema/repositório de controlo de origem. Os caracteres máximos permitidos são 10, e todos os outros caracteres são truncados. |
|`[log_collection_settings.stdout] enabled =` |Booleano | true ou false | Isto controla se a recolha de registos de contentores stdout estiver ativada. Quando `true` definidos e não forem excluídos espaços de`log_collection_settings.stdout.exclude_namespaces` nome para recolha de troncos stdout (definição abaixo), os troncos stdout serão recolhidos de todos os recipientes em todas as cápsulas/nós do cluster. Se não especificado no ConfigMaps, `enabled = true`o valor predefinido é . |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Matriz separada de vírina |Conjunto de espaços de nome Kubernetes para os quais os troncos stdout não serão recolhidos. Esta definição só `log_collection_settings.stdout.enabled` é `true`eficaz se estiver definida para . Se não especificado no ConfigMap, `exclude_namespaces = ["kube-system"]`o valor predefinido é .|
|`[log_collection_settings.stderr] enabled =` |Booleano | true ou false |Isto controla se a recolha de registos de contentores stderr estiver ativada. Quando `true` definidos e não forem excluídos espaços de`log_collection_settings.stderr.exclude_namespaces` nome para recolha de troncos stdout (definição), os troncos de estarr serão recolhidos de todos os recipientes em todas as cápsulas/nós do cluster. Se não especificado no ConfigMaps, `enabled = true`o valor predefinido é . |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Matriz separada de vírina |Conjunto de espaços de nome Kubernetes para os quais os troncos de stderr não serão recolhidos. Esta definição só `log_collection_settings.stdout.enabled` é `true`eficaz se estiver definida para . Se não especificado no ConfigMap, `exclude_namespaces = ["kube-system"]`o valor predefinido é . |
| `[log_collection_settings.env_var] enabled =` |Booleano | true ou false | Esta definição controla a recolha de variáveis ambientais em `enabled = true` todas as cápsulas/nós do cluster e predefinidos para quando não especificadas no ConfigMaps. Se a recolha de variáveis ambientais estiver globalmente ativada, pode `AZMON_COLLECT_ENV` desativá-la para um recipiente específico, definindo a variável ambiental para **False,** quer com uma definição de Dockerfile, quer no ficheiro de [configuração do Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) sob o **env:** secção. Se a recolha de variáveis ambientais for globalmente desativada, então não é possível permitir a recolha de um recipiente específico (isto é, a única sobreposição que pode ser aplicada ao nível do contentor é desativar a recolha quando já está ativada globalmente).). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Booleano | true ou false | Esta definição controla o enriquecimento de registo de contentores para povoar os valores de propriedade de Nome e Imagem para cada registo de registo escrito na tabela ContainerLog para todos os troncos de contentores do cluster. Não se `enabled = false` aplica ao quando não especificado no ConfigMap. |

ConfigMaps é uma lista global e só pode haver um ConfigMap aplicado ao agente. Não pode ter outro ConfigMaps a anular as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configure e implemente configMaps

Execute os seguintes passos para configurar e implementar o seu ficheiro de configuração ConfigMap para o seu cluster.

1. [Descarregue](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o ficheiro de yaml do modelo ConfigMap e guarde-o como contentor-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Este passo não é necessário quando se trabalha com o Azure Red Hat OpenShift uma vez que o modelo ConfigMap já existe no cluster.

2. Editar o ficheiro de yaml ConfigMap com as suas personalizações para recolher variáveis stdout, stderr e/ou ambientais. Se estiver a editar o ficheiro de yaml ConfigMap para O `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Hat OpenShift azure Red Hat, primeiro executa o comando para abrir o ficheiro num editor de texto.

    - Para excluir espaços de nome específicos para recolha de registos stdout, configura a chave/valor utilizando o seguinte exemplo: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Para desativar a recolha de variáveis `[log_collection_settings.env_var] enabled = true` ambientais para um recipiente específico, detete a chave/valor para permitir a recolha variável a nível global e, em seguida, siga os passos [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para completar a configuração para o recipiente específico.
    
    - Para desativar o cluster de recolha de registos stderr `[log_collection_settings.stderr] enabled = false`em toda a escala, configura a chave/valor utilizando o seguinte exemplo: .

3. Para clusters que não o Azure Red Hat OpenShift, crie o `kubectl apply -f <configmap_yaml_file.yaml>` ConfigMap executando o seguinte comando kubectl: em clusters que não o Azure Red Hat OpenShift. 
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Para o Azure Red Hat OpenShift, guarde as suas alterações no editor.

A mudança de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas de omsagent no cluster recomeçam. O reinício é um reinício rolante para todas as cápsulas de omsagent, nem todas reiniciam ao mesmo tempo. Quando os reinícios estiverem terminados, é apresentada uma mensagem semelhante `configmap "container-azm-ms-agentconfig" created`à seguinte e inclui o resultado: .

## <a name="verify-configuration"></a>Verificar a configuração

Para verificar se a configuração foi aplicada com sucesso a um cluster que não o Azure `kubectl logs omsagent-fdf58 -n=kube-system`Red Hat OpenShift, utilize o seguinte comando para rever os registos de uma cápsula de agente: . Se houver erros de configuração das cápsulas omsagent, a saída mostrará erros semelhantes aos seguintes:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Erros relacionados com a aplicação de alterações de configuração também estão disponíveis para revisão. As seguintes opções estão disponíveis para realizar uma resolução adicional de problemas de alterações de configuração:

- A partir de um registo `kubectl logs` de cápsulas de agente usando o mesmo comando. 

    >[!NOTE]
    >Este comando não é aplicável ao cluster Azure Red Hat OpenShift.
    > 

- Dos registos ao vivo. Os registos ao vivo mostram erros semelhantes aos seguintes:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Da tabela **KubeMonAgentEvents** no seu espaço de trabalho Log Analytics. Os dados são enviados a cada hora com a gravidade do *Erro* para erros de configuração. Caso não haja erros, a entrada na tabela terá dados com severity *Info*, que não reporta erros. A propriedade **Tags** contém mais informações sobre o id do casulo e do recipiente em que ocorreu o erro e também a primeira ocorrência, última ocorrência e contagem na última hora.

- Com o Azure Red Hat OpenShift, verifique os registos do omsagent, procurando na tabela **ContainerLog** para verificar se a recolha de registos de exploração madeireira em openshift-azure está ativada.

Depois de corrigir o erro(s) no ConfigMap em clusters que não o Azure Red Hat OpenShift, guarde o ficheiro yaml e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml`. Para o Azure Red Hat OpenShift, edite e guarde os ConfigMaps atualizados executando o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Aplicação do ConfigMap atualizado

Se já implementou um ConfigMap em clusters que não o Azure Red Hat OpenShift e pretende atualizá-lo com uma configuração mais recente, pode `kubectl apply -f <configmap_yaml_file.yaml`editar o ficheiro ConfigMap que já utilizou anteriormente e depois aplicar usando o mesmo comando que antes, . Para o Azure Red Hat OpenShift, edite e guarde os ConfigMaps atualizados executando o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

A mudança de configuração pode demorar alguns minutos a terminar antes de fazer efeito, e todas as cápsulas de omsagent no cluster recomeçam. O reinício é um reinício rolante para todas as cápsulas de omsagent, nem todas reiniciam ao mesmo tempo. Quando os reinícios estiverem terminados, é apresentada uma mensagem semelhante `configmap "container-azm-ms-agentconfig" updated`à seguinte e inclui o resultado: .

## <a name="verifying-schema-version"></a>Verificação da versão do esquema

As versões de config schema suportadas estão disponíveis como anotação de pod (versões de esquemas) na cápsula omsagent. Pode vê-los com o seguinte comando kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

A saída apresentará semelhante à seguinte com as versões de anotação schema:

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

- O Monitor Azure para contentores não inclui um conjunto predefinido de alertas. Reveja os alertas de [desempenho Create com o Monitor Azure para que](container-insights-alerts.md) os recipientes aprendam a criar alertas recomendados para alta CPU e utilização de memória para suportar os seus DevOps ou processos e procedimentos operacionais.

- Com a monitorização habilitada a recolher a utilização da saúde e dos recursos do seu cluster AKS ou híbrido e as cargas de trabalho que os executam, aprenda [a utilizar o](container-insights-analyze.md) Monitor Azure para contentores.

- Veja [exemplos](container-insights-log-search.md#search-logs-to-analyze-data) de consultas de log para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar os seus clusters.
