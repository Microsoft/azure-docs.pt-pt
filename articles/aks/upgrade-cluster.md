---
title: Atualizar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como atualizar um cluster Azure Kubernetes Service (AKS) para obter as mais recentes funcionalidades e atualizações de segurança.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: d6a5ed468541090d433dba732707a59841e6ff41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779620"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Azure Kubernetes Service (AKS)

Parte do ciclo de vida do cluster AKS envolve a realização de upgrades periódicos para a versão mais recente de Kubernetes. É importante que aplique as mais recentes versões de segurança ou atualização para obter as funcionalidades mais recentes. Este artigo mostra-lhe como atualizar os componentes principais ou uma única piscina de nó padrão num cluster AKS.

Para clusters AKS que usam várias piscinas de nós ou nós do Windows Server, consulte [atualizar uma piscina de nós em AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que esteja a executar a versão Azure CLI 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

> [!WARNING]
> Um upgrade de cluster AKS aciona um cordão e drenagem dos seus nós. Se tiver uma quota de cálculo baixa disponível, a atualização poderá falhar. Para mais informações, consulte [aumentar as quotas](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Verifique se estão disponíveis atualizações de clusterS AKS

Para verificar quais as versões de Kubernetes disponíveis para o seu cluster, utilize o comando [az aks get upgrades.][az-aks-get-upgrades] Os seguintes testes de exemplo para atualizações disponíveis para *o myAKSCluster* no *myResourceGroup:*

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando atualiza um cluster AKS suportado, as versões menores de Kubernetes não podem ser ignoradas. Todas as atualizações devem ser efetuadas sequencialmente pelo número de versão principal. Por exemplo, são permitidas atualizações entre *1.14.x*  ->  *1.15.x* ou *1.15.x*  ->  *1.16.x,* no entanto não é permitido *1.14.x*  ->  *1.16.x.* 
> > Saltar várias versões só pode ser feito quando a atualização de uma _versão não suportada_ de volta para uma _versão suportada_. Por exemplo, uma atualização de um *1.10.x* não suportado --> um *1.15.x* suportado pode ser concluído.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para as versões *1.19.1* e *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Se não houver upgrade disponível, receberá a mensagem:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Personalizar atualização de aumento de nó

> [!Important]
> Os aumentos de node requerem quota de subscrição para a contagem máxima de aumento solicitada para cada operação de upgrade. Por exemplo, um cluster que tem 5 piscinas de nó, cada uma com uma contagem de 4 nós, tem um total de 20 nós. Se cada piscina de nódoa tiver um valor máximo de aumento de 50%, é necessário um cálculo adicional e quota IP de 10 nós (2 nós * 5 piscinas) para completar a atualização.
>
> Se utilizar o Azure CNI, valide que existem IPs disponíveis na sub-rede, bem como para satisfazer os [requisitos de IP do Azure CNI](configure-azure-cni.md).

Por padrão, a AKS configura upgrades para aumentar com um nó adicional. Um valor predefinido de um para as definições de pico máximo permitirá que a AKS minimize a perturbação da carga de trabalho criando um nó adicional antes do cordão/drenagem das aplicações existentes para substituir um nó de versão mais antiga. O valor máximo de aumento pode ser personalizado por piscina de nó para permitir uma compensação entre a velocidade de upgrade e a interrupção do upgrade. Ao aumentar o valor máximo de aumento, o processo de upgrade completa-se mais rapidamente, mas definir um grande valor para o pico máximo pode causar interrupções durante o processo de atualização. 

Por exemplo, um valor máximo de aumento de 100% fornece o processo de atualização mais rápido possível (duplicando a contagem de nós) mas também faz com que todos os nós na piscina do nó sejam drenados simultaneamente. Pode desejar utilizar um valor mais elevado, como este, para testar ambientes. Para piscinas de nó de produção, recomendamos uma max_surge fixação de 33%.

A AKS aceita valores inteiros e um valor percentual para o aumento máximo. Um inteiro como "5" indica cinco nós adicionais para aumentar. Um valor de "50%" indica um valor de aumento de metade da contagem atual do nó na piscina. Os valores máximos de por cento podem ser um mínimo de 1% e um máximo de 100%. Um valor por cento é arredondado para a contagem de nós mais próximo. Se o valor máximo de aumento for inferior à contagem de nós atuais no momento da atualização, a contagem atual do nó é usada para o valor máximo de aumento.

Durante uma atualização, o valor máximo de aumento pode ser um mínimo de 1 e um valor máximo igual ao número de nós na piscina do nó. Pode definir valores maiores, mas o número máximo de nós usados para o pico máximo não será superior ao número de nós na piscina no momento da atualização.

> [!Important]
> A regulação máxima do pico numa piscina de nó é permanente.  As atualizações subsequentes da Versão Kubernetes ou do nó utilizarão esta definição. Pode alterar o valor máximo de aumento para as suas piscinas de nó a qualquer momento. Para piscinas de nó de produção, recomendamos uma regulação máxima de 33%.

Utilize os seguintes comandos para definir os valores máximos de subida para piscinas de nó novos ou existentes.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster do AKS

Com uma lista de versões disponíveis para o seu cluster AKS, utilize o comando [de upgrade az aks][az-aks-upgrade] para atualizar. Durante o processo de atualização, a AKS: 
- adicione um novo nó tampão (ou tantos nós como configurados em [pico máximo)](#customize-node-surge-upgrade)ao cluster que executa a versão especificada de Kubernetes. 
- [cordão e drene][kubernetes-drain] um dos nós antigos para minimizar a perturbação das aplicações de funcionamento (se estiver a usar o máximo de onda, [irá cordon e drenar][kubernetes-drain] o número de nós ao mesmo tempo que o número de nós tampão especificado). 
- Quando o nó antigo estiver totalmente drenado, será reamco para receber a nova versão e tornar-se-á o nó tampão para o nó seguinte ser atualizado. 
- Este processo repete-se até que todos os nós do cluster tenham sido atualizados. 
- No final do processo, o último nó tampão será eliminado, mantendo a contagem de nós do agente existente e o equilíbrio de zona.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Leva alguns minutos para atualizar o cluster, dependendo de quantos nós tem.

> [!IMPORTANT]
> Certifique-se de que qualquer `PodDisruptionBudgets` (PDBs) permite que pelo menos 1 réplica de cápsulas seja movida de cada vez, caso contrário, a operação de drenagem/despejo falhará.
> Se a operação de drenagem falhar, a operação de atualização falhará por conceção para garantir que as aplicações não sejam interrompidas. Corrija o que causou a paragem da operação (PDBs incorretos, falta de quota, e assim por diante) e re-tente a operação.

Para confirmar que a atualização foi bem sucedida, use o comando [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora funciona *1.18.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Definir canal de atualização automática

Além de atualizar manualmente um cluster, pode definir um canal de atualização automática no seu cluster. Estão disponíveis os seguintes canais de atualização:

|Canal| Ação | Exemplo
|---|---|---|
| `none`| desativa as atualizações automáticas e mantém o cluster na sua versão atual de Kubernetes| Definição predefinido se deixada inalterada|
| `patch`| Atualize automaticamente o cluster para a versão patch suportada mais recente quando estiver disponível, mantendo a versão menor da mesma forma.| Por exemplo, se um cluster estiver a executar a versão *1.17.7* e as versões *1.17.9*, *1.18.4*, *1.18.6*, e *1.19.1* estão disponíveis, o seu cluster é atualizado para *1.17.9*|
| `stable`| Atualize automaticamente o cluster para a versão mais recente suportada do patch na versão menor *N-1*, onde *N* é a versão menor suportada mais recente.| Por exemplo, se um cluster estiver a executar a versão *1.17.7* e as versões *1.17.9*, *1.18.4*, *1.18.6*, e *1.19.1* estão disponíveis, o seu cluster é atualizado para *1.18.6*.
| `rapid`| Atualize automaticamente o cluster para a versão mais recente suportada do patch na versão menor suportada mais recente.| Nos casos em que o cluster se encontra numa versão de Kubernetes que se encontra numa versão menor *N-2* onde *N* é a versão menor suportada mais recente, o cluster atualiza-se pela primeira vez para a versão patch suportada na versão menor *N-1.* Por exemplo, se um cluster estiver a executar a versão *1.17.7* e as versões *1.17.9*, *1.18.4*, *1.18.6*, e *1.19.1* estão disponíveis, o seu cluster é atualizado primeiro para *1.18.6*, então é atualizado para *1.19.1*.

> [!NOTE]
> O Cluster apenas atualiza as atualizações para as versões GA de Kubernetes e não atualizará para versões de pré-visualização.

A atualização automática de um cluster segue o mesmo processo que a atualização manual de um cluster. Para mais detalhes, consulte [atualizar um cluster AKS][upgrade-cluster].

O cluster de upgrade automático para clusters AKS é uma funcionalidade de pré-visualização.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registe o `AutoUpgradePreview` pavilhão de funcionalidades utilizando o comando [de registo de recursos az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Demora alguns minutos para que o estado seja *apresentado.* Verifique o estado de registo utilizando o comando [da lista de recursos AZ:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Para definir o canal de atualização automática ao criar um cluster, utilize o parâmetro *do canal de atualização automática,* semelhante ao exemplo seguinte.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Para definir o canal de atualização automática no cluster existente, atualize o parâmetro *do canal de atualização automática,* semelhante ao exemplo seguinte.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Passos seguintes

Este artigo mostrou-lhe como atualizar um cluster AKS existente. Para saber mais sobre a implementação e gestão de clusters AKS, consulte o conjunto de tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-provider-register]: /cli/azure/provider#az_provider_register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
