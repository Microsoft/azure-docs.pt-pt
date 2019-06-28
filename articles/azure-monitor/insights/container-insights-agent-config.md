---
title: Configure o Azure Monitor para recolha de dados do agente de contentores | Documentos da Microsoft
description: Este artigo descreve como pode configurar o Azure Monitor para agente de contentores controlar o stdout/stderr e recolha de registos de variáveis de ambiente.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341653"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurar a recolha de dados do agente para o Monitor do Azure para contentores

Monitor do Azure para contentores recolhe stdout, stderr e variáveis de ambiente de cargas de trabalho de contentor implementadas em clusters do Kubernetes alojados no Azure Kubernetes Service (AKS) do agente em contentores geridos. Pode configurar definições de recolha de dados do agente através da criação de um personalizado ConfigMaps do Kubernetes para controlar essa experiência. Este artigo demonstra como criar ConfigMap e configurar a recolha de dados com base nos seus requisitos.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Configurar o seu cluster com definições de recolha de dados personalizados

Um arquivo de modelo de ConfigMap é fornecida permitindo que facilmente editá-lo com as suas personalizações sem ter de criá-la a partir do zero. Antes de começar, deve rever a documentação do Kubernetes sobre [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e se familiarize com a forma de criar, configurar e implementar ConfigMaps. Isto permitirá filtrar stderr e stdout por espaço de nomes ou entre o cluster completo e variáveis de ambiente para qualquer contentor em execução em todos os pods/nós no cluster.

>[!IMPORTANT]
>A versão de agente mínima suportada por esta funcionalidade é microsoft / oms:ciprod06142019 ou posterior. 

### <a name="overview-of-configurable-data-collection-settings"></a>Descrição geral das definições de recolha de dados configuráveis

Seguem-se as definições que podem ser configuradas para controlar a recolha de dados.

|Chave |Tipo de dados |Value |Descrição |
|----|----------|------|------------|
|`schema-version` |Cadeia (sensível a maiúsculas e minúsculas) |v1 |Esta é a versão de esquema usada pelo agente de quando este ConfigMap de análise. Versão de esquema suportado atualmente é v1. Modificar este valor não é suportada e será rejeitada quando ConfigMap é avaliada.|
|`config-version` |String | | Suporta a capacidade de manter o controle de versão do ficheiro de configuração no seu controle sistema/repositório de origem. Número máximo de carateres permitido é 10 e todos os outros carateres são truncados. |
|`[log_collection_settings.stdout] enabled =` |Boolean | VERDADEIRO ou FALSO | Controla se a recolha de registos do contentor de stdout está ativada. Quando definido como `true` e não espaços de nomes são excluídos para recolha de registos de stdout (`log_collection_settings.stdout.exclude_namespaces` definição abaixo), os registos de stdout serão recolhidos de todos os contentores de todos os pods/nós no cluster. Se não for especificado no ConfigMaps, o valor predefinido é `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Matriz separada por vírgulas |Matriz de espaços de nomes do Kubernetes para que stdout registos não serão recolhidos. Esta definição é eficaz apenas se for `log_collection_settings.stdout.enabled` está definido como `true`. Se não for especificado no ConfigMap, o valor predefinido é `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | VERDADEIRO ou FALSO |Controla se a recolha de registos do contentor de stderr está ativada. Quando definido como `true` e não espaços de nomes são excluídos para recolha de registos de stdout (`log_collection_settings.stderr.exclude_namespaces` definição), stderr registos serão recolhidos de todos os contentores de todos os pods/nós no cluster. Se não for especificado no ConfigMaps, o valor predefinido é `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Matriz separada por vírgulas |Matriz de espaços de nomes do Kubernetes para o qual stderr registos não serão recolhidos. Esta definição é eficaz apenas se for `log_collection_settings.stdout.enabled` está definido como `true`. Se não for especificado no ConfigMap, o valor predefinido é `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | VERDADEIRO ou FALSO | Controla se a coleção de variáveis de ambiente está ativada. Quando definido como `false`, não existem variáveis de ambiente são recolhidas para qualquer contentor em execução em todos os pods/nós no cluster. Se não for especificado no ConfigMap, o valor predefinido é `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Configurar e implementar ConfigMaps

Execute os seguintes passos para configurar e implementar o ficheiro de configuração de ConfigMap ao seu cluster.

1. [Transferir](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) yaml de ConfigMap o modelo de ficheiro e guarde-o como contentor-azm-ms-agentconfig.yaml.  
1. Edite o ficheiro yaml de ConfigMap com as suas personalizações. 

    - Para excluir espaços de nomes específicos de recolha de registos de stdout, configura o chave/valor usando o seguinte exemplo: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Para desativar a coleção de variáveis de ambiente para um contentor específico, defina o chave/valor `[log_collection_settings.env_var] enabled = true` para ativar a recolha de variável global e, em seguida, siga os passos [aqui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) para toda a configuração do contentor específico.
    - Para desativar a recolha de registos de stderr em todo o cluster, configure o chave/valor usando o seguinte exemplo: `[log_collection_settings.stderr] enabled = false`.

1. Crie ConfigMap ao executar o seguinte comando kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemplo: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    A alteração de configuração pode demorar alguns minutos para concluir antes de entrarem em vigor e irão reiniciar todos os pods de omsagent no cluster. O reinício é um reinício sem interrupção para todos os pods de omsagent, nem todos reiniciar ao mesmo tempo. Quando terminar os reinícios, é apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" created`.

Para verificar a configuração foi aplicada com êxito, utilize o seguinte comando para rever os registos de um pod do agente: `kubectl logs omsagent-fdf58 -n=kube-system`. Se houver erros de configuração de osmagent pods, o resultado apresentará erros semelhantes ao seguinte:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Erros de impedem que omsagent analisar o ficheiro, fazendo com que a reinicie e utilizar a configuração predefinida. Depois de corrigir os erros no ConfigMap, guarde o ficheiro yaml e aplicar o ConfigMaps atualizada ao executar o comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Aplicação atualizada ConfigMap

Se já tiver implementado um ConfigMap para o seu cluster e pretende atualizá-lo com uma configuração mais recente, pode simplesmente editar o ficheiro de ConfigMap usou anteriormente e, em seguida, serão aplicadas usando o mesmo comando como antes, `kubectl apply -f <configmap_yaml_file.yaml`.

A alteração de configuração pode demorar alguns minutos para concluir antes de entrarem em vigor e irão reiniciar todos os pods de omsagent no cluster. O reinício é um reinício sem interrupção para todos os pods de omsagent, nem todos reiniciar ao mesmo tempo. Quando terminar os reinícios, é apresentada uma mensagem que é semelhante ao seguinte e inclui o resultado: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verificar a versão de esquema

Versões de esquema de configuração suportados estão disponíveis como anotação de pod (versões de esquema) sobre o pod omsagent. Pode vê-los com o seguinte comando kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

O resultado apresentará semelhante ao seguinte com as versões de esquema de anotação:

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

## <a name="next-steps"></a>Passos Seguintes

- Para continuar a aprender a utilizar o Azure Monitor e monitorizar outros aspectos do seu cluster do AKS, veja [estado de funcionamento do serviço de Kubernetes do Azure de modo de exibição](container-insights-analyze.md).
- Modo de exibição [exemplos de consulta de registo](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas predefinidas e exemplos para avaliar ou personalizar para os alertas, visualizar ou analisar os seus clusters.