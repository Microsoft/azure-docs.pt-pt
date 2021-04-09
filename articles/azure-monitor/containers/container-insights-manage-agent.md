---
title: Como gerir o agente de insights do contentor | Microsoft Docs
description: Este artigo descreve a gestão das tarefas de manutenção mais comuns com o agente de Log Analytics contentorizado utilizado por insights de contentores.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 2a0c32ef797a953eca794e16fe0ace5e967f339f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713801"
---
# <a name="how-to-manage-the-container-insights-agent"></a>Como gerir o agente de insights do contentor

Os insights do contentor utilizam uma versão contentorizada do agente Log Analytics para o Linux. Após a colocação inicial, existem tarefas rotineiras ou opcionais que poderá necessitar de executar durante o seu ciclo de vida. Este artigo detalha como atualizar manualmente o agente e desativar a recolha de variáveis ambientais a partir de um determinado recipiente. 

## <a name="how-to-upgrade-the-container-insights-agent"></a>Como atualizar o agente de insights do contentor

Os insights do contentor utilizam uma versão contentorizada do agente Log Analytics para o Linux. Quando uma nova versão do agente é lançada, o agente é automaticamente atualizado nos seus clusters de Kubernetes geridos hospedados no Azure Kubernetes Service (AKS) e na versão 3.x do Azure Red Hat OpenShift. Para um [cluster híbrido Kubernetes](container-insights-hybrid-setup.md) e Azure Red Hat OpenShift versão 4.x, o agente não é gerido, e você precisa atualizar manualmente o agente.

Se a atualização do agente falhar para um cluster alojado na versão 3.x do AKS ou do Azure Red Hat OpenShift, este artigo também descreve o processo para atualizar manualmente o agente. Para acompanhar as versões lançadas, consulte os [anúncios de lançamento do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-aks-cluster"></a>Agente de upgrade no cluster AKS

O processo de atualização do agente em clusters AKS consiste em dois passos para a frente. O primeiro passo é desativar a monitorização com insights do contentor utilizando o Azure CLI. Siga os passos descritos no artigo [de monitorização para deficientes.](container-insights-optout.md?#azure-cli) A utilização do CLI Azure permite-nos remover o agente dos nós do cluster sem impactar a solução e os dados correspondentes que são armazenados no espaço de trabalho. 

>[!NOTE]
>Enquanto estiver a realizar esta atividade de manutenção, os nós no cluster não estão a encaminhar dados recolhidos, e as vistas de desempenho não mostrarão dados entre o momento em que remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga os passos descritos na [monitorização de ativação utilizando o Azure CLI,](container-insights-enable-new-cluster.md#enable-using-azure-cli)para concluir este processo.  

Depois de ter re-activado a monitorização, pode demorar cerca de 15 minutos até poder ver as métricas de saúde atualizadas para o cluster. Para verificar se o agente foi atualizado com sucesso, pode:

* Executar o comando: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . No estado devolvido, note o valor em **Imagem** para omsagent na secção *Contentores* da saída.
* No **separador Nodes,** selecione o nó de cluster e no painel **de propriedades** à direita, observe o valor na Etiqueta de Imagem do **Agente**.

A versão do agente mostrada deve corresponder à versão mais recente listada na página [de histórico de Lançamento.](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Agente de upgrade no cluster híbrido Kubernetes

Execute os seguintes passos para atualizar o agente num cluster Kubernetes em execução:

* Clusters Kubernetes auto-geridos hospedados em Azure usando motor AKS.
* Clusters Kubernetes auto-geridos hospedados em Azure Stack ou no local usando motor AKS.
* Red Hat OpenShift versão 4.x.

Se o espaço de trabalho Log Analytics estiver em Azure comercial, executar o seguinte comando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Se o espaço de trabalho Log Analytics estiver no Azure China 21Vianet, executar o seguinte comando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Se o espaço de trabalho Log Analytics estiver no Governo dos EUA, executar o seguinte comando:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Agente de upgrade no Azure Red Hat OpenShift v4

Execute os seguintes passos para atualizar o agente num cluster Kubernetes em execução na versão 4.x do Azure Red Hat OpenShift. 

>[!NOTE]
>A versão 4.x do Azure Red Hat OpenShift suporta apenas a execução na nuvem comercial Azure.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

Ver **Utilização do principal de serviço** em Enable [monitoring do Azure Arc ativou o cluster Kubernetes](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) para obter detalhes sobre a utilização de um principal de serviço com este comando.

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Agente de upgrade em Azure Arc habilitado Kubernetes

Execute o seguinte comando para atualizar o agente num Azure Arc ativado pelo cluster Kubernetes.

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

Ver **Utilização do principal de serviço** em Enable [monitoring do Azure Arc ativou o cluster Kubernetes](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) para obter detalhes sobre a utilização de um principal de serviço com este comando.


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desativar a recolha variável do ambiente num recipiente

Os insights do contentor recolhem variáveis ambientais dos contentores que funcionam numa vagem e apresentam-nas no painel de propriedade do recipiente selecionado na vista **dos Recipientes.** Pode controlar este comportamento desativando a recolha de um recipiente específico durante a implantação do cluster Kubernetes, quer depois de definir a variável ambiental *AZMON_COLLECT_ENV*. Esta funcionalidade encontra-se disponível na versão do agente – ciprod11292018 e superior.  

Para desativar a recolha de variáveis ambientais num recipiente novo ou existente, deite a variável **AZMON_COLLECT_ENV** com um valor de **Falso** no seu ficheiro de configuração de implementação de Kubernetes. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Executar o seguinte comando para aplicar a alteração aos clusters Kubernetes que não o Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>` . Para editar o ConfigMap e aplicar esta alteração para clusters Azure Red Hat OpenShift, executar o comando:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Isto abre o seu editor de texto padrão. Depois de definir a variável, guarde o ficheiro no editor.

Para verificar se a alteração de configuração entrou em vigor, selecione um recipiente na vista **de Recipientes** em insights de contentores, e no painel de propriedades, expanda **as Variáveis ambientais**.  A secção deve mostrar apenas a variável criada anteriormente - **AZMON_COLLECT_ENV=FALSE**. Para todos os outros recipientes, a secção De Variáveis ambientais deve listar todas as variáveis ambientais descobertas.

Para reepercutir a descoberta das variáveis ambientais, aplique o mesmo processo mais cedo e altere o valor de **Falso** para **Verdadeiro**, e, em seguida, volte a fazer a `kubectl` retalho para atualizar o recipiente.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas durante a atualização do agente, reveja o [guia de resolução de problemas](container-insights-troubleshoot.md) para obter apoio.
