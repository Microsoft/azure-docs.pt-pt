---
title: Como gerir o Azure Monitor para agente de contentores | Documentos da Microsoft
description: Este artigo descreve a gerir as tarefas de manutenção mais comuns com o agente do Log Analytics em contentores utilizado pelo Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843961"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerir o Azure Monitor para agente de contentores

Monitor do Azure para contentores utiliza uma versão em contentores do agente do Log Analytics para Linux. Após a implementação inicial, existem tarefas opcionais que poderá ter de efetuar durante seu ciclo de vida ou de rotina. Este artigo descreve a sobre como atualizar o agente e desativar a recolha de variáveis de ambiente de um determinado contêiner manualmente. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o Azure Monitor para agente de contentores

Monitor do Azure para contentores utiliza uma versão em contentores do agente do Log Analytics para Linux. Quando uma nova versão do agente é lançada, o agente é automaticamente atualizado nos seus clusters Kubernetes geridos hospedados no Azure Kubernetes Service (AKS) e Azure Red Hat OpenShift. Para um [cluster híbrido kubernetes,](container-insights-hybrid-setup.md) o agente não é gerido e precisa de atualizar manualmente o agente.

Se a atualização do agente falhar num cluster hospedado no AKS, este artigo também descreve o processo para atualizar manualmente o agente. Para seguir as versões lançadas, consulte [anúncios de lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Agente de upgrade no cluster Kubernetes monitorizado

O processo para atualizar o agente em clusters, diferente do Azure Red Hat OpenShift, consiste em duas etapas diretas. A primeira etapa é desativar a monitorização com o Azure Monitor para contentores com a CLI do Azure. Siga os passos descritos no [desativar a monitorização](container-insights-optout.md?#azure-cli) artigo. Com a CLI do Azure permite-nos remover o agente a partir de nós do cluster sem afetar a solução e os dados correspondentes, que são armazenados na área de trabalho. 

>[!NOTE]
>Enquanto estiver a efetuar esta atividade de manutenção, os nós do cluster não estão a reencaminhar os dados recolhidos e vistas de desempenho não apresentará dados entre a hora a remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas em [habilitar monitoramento usando CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli)para concluir este processo.  

Depois de habilitar novamente o monitoramento, pode levar cerca de 15 minutos para que você possa exibir as métricas de integridade atualizadas para o cluster. Para verificar se o agente atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

O estado deve assemelhar-se o exemplo seguinte, onde o valor para *omi* e *omsagent* deve corresponder à versão mais recente especificada no [histórico de versões de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Agente de upgrade no cluster Kubernetes híbrido

O processo de atualização do agente num cluster Kubernetes hospedado no local, aks engine on Azure e Azure Stack pode ser concluído executando o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Se o espaço de trabalho Log Analytics estiver no Azure China, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Se o espaço de trabalho Log Analytics estiver no governo dos EUA do Azure, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desativar a coleção de variáveis de ambiente num contêiner

Monitor do Azure para contentores recolhe variáveis de ambiente de contentores em execução num pod e apresenta-os no painel de propriedades do contentor selecionado na **contentores** vista. Pode controlar este comportamento desativando a recolha de um recipiente específico, quer durante a implantação do cluster Kubernetes, quer após a definição da variável ambiental *AZMON_COLLECT_ENV*. Esta funcionalidade está disponível a partir da versão de agente – ciprod11292018 e superior.  

Para desativar a recolha de variáveis ambientais num recipiente novo ou existente, detete a variável **AZMON_COLLECT_ENV** com um valor de **Falso** no seu ficheiro de configuração de yaml de implantação kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Execute o comando a seguir para aplicar a alteração em clusters kubernetes diferentes do Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Para editar ConfigMap e aplicar essa alteração para os clusters do Azure Red Hat OpenShift, execute o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Isso abre o editor de texto padrão. Depois de definir a variável, salve o arquivo no editor.

Para verificar se as alterações de configuração entraram em vigor, selecione um contêiner na exibição **contêineres** em Azure monitor para contêineres e, no painel de propriedades, expanda **variáveis de ambiente**.  A secção deve mostrar apenas a variável criada anteriormente - **AZMON_COLLECT_ENV=FALSE**. Para todos os outros contentores, a secção de variáveis de ambiente deve listar todas as variáveis de ambiente detetadas.

Para reabilitar a descoberta das variáveis ambientais, aplique o mesmo processo anteriormente e altere o valor de **false** para **true**e, em seguida, execute novamente o comando `kubectl` para atualizar o contêiner.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Passos seguintes

Se ocorrerem problemas durante a atualização do agente, consulte a [guia de resolução de problemas](container-insights-troubleshoot.md) para o suporte.
