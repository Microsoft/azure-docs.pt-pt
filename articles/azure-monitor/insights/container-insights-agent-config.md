---
title: Configurar Azure Monitor para coleta de dados de agente de contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o Azure Monitor para agente de contêineres para controlar stdout/stderr e a coleção de logs de variáveis de ambiente.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0bde696f39af22f864500e0c79b5e03ca66cc7f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405668"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurar a coleta de dados do agente para Azure Monitor para contêineres

Azure Monitor para contêineres coleta variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner implantadas em clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure) do agente em contêiner. Você pode definir as configurações de coleta de dados do agente criando um ConfigMaps kubernetes personalizado para controlar essa experiência. 

Este artigo demonstra como criar ConfigMap e configurar a coleta de dados com base em seus requisitos.

## <a name="configmap-file-settings-overview"></a>Visão geral das configurações do arquivo ConfigMap

Um arquivo de modelo ConfigMap é fornecido para que você possa editá-lo facilmente com suas personalizações sem precisar criá-lo do zero. Antes de começar, você deve examinar a documentação do kubernetes sobre [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e se familiarizar com como criar, configurar e implantar o ConfigMaps. Isso permitirá que você filtre stderr e stdout por namespace ou por todo o cluster e variáveis de ambiente para qualquer contêiner em execução em todos os pods/nós no cluster.

>[!IMPORTANT]
>A versão mínima do agente com suporte para coletar variáveis stdout, stderr e ambientais de cargas de trabalho de contêiner é ciprod06142019 ou posterior. Para verificar a versão do agente, na guia **nó** , selecione um nó e, no painel Propriedades, observe o valor da propriedade **marca da imagem do agente** . Para obter informações adicionais sobre as versões do agente e o que está incluído em cada versão, consulte [notas de versão do agente](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Configurações de coleta de dados

A seguir estão as configurações que podem ser definidas para controlar a coleta de dados.

|Chave |Data type |Valor |Descrição |
|----|----------|------|------------|
|`schema-version` |Cadeia de caracteres (diferencia maiúsculas de minúsculas) |v1 |Esta é a versão do esquema usada pelo agente ao analisar este ConfigMap. A versão de esquema com suporte atualmente é v1. Não há suporte para a modificação desse valor e ele será rejeitado quando ConfigMap for avaliado.|
|`config-version` |String | | Dá suporte à capacidade de controlar a versão deste arquivo de configuração no sistema/repositório do controle do código-fonte. Os caracteres máximos permitidos são 10 e todos os outros caracteres são truncados. |
|`[log_collection_settings.stdout] enabled =` |Booleano | true ou false | Isso controla se a coleta de log de contêiner stdout está habilitada. Quando definido como `true` e nenhum namespace for excluído para a coleta de log de stdout (`log_collection_settings.stdout.exclude_namespaces` configuração abaixo), os logs de stdout serão coletados de todos os contêineres em todos os pods/nós no cluster. Se não for especificado em ConfigMaps, o valor padrão será `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Matriz separada por vírgulas |Matriz de namespaces kubernetes para os quais os logs de stdout não serão coletados. Essa configuração só será eficaz se `log_collection_settings.stdout.enabled` estiver definida como `true`. Se não for especificado em ConfigMap, o valor padrão será `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Booleano | true ou false |Isso controla se a coleta de log de contêiner stderr está habilitada. Quando definido como `true` e nenhum namespace for excluído para a coleta de log de stdout (configuração de`log_collection_settings.stderr.exclude_namespaces`), os logs de stderr serão coletados de todos os contêineres em todos os pods/nós no cluster. Se não for especificado em ConfigMaps, o valor padrão será `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Matriz separada por vírgulas |Matriz de namespaces kubernetes para os quais os logs de stderr não serão coletados. Essa configuração só será eficaz se `log_collection_settings.stdout.enabled` estiver definida como `true`. Se não for especificado em ConfigMap, o valor padrão será `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Booleano | true ou false | Essa configuração controla a coleção de variáveis de ambiente em todos os pods/nós no cluster e usa como padrão `enabled = true` quando não especificado em ConfigMaps. Se a coleção de variáveis de ambiente for habilitada globalmente, você poderá desabilitá-la para um contêiner específico definindo a variável de ambiente `AZMON_COLLECT_ENV` como **false** com uma configuração Dockerfile ou no [arquivo de configuração para o Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) na seção **env:** . Se a coleção de variáveis de ambiente for desabilitada globalmente, você não poderá habilitar a coleta para um contêiner específico (ou seja, a única substituição que pode ser aplicada no nível de contêiner será desabilitar a coleta quando ela já estiver habilitada globalmente.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Booleano | true ou false | Essa configuração controla o enriquecimento do log de contêiner para popular os valores de propriedade Name e Image para cada registro de log gravado na tabela ContainerLog para todos os logs de contêiner no cluster. O padrão é `enabled = false` quando não especificado em ConfigMap. |

ConfigMaps é uma lista global e pode haver apenas um ConfigMap aplicado ao agente. Você não pode ter outro ConfigMaps que se refaça com as coleções.

## <a name="configure-and-deploy-configmaps"></a>Configurar e implantar ConfigMaps

Execute as etapas a seguir para configurar e implantar o arquivo de configuração do ConfigMap no cluster.

1. [Baixe](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) o arquivo do modelo ConfigMap YAML e salve-o como contêiner-AZM-MS-agentconfig. YAML.  

2. Edite o arquivo ConfigMap YAML com suas personalizações para coletar stdout, stderr e/ou variáveis ambientais.

    - Para excluir namespaces específicos para coleta de log de stdout, configure a chave/valor usando o exemplo a seguir: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Para desabilitar a coleção de variáveis de ambiente para um contêiner específico, defina a `[log_collection_settings.env_var] enabled = true` de chave/valor para habilitar a coleção de variáveis globalmente e siga as etapas [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para concluir a configuração para o contêiner específico.
    
    - Para desabilitar a coleção de logs stderr em todo o cluster, configure a chave/valor usando o seguinte exemplo: `[log_collection_settings.stderr] enabled = false`.

3. Crie ConfigMap executando o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Verificar configuração 

Para verificar se a configuração foi aplicada com êxito, use o seguinte comando para examinar os logs de um pod do agente: `kubectl logs omsagent-fdf58 -n=kube-system`. Se houver erros de configuração do pods omsagent, a saída mostrará erros semelhantes ao seguinte:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Os erros relacionados à aplicação de alterações de configuração também estão disponíveis para revisão. As opções a seguir estão disponíveis para executar a solução de problemas adicional de alterações de configuração:

- De logs de pod de agente usando o mesmo comando de `kubectl logs`. 

- De logs ao vivo. Os logs ao vivo mostram erros semelhantes ao seguinte:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Na tabela **KubeMonAgentEvents** em seu espaço de trabalho log Analytics. Os dados são enviados a cada hora com severidade de *erro* para erros de configuração. Se não houver erros, a entrada na tabela terá dados com *informações*de severidade, que não relatam erros. A propriedade **Tags** contém mais informações sobre o pod e a ID do contêiner em que o erro ocorreu e também a primeira ocorrência, última ocorrência e contagem na última hora.

Os erros impedem que o omsagent analise o arquivo, fazendo com que ele seja reiniciado e use a configuração padrão. Depois de corrigir os erros em ConfigMap, salve o arquivo YAML e aplique o ConfigMaps atualizado executando o comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Aplicando ConfigMap atualizado

Se você já tiver implantado um ConfigMap em seu cluster e quiser atualizá-lo com uma configuração mais recente, poderá editar o arquivo ConfigMap que você usou anteriormente e, em seguida, aplicar usando o mesmo comando que antes, `kubectl apply -f <configmap_yaml_file.yaml`.

A alteração de configuração pode levar alguns minutos para ser concluída antes de entrar em vigor, e todos os pods de omsagent no cluster serão reiniciados. A reinicialização é uma reinicialização sem interrupção para todos os pods omsagent, nem todas as reinicializações ao mesmo tempo. Quando as reinicializações forem concluídas, será exibida uma mensagem semelhante à seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verificando a versão do esquema

As versões de esquema de configuração com suporte estão disponíveis como uma anotação de Pod (versões de esquema) no pod omsagent. Você pode vê-los com o seguinte comando kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

A saída será exibida de forma semelhante à seguinte com a anotação Schema-Versions:

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

- Azure Monitor para contêineres não inclui um conjunto predefinido de alertas. Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps.

- Com o monitoramento habilitado para coletar a integridade e a utilização de recursos de seu cluster AKS ou híbrido e cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) Azure monitor para contêineres.

- Exiba [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para alertar, Visualizar ou analisar seus clusters.
