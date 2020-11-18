---
title: Atualizar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como atualizar um cluster Azure Kubernetes Service (AKS) para obter as mais recentes funcionalidades e atualizações de segurança.
services: container-service
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 262905c9f840850795ba9555912e81eca61369d1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683238"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Azure Kubernetes Service (AKS)

Como parte do ciclo de vida de um cluster AKS, você precisa muitas vezes de atualizar para a versão mais recente de Kubernetes. É importante que aplique as mais recentes versões de segurança da Kubernetes ou upgrade para obter as funcionalidades mais recentes. Este artigo mostra-lhe como atualizar os componentes principais ou uma única piscina de nó padrão num cluster AKS.

Para clusters AKS que usam várias piscinas de nós ou nós do Windows Server, consulte [atualizar uma piscina de nós em AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Before you begin

Este artigo requer que esteja a executar a versão Azure CLI 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

> [!WARNING]
> Um upgrade de cluster AKS aciona um cordão e drenagem dos seus nós. Se tiver uma quota de cálculo baixa disponível, a atualização poderá falhar. Consulte [as quotas de aumento](../azure-portal/supportability/resource-manager-core-quotas-request.md) para mais informações.

## <a name="check-for-available-aks-cluster-upgrades"></a>Verifique se estão disponíveis atualizações de clusterS AKS

Para verificar quais as versões de Kubernetes disponíveis para o seu cluster, utilize o comando [az aks get upgrades.][az-aks-get-upgrades] O exemplo seguinte verifica as atualizações disponíveis para o cluster denominado *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando atualiza um cluster AKS suportado, as versões menores de Kubernetes não podem ser ignoradas. Por exemplo, são permitidas atualizações entre *1.12.x*  ->  *1.13.x* ou *1.13.x*  ->  *1.14.x,* no entanto *1.12.x*  ->  *1.14.x* não é.
>
> Para atualizar, a partir de *1.12.x*  ->  *1.14.x,* primeiro upgrade a partir de *1.12.x*  ->  *1.13.x,* em seguida, upgrade a partir de *1.13.x*  ->  *1.14.x*.
>
> Saltar várias versões só pode ser feito quando o upgrade de uma versão não suportada de volta para uma versão suportada. Por exemplo, o upgrade a partir de um *1.10.x* não suportado --> um *1.15.x* suportado pode ser concluído.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para as versões *1.13.9* e *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se não houver upgrade disponível, receberá:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Personalizar atualização de aumento de nó

> [!Important]
> Os aumentos de node requerem quota de subscrição para a contagem máxima de aumento solicitada para cada operação de upgrade. Por exemplo, um cluster que tem 5 piscinas de nó, cada uma com uma contagem de 4 nós, tem um total de 20 nós. Se cada piscina de nódoa tiver um valor máximo de aumento de 50%, é necessário um cálculo adicional e quota IP de 10 nós (2 nós * 5 piscinas) para completar a atualização.
>
> Se utilizar o Azure CNI, valide que existem IPs disponíveis na sub-rede, bem como para satisfazer os [requisitos de IP do Azure CNI](configure-azure-cni.md).

Por padrão, a AKS configura upgrades para aumentar com um nó adicional. Um valor predefinido de um para a definição de pico máximo permite que a AKS minimize a perturbação da carga de trabalho criando um nó adicional antes do cordão/drenagem das aplicações existentes para substituir um nó de versão mais antiga. O valor máximo de aumento pode ser personalizado por piscina de nó para permitir uma compensação entre a velocidade de upgrade e a interrupção do upgrade. Ao aumentar o valor máximo de aumento, o processo de upgrade completa-se mais rapidamente, mas definir um grande valor para o pico máximo pode causar interrupções durante o processo de atualização. 

Por exemplo, um valor máximo de aumento de 100% fornece o processo de atualização mais rápido possível (duplicando a contagem de nós) mas também faz com que todos os nós na piscina do nó sejam drenados simultaneamente. Pode desejar utilizar um valor mais elevado, como este, para testar ambientes. Para piscinas de nó de produção, recomendamos uma max_surge fixação de 33%.

A AKS aceita valores inteiros e um valor percentual para o aumento máximo. Um inteiro como "5" indica cinco nós adicionais para aumentar. Um valor de "50%" indica um valor de aumento de metade da contagem atual do nó na piscina. Os valores máximos de por cento podem ser um mínimo de 1% e um máximo de 100%. Um valor por cento é arredondado para a contagem de nós mais próximo. Se o valor máximo de aumento for inferior à contagem de nós atuais no momento da atualização, a contagem atual do nó é usada para o valor máximo de aumento.

Durante uma atualização, o valor máximo de aumento pode ser um mínimo de 1 e um valor máximo igual ao número de nós na piscina do nó. Pode definir valores maiores, mas o número máximo de nós usados para o pico máximo não será superior ao número de nós na piscina no momento da atualização.

Até à versão CLI 2.16.0+ necessitará da extensão CLI *de pré-visualização aks* para utilizar o pico máximo. Utilize o comando [de adicionar extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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

Com uma lista de versões disponíveis para o seu cluster AKS, utilize o comando [de upgrade az aks][az-aks-upgrade] para atualizar. Durante o processo de atualização, a AKS adiciona um novo nó tampão (ou tantos nós configurados em [pico máximo)](#customize-node-surge-upgrade)ao cluster que executa a versão especificada de Kubernetes. Em seguida, [irá cordon e drenar][kubernetes-drain] um dos nós antigos para minimizar a perturbação das aplicações de funcionamento (se estiver a usar o máximo de onda, irá isolar e [drenar][kubernetes-drain] tantos nós ao mesmo tempo que o número de nós tampão especificados). Quando o nó antigo estiver totalmente drenado, será reamco para receber a nova versão e tornar-se-á o nó tampão para o nó seguinte ser atualizado. Este processo repete-se até que todos os nós do cluster tenham sido atualizados. No final do processo, o último nó drenado será eliminado, mantendo a contagem de nó de agente existente.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Leva alguns minutos para atualizar o cluster, dependendo de quantos nós tem.

> [!NOTE]
> Há um tempo total permitido para um upgrade de cluster para completar. Desta vez é calculado tomando o produto de `10 minutes * total number of nodes in the cluster` . Por exemplo, num cluster de 20 nós, as operações de atualização devem ter sucesso em 200 minutos ou a AKS falhará na operação para evitar um estado de cluster irrecuperável. Para recuperar a falha de atualização, recísse a operação de atualização após o tempo limite ter sido atingido.

Para confirmar que a atualização foi bem sucedida, use o comando [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora funciona *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximos passos

Este artigo mostrou-lhe como atualizar um cluster AKS existente. Para saber mais sobre a implementação e gestão de clusters AKS, consulte o conjunto de tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
