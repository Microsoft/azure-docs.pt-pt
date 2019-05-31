---
title: Utilizar vários conjuntos de nós no Azure Kubernetes Service (AKS)
description: Saiba como criar e gerir vários conjuntos de nós de um cluster no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 4af2e97e8ace432c37a770f1930514dd19e30944
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235758"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Pré-visualizar - criar e gerir vários conjuntos de nós de um cluster no Azure Kubernetes Service (AKS)

No Azure Kubernetes Service (AKS), nós da configuração do mesmo são agrupados em conjunto *conjuntos de nós*. Estes conjuntos de nós contêm as VMs subjacentes que executem as suas aplicações. O número inicial de nós e seu tamanho (SKU) está definido quando cria um cluster do AKS, que cria um *predefinido o conjunto de nós*. Para suportar as aplicações com computação diferentes ou necessidades de armazenamento, pode criar conjuntos de nós adicionais. Por exemplo, utilize esses agrupamentos de nó adicional para fornecer GPUs para as aplicações de computação intensiva ou acesso a armazenamento SSD de elevado desempenho.

Este artigo mostra-lhe como criar e gerir vários conjuntos de nós num cluster do AKS. Esta funcionalidade encontra-se em pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks

Os comandos da CLI para criar e gerir vários conjuntos de nós estão disponíveis no *pré-visualização do aks* extensão da CLI. Instalar o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az] [ az-extension-add] de comando, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Se instalou anteriormente a *pré-visualização do aks* extensão, instale qualquer disponível atualizações com o `az extension update --name aks-preview` comando.

### <a name="register-multiple-node-pool-feature-provider"></a>Registar o fornecedor de recursos de conjunto de nós de vários

Para criar um cluster do AKS que pode utilizar vários conjuntos de nós, ative primeiro dois sinalizadores de recurso na sua subscrição. Clusters com vários nós conjunto utilizam um conjunto de dimensionamento de máquinas virtuais (VMSS) para gerir a implementação e configuração de nós do Kubernetes. Registe-se a *MultiAgentpoolPreview* e *VMSSPreview* sinalizadores de recurso com o [Registre-se de funcionalidade de az] [ az-feature-register] comando conforme mostrado na exemplo a seguir:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualquer cluster do AKS criar depois de registar com êxito a *MultiAgentpoolPreview* utilizar esta experiência de cluster de pré-visualização. Para continuar a criar clusters regulares, totalmente suportada, não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize um desenvolvimento subscrição do Azure ou de teste separada para fins de teste de funcionalidades de pré-visualização.

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az] [ az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az] [ az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se ao criar e gerir clusters do AKS que oferecem suporte a vários conjuntos de nós:

* Vários conjuntos de nós só estão disponíveis nos clusters criados após ter registrado com êxito a *MultiAgentpoolPreview* e *VMSSPreview* funcionalidades para a sua subscrição. Não é possível adicionar ou gerir conjuntos de nós com um cluster do AKS existente criado antes desses recursos foram registados com êxito.
* Não é possível eliminar o primeiro conjunto de nós.
* Não é possível utilizar o suplemento de encaminhamento de aplicação de HTTP.
* Não é possível utilizar um modelo do Resource Manager existente tal como acontece com a maioria das operações de conjuntos de nós de adicionar/atualizar/eliminar. Em vez disso, [utilizar um modelo do Resource Manager separado](#manage-node-pools-using-a-resource-manager-template) fazer alterações em conjuntos de nós num cluster do AKS.

Enquanto esta funcionalidade está em pré-visualização, aplicam-se as seguintes limitações adicionais:

* O cluster do AKS pode ter um máximo de oito conjuntos de nós.
* O cluster do AKS pode ter um máximo de 400 nós entre esses conjuntos de oito nós.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Para começar, crie um cluster do AKS com um conjunto de nó único. O exemplo seguinte utiliza a [criar grupo az] [ az-group-create] comando para criar um grupo de recursos com o nome *myResourceGroup* no *eastus* região. Um cluster do AKS com o nome *myAKSCluster* , em seguida, é criado usando o [criar az aks] [ az-aks-create] comando. R *kubernetes--version* de *1.12.6* é utilizado para mostrar como atualizar um conjunto de nós num passo seguinte. Pode especificar qualquer [suportada a versão do Kubernetes][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

A criação do cluster demora alguns minutos.

Quando o cluster estiver pronto, utilize o [az aks get-credentials] [ az-aks-get-credentials] comando para obter as credenciais de cluster para utilização com `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicionar um conjunto de nós

O cluster que criou no passo anterior tem um conjunto de nó único. Vamos adicionar um segundo nó agrupamento com o [adicionar conjunto de nós do aks az] [ az-aks-nodepool-add] comando. O exemplo seguinte cria um conjunto de nós com o nome *mynodepool* que executa *3* nós:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Para ver o estado dos seus conjuntos de nós, utilize o [lista de conjunto de nós do az aks] [ az-aks-nodepool-list] de comando e especifique o nome de cluster e de grupo de recursos:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

O resultado de exemplo seguinte mostra que *mynodepool* foi criado com êxito com três nós no conjunto de nós. Quando o cluster do AKS foi criado no passo anterior, um padrão *nodepool1* foi criado com uma contagem de nó de *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Se nenhum *OrchestratorVersion* ou *VmSize* é especificado ao adicionar um conjunto de nós, os nós são criados com base nas predefinições para o cluster do AKS. Neste exemplo, que foi a versão do Kubernetes *1.12.6* e o tamanho de nó dos *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Atualizar um conjunto de nós

Quando o cluster do AKS foi criado na primeira etapa, um `--kubernetes-version` dos *1.12.6* foi especificado. Vamos atualizar o *mynodepool* para o Kubernetes *1.12.7*. Utilize o [atualização de conjunto de nó do az aks] [ az-aks-nodepool-upgrade] comando para atualizar o conjunto de nós, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Liste o estado dos seus conjuntos de nós novamente com o [lista de conjunto de nós do az aks] [ az-aks-nodepool-list] comando. O exemplo seguinte mostra que *mynodepool* está no *Upgrading* estado para *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Demora alguns minutos para atualizar os nós para a versão especificada.

Como melhor prática, deve atualizar todos os conjuntos de nós num cluster do AKS para a mesma versão do Kubernetes. A capacidade para atualizar conjuntos de nós individuais permite-lhe executar uma atualização sem interrupção e agendar pods entre conjuntos de nós para manter o tempo de atividade do aplicativo.

## <a name="scale-a-node-pool"></a>Dimensionar um conjunto de nós

Como seu aplicativo de carga de trabalho exige alterações, poderá ter de aumentar o número de nós num conjunto de nós. O número de nós pode ser aumentado vertical ou horizontalmente.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para dimensionar o número de nós num conjunto de nós, utilize o [escala de conjunto de nó do az aks] [ az-aks-nodepool-scale] comando. O exemplo seguinte aumenta o número de nós *mynodepool* ao *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Liste o estado dos seus conjuntos de nós novamente com o [lista de conjunto de nós do az aks] [ az-aks-nodepool-list] comando. O exemplo seguinte mostra que *mynodepool* está no *Scaling* Estado com uma nova contagem de *5* nós:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Demora alguns minutos para concluir a operação de dimensionamento.

## <a name="delete-a-node-pool"></a>Eliminar um conjunto de nós

Se já não precisar de um conjunto, pode eliminá-lo e remover os nós da VM subjacentes. Para eliminar um conjunto de nós, utilize o [eliminar o conjunto de nós do aks az] [ az-aks-nodepool-delete] de comando e especifique o nome do conjunto de nó. O exemplo seguinte elimina a *mynoodepool* criado nos passos anteriores:

> [!CAUTION]
> Não existem opções de recuperação sem perda de dados que podem ocorrer quando eliminar um conjunto de nós. Se não não possível agendar pods em outros conjuntos de nós, esses aplicativos não estão disponíveis. Certifique-se de que não eliminar um conjunto de nós, quando aplicativos em utilização não tem cópias de segurança de dados ou a capacidade de executar em outros conjuntos de nós do cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

A seguinte saída de exemplo do [lista de conjunto de nós do az aks] [ az-aks-nodepool-list] comando mostra que *mynodepool* está no *a eliminação* Estado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Demora alguns minutos para eliminar os nós e o conjunto de nós.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especificar um tamanho VM para um conjunto de nós

Nos exemplos anteriores para criar um conjunto de nós, o tamanho de VM predefinido foi utilizado para os nós criados no cluster. Um cenário mais comum é a criação de conjuntos de nós com diferentes tamanhos de VM e recursos. Por exemplo, pode criar um conjunto de nós que contém nós com grandes quantidades de CPU ou memória ou um conjunto de nós que fornece suporte GPU. No próximo passo, [taints de utilização e tolerations][#schedule-pods-using-taints-and-tolerations] para informar o agendador do Kubernetes como limitar o acesso a pods que podem ser executadas em nós.

No exemplo a seguir, criar um conjunto de nós baseada em GPU que utiliza a *Standard_NC6* tamanho da VM. Estas VMs têm a tecnologia NVIDIA Tesla K80 cartão. Para obter informações sobre tamanhos VM disponíveis, consulte [tamanhos de máquinas de virtuais do Linux no Azure][vm-sizes].

Criar um conjunto de nó com o [adicionar conjunto de nós do aks az] [ az-aks-nodepool-add] novamente o comando. Desta vez, especifique o nome *gpunodepool*e utilizar os `--node-vm-size` parâmetro para especificar o *Standard_NC6* tamanho:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A seguinte saída de exemplo do [lista de conjunto de nós do az aks] [ az-aks-nodepool-list] comando mostra que *gpunodepool* é *criando* nós com o especificado *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Demora alguns minutos o *gpunodepool* para ser criado com êxito.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Agendar pods usando taints e tolerations

Agora tem dois conjuntos de nós no seu cluster - o conjunto de nó de predefinido criado inicialmente e o conjunto de nós baseada em GPU. Utilize o [kubectl obter nós] [ kubectl-get] comando para ver os nós do cluster. O resultado de exemplo seguinte mostra um nó em cada conjunto de nós:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

O agendador de Kubernetes pode utilizar taints e tolerations para restringir o que podem executar cargas de trabalho em nós.

* R **taint** é aplicada a um nó que indica que apenas os pods específicos podem ser agendados nos mesmos.
* R **toleration** , em seguida, é aplicado a um pod que lhes permita *tolerar* taint de um nó.

Para obter mais informações sobre como utilizar avançadas de recursos do Kubernetes agendada, consulte [melhores práticas para as funcionalidades avançadas do scheduler no AKS][taints-tolerations]

Neste exemplo, aplicar um taint sua baseada em GPU de nó com o [kubectl taint nó] [ kubectl-taint] comando. Especifique o nome do seu nó baseada em GPU da saída do anterior `kubectl get nodes` comando. O taint é aplicado como um *chave: valor* e, em seguida, uma opção de agendamento. O exemplo seguinte utiliza a *sku = gpu* emparelhe e define os pods caso contrário, o *NoSchedule* capacidade:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

O manifesto YAML seguinte exemplo básico utiliza um toleration para permitir que o agendador de Kubernetes executar um pod do NGINX no nó baseada em GPU. Para obter um exemplo mais adequado, mas com uso intensivo de tempo executar uma tarefa do Tensorflow no conjunto de dados MNIST, veja [GPUs de utilização para cargas de trabalho de computação intensiva no AKS][gpu-cluster].

Crie um ficheiro denominado `gpu-toleration.yaml` e copie o exemplo seguinte YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Agendar o pod com o `kubectl apply -f gpu-toleration.yaml` comando:

```console
kubectl apply -f gpu-toleration.yaml
```

Demora alguns segundos para agendar o pod e extraia a imagem da NGINX. Utilize o [kubectl descrevem pod] [ kubectl-describe] comando para ver o estado de pod. O seguinte exemplo condensado saída mostra o *sku = gpu:NoSchedule* toleration é aplicada. Na seção de eventos, o agendador atribuiu o pod para o *aks-gpunodepool-28993262-vmss000000* baseada em GPU de nó:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Apenas os pods que tenham este taint aplicada podem ser agendados em nós *gpunodepool*. Quaisquer outro pod teria de ser agendada do *nodepool1* conjunto de nós. Se criar conjuntos de nós adicionais, pode utilizar taints adicionais e tolerations para limitar os pods podem ser agendadas a esses recursos de nó.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerir conjuntos de nós com um modelo do Resource Manager

Quando utiliza um modelo Azure Resource Manager para criar e recursos gerenciados, normalmente, é possível atualizar as definições no seu modelo e Reimplementar para atualizar o recurso. Com nodepools no AKS, o perfil de nodepool inicial não pode ser atualizado depois de criar o cluster do AKS. Este comportamento significa que não é possível atualizar um modelo do Resource Manager existente, faça uma alteração para os conjuntos de nós e voltar a implementar. Em vez disso, tem de criar um modelo do Resource Manager separado, que atualiza os conjuntos de agentes para um cluster do AKS existente.

Criar um modelo como `aks-agentpools.json` e cole o manifesto de exemplo seguinte. Este modelo de exemplo configura as seguintes definições:

* Atualizações a *Linux* com o nome de conjunto de agentes *myagentpool* a execução de três nós.
* Define os nós no conjunto de nós para executar a versão do Kubernetes *1.12.8*.
* Define o tamanho do nó como *Standard_DS2_v2*.

Edite estes valores conforme necessário para atualizar, adicionar ou eliminar conjuntos de nós, conforme necessário:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Implementar este modelo com o [criar a implementação do grupo az] [ az-group-deployment-create] de comando, conforme mostrado no exemplo a seguir. São-lhe pedido para o nome do cluster AKS e a localização existente:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Pode demorar alguns minutos a atualizar o seu cluster do AKS consoante as definições de agrupamento de nó e operações que define no modelo do Resource Manager.

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criou um cluster do AKS que inclui nós baseada em GPU. Para reduzir custos desnecessários, pode querer eliminar os *gpunodepool*, ou o cluster do AKS inteiro.

Para eliminar o conjunto de nós baseada em GPU, utilize o [eliminar az aks nodepool] [ az-aks-nodepool-delete] comando conforme mostrado no exemplo seguinte:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para eliminar o próprio cluster, utilize o [eliminação do grupo de az] [ az-group-delete] comando para eliminar o grupo de recursos do AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como criar e gerir vários conjuntos de nós num cluster do AKS. Para obter mais informações sobre como controlar os pods em conjuntos de nós, consulte [melhores práticas para as funcionalidades avançadas do scheduler no AKS][operator-best-practices-advanced-scheduler].

Para criar e utilizar conjuntos de nós de contentor do Windows Server, consulte [criar um contentor do Windows Server no AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
