---
title: Utilize zonas de disponibilidade no Serviço Azure Kubernetes (AKS)
description: Saiba como criar um cluster que distribui nós em zonas de disponibilidade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 7d91491a2f521d974f15878791739a70a31c1bbe
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745811"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Criar um cluster Azure Kubernetes Service (AKS) que utiliza zonas de disponibilidade

Um cluster Azure Kubernetes Service (AKS) distribui recursos como nós e armazenamento em secções lógicas da infraestrutura Azure subjacente. Este modelo de implantação ao utilizar zonas de disponibilidade, garante que os nós numa determinada zona de disponibilidade são fisicamente separados dos definidos noutra zona de disponibilidade. Os clusters AKS implantados com múltiplas zonas de disponibilidade configuradas através de um cluster fornecem um nível mais elevado de disponibilidade para proteger contra uma falha de hardware ou um evento de manutenção planeado.

Ao definir piscinas de nó em um cluster para abranger várias zonas, os nós em uma determinada piscina de nós são capazes de continuar a operar mesmo que uma única zona tenha caído. As suas aplicações podem continuar disponíveis mesmo que haja uma falha física num único datacenter se orquestrado para tolerar a falha de um subconjunto de nós.

Este artigo mostra-lhe como criar um cluster AKS e distribuir os componentes do nó através de zonas de disponibilidade.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.76 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitações e disponibilidade da região

Os clusters AKS podem ser criados atualmente utilizando zonas de disponibilidade nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* E.U.A. Central
* E.U.A. Leste 2
* E.U.A. Leste
* França Central
* Leste do Japão
* Europa do Norte
* Sudeste Asiático
* Sul do Reino Unido
* Europa Ocidental
* E.U.A. Oeste 2

Aplicam-se as seguintes limitações quando cria um cluster AKS utilizando zonas de disponibilidade:

* Só é possível definir zonas de disponibilidade quando o cluster ou a piscina de nó são criados.
* As definições da zona de disponibilidade não podem ser atualizadas após a criação do cluster. Também não é possível atualizar um cluster de zonas existentes e não ingem disponibilidade para usar zonas de disponibilidade.
* O tamanho do nó escolhido (VM SKU) selecionado deve estar disponível em todas as zonas de disponibilidade selecionadas.
* Os clusters com zonas de disponibilidade ativadas requerem a utilização de Balançadores de Carga Padrão Azure para distribuição em zonas. Este tipo de balançador de carga só pode ser definido no cluster criar tempo. Para obter mais informações e as limitações do balançador de carga padrão, consulte [as limitações SKU padrão do balançador de carga Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitações dos discos Azure

Os volumes que utilizam discos geridos a Azure não são atualmente recursos redundantes em zonas. Os volumes não podem ser fixados em zonas e devem ser co-localizados na mesma zona que um dado nó que alberga a cápsula-alvo.

Se tiver de executar cargas de trabalho imponentes, use manchas de piscina de nó e tolerâncias nas especificações do pod para agendar pods em grupo na mesma zona que os seus discos. Em alternativa, utilize o armazenamento baseado em rede, como os Ficheiros Azure, que podem anexar-se a cápsulas à medida que estão programados entre zonas.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral das zonas de disponibilidade para clusters AKS

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados contra falhas no datacenter. As zonas são localizações físicas únicas dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há sempre mais de uma zona em todas as regiões ativadas por zonas. A separação física das zonas de disponibilidade numa região protege as aplicações e os dados de falhas do datacenter.

Para mais informações, veja [quais são as zonas de disponibilidade em Azure?][az-overview]

Os clusters AKS que são implantados usando zonas de disponibilidade podem distribuir nós em várias zonas dentro de uma única região. Por exemplo, um cluster na região  *leste dos EUA 2*   pode criar nós nas três zonas de disponibilidade no *Leste dos EUA 2* . Esta distribuição de recursos de cluster AKS melhora a disponibilidade do cluster, uma vez que são resistentes ao fracasso de uma zona específica.

![Distribuição de nó AKS em zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Se uma única zona ficar indisponível, as suas aplicações continuam a funcionar se o cluster estiver espalhado por várias zonas.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster AKS em zonas de disponibilidade

Quando cria um cluster utilizando as [az aks criar][az-aks-create] comando, o `--zones` parâmetro define em que zonas os nós de agente são implantados. Os componentes do plano de controlo, tais como etcd ou API, estão espalhados pelas zonas disponíveis na região se definirmos o parâmetro no tempo de criação do `--zones` cluster. As zonas específicas pelas quais os componentes do plano de controlo estão espalhados são independentes das zonas explícitas selecionadas para a piscina inicial do nó.

Se não definir nenhuma zona para o conjunto de agentes predefinidos quando criar um cluster AKS, os componentes do plano de controlo não são garantidos para se espalharem por zonas de disponibilidade. Pode adicionar piscinas de nó adicionais utilizando o [nodepool az aks adicionar][az-aks-nodepool-add] comando e especificar `--zones` para novos nós, mas não altera a forma como o plano de controlo foi espalhado por zonas. As definições da zona de disponibilidade só podem ser definidas no intervalo ou node pool-tempo.

O exemplo a seguir cria um cluster AKS chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup* . Um total de *3* nós são criados - um agente na zona *1* , um em *2* , e depois um em *cada 3* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

A criação do cluster do AKS demora alguns minutos.

Ao decidir a que zona deve pertencer um novo nó, uma dada piscina de nó AKS utilizará uma [melhor zona de esforço de equilíbrio oferecida por conjuntos de escala de máquina virtual Azure subjacentes][vmss-zone-balancing]. Um determinado conjunto de nó AKS é considerado "equilibrado" se cada zona tiver o mesmo número de VMs ou + \- 1 VM em todas as outras zonas para o conjunto de escala.

## <a name="verify-node-distribution-across-zones"></a>Verificar distribuição de nó em zonas

Quando o cluster estiver pronto, enumere os nós de agente na escala definida para ver em que zona de disponibilidade estão implantados.

Primeiro, obtenha as credenciais de cluster AKS usando o comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, utilize o comando [de desação do kubectl][kubectl-describe] para listar os nós no cluster e filtrar o *valor failure-domain.beta.kubernetes.io/zone.* O exemplo a seguir é para uma concha bash.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A saída de exemplo mostra os três nóns distribuídos pela região especificada e zonas de disponibilidade, tais como *eastus2-1* para a primeira zona de disponibilidade e *leste2-2* para a segunda zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Ao adicionar nós adicionais a um conjunto de agentes, a plataforma Azure distribui automaticamente os VMs subjacentes pelas zonas de disponibilidade especificadas.

Note que nas versões mais recentes de Kubernetes (1.17.0 e posterior), a AKS está a utilizar a etiqueta mais recente `topology.kubernetes.io/zone` para além da depreciada `failure-domain.beta.kubernetes.io/zone` .

## <a name="verify-pod-distribution-across-zones"></a>Verificar distribuição de cápsulas em zonas

Como documentado em [Etiquetas, Anotações e Manchas Bem Conhecidas,][kubectl-well_known_labels]a Kubernetes utiliza a `failure-domain.beta.kubernetes.io/zone` etiqueta para distribuir automaticamente cápsulas num controlador de replicação ou serviço nas diferentes zonas disponíveis. Para testar isto, pode escalar o seu cluster de 3 a 5 nóns, para verificar a propagação correta da cápsula:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Quando a operação de escala terminar após alguns minutos, o comando `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` numa casca bash deve dar uma saída semelhante a esta amostra:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Temos agora dois nós adicionais nas zonas 1 e 2. Pode implementar uma aplicação composta por três réplicas. Usaremos o NGINX como exemplo:

```console
kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=3
```

Ao visualizar nós onde as suas cápsulas estão a funcionar, vê que as cápsulas estão a correr nos nós correspondentes a três zonas de disponibilidade diferentes. Por exemplo, com o comando `kubectl describe pod | grep -e "^Name:" -e "^Node:"` numa concha bash obteria uma saída semelhante a esta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Como pode ver na saída anterior, a primeira cápsula está a funcionar no nó 0, que está localizado na zona de `eastus2-1` disponibilidade. A segunda cápsula está a funcionar no nó 2, que corresponde a `eastus2-3` , e a terceira no nó 4, em `eastus2-2` . Sem qualquer configuração adicional, a Kubernetes está a espalhar corretamente as cápsulas pelas três zonas de disponibilidade.

## <a name="next-steps"></a>Passos seguintes

Este artigo detalhou como criar um cluster AKS que utiliza zonas de disponibilidade. Para obter mais considerações sobre clusters altamente disponíveis, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres em AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
