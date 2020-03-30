---
title: Como gerir o Monitor Azure para agente de contentores [ Microsoft Docs
description: Este artigo descreve a gestão das tarefas de manutenção mais comuns com o agente de Log Analytics contentorizado utilizado pelo Azure Monitor para contentores.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275324"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerir o Monitor Azure para agente de contentores

O Monitor Azure para contentores utiliza uma versão contentorizada do agente Log Analytics para o Linux. Após a implementação inicial, existem tarefas rotineiras ou opcionais que poderá ter de realizar durante o seu ciclo de vida. Este artigo detalha como atualizar manualmente o agente e desativar a recolha de variáveis ambientais a partir de um determinado recipiente. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o Monitor Azure para agente de contentores

O Monitor Azure para contentores utiliza uma versão contentorizada do agente Log Analytics para o Linux. Quando uma nova versão do agente é lançada, o agente é automaticamente atualizado nos seus clusters Kubernetes geridos hospedados no Azure Kubernetes Service (AKS) e Azure Red Hat OpenShift. Para um [cluster híbrido kubernetes,](container-insights-hybrid-setup.md) o agente não é gerido e precisa de atualizar manualmente o agente.

Se a atualização do agente falhar num cluster hospedado no AKS, este artigo também descreve o processo para atualizar manualmente o agente. Para acompanhar as versões lançadas, consulte os [anúncios](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)de lançamento do agente .

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Agente de upgrade no cluster Kubernetes monitorizado

O processo de atualização do agente em clusters, com além do Azure Red Hat OpenShift, consiste em dois passos para a frente. O primeiro passo é desativar a monitorização com o Monitor Azure para os contentores que utilizem o Azure CLI. Siga os passos descritos no artigo de [monitorização de desativação.](container-insights-optout.md?#azure-cli) A utilização do Azure CLI permite-nos remover o agente dos nós do cluster sem afetar a solução e os dados correspondentes que são armazenados no espaço de trabalho. 

>[!NOTE]
>Enquanto está a realizar esta atividade de manutenção, os nós do cluster não estão a reencaminhar dados recolhidos, e as visualizações de desempenho não mostrarão dados entre o momento em que remove o agente e instala a nova versão. 
>

Para instalar a nova versão do agente, siga os passos descritos na [monitorização ativa utilizando o Azure CLI,](container-insights-enable-new-cluster.md#enable-using-azure-cli)para completar este processo.  

Depois de ter reativado a monitorização, pode demorar cerca de 15 minutos até poder ver métricas de saúde atualizadas para o cluster. Para verificar se o agente foi atualizado com sucesso, execute o comando:`kubectl logs omsagent-484hw --namespace=kube-system`

O estado deve assemelhar-se ao seguinte exemplo, em que o valor para *omi* e *omsagent* deve corresponder à versão mais recente especificada no histórico de [libertação](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)do agente .  

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

Se o espaço de trabalho do Log Analytics estiver na China Azure, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Se o espaço de trabalho do Log Analytics estiver no Governo dos EUA, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desativar a recolha de variáveis ambientais num recipiente

O Monitor Azure para contentores recolhe variáveis ambientais dos contentores que correm numa vagem e apresenta-as no painel de propriedades do contentor selecionado na vista dos **contentores.** Pode controlar este comportamento desativando a recolha de um recipiente específico, quer durante a implantação do cluster Kubernetes, quer após a definição da variável ambiental *AZMON_COLLECT_ENV*. Esta funcionalidade está disponível na versão do agente – ciprod11292018 e superior.  

Para desativar a recolha de variáveis ambientais num recipiente novo ou existente, detete a variável **AZMON_COLLECT_ENV** com um valor de **Falso** no seu ficheiro de configuração de yaml de implantação kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Executar o seguinte comando para aplicar a alteração em clusters Kubernetes que não o Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Para editar o ConfigMap e aplicar esta alteração para os clusters OpenShift do Chapéu Vermelho Azure, execute o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Isto abre o seu editor de texto predefinido. Depois de definir a variável, guarde o ficheiro no editor.

Para verificar se a mudança de configuração entrou em vigor, selecione um recipiente na vista dos **contentores** no Monitor Azure para contentores e no painel de propriedades, expandir **variáveis ambientais.**  A secção deve mostrar apenas a variável criada anteriormente - **AZMON_COLLECT_ENV=FALSE**. Para todos os outros recipientes, a secção Variáveis Ambientais deve enumerar todas as variáveis ambientais descobertas.

Para reativar a descoberta das variáveis ambientais, aplique o mesmo processo mais cedo `kubectl` e altere o valor de **Falso** para **Verdadeiro**, e depois reexecutar o comando para atualizar o recipiente.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas ao atualizar o agente, reveja o guia de resolução de [problemas](container-insights-troubleshoot.md) para obter suporte.
