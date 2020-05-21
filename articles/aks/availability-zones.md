---
title: Utilize zonas de disponibilidade no Serviço Azure Kubernetes (AKS)
description: Saiba como criar um cluster que distribui nós através de zonas de disponibilidade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 35aaad31728f4a0cd73913ecf397d8123b3f909a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725101"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Criar um cluster azure Kubernetes Service (AKS) que utiliza zonas de disponibilidade

Um cluster azure Kubernetes Service (AKS) distribui recursos como nós e armazenamento em secções lógicas da infraestrutura azure subjacente. Este modelo de implantação ao utilizar zonas de disponibilidade, garante que os nós numa determinada zona de disponibilidade estão fisicamente separados dos definidos noutra zona de disponibilidade. Os clusters AKS implantados com múltiplas zonas de disponibilidade configuradas em todo um cluster fornecem um nível mais elevado de disponibilidade para proteger contra uma falha de hardware ou um evento de manutenção planeado.

Ao definir piscinas de nós em um cluster para abranger várias zonas, nós em uma dada piscina de nó são capazes de continuar a operar mesmo que uma única zona tenha caído. As suas aplicações podem continuar disponíveis mesmo que haja uma falha física num único datacenter se orquestradas para tolerar a falha de um subconjunto de nós.

Este artigo mostra-lhe como criar um cluster AKS e distribuir os componentes do nó em zonas de disponibilidade.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.76 ou posteriormente instalada e configurada. Corra  `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitações e disponibilidade da região

Os clusters AKS podem atualmente ser criados utilizando zonas de disponibilidade nas seguintes regiões:

* E.U.A. Central
* E.U.A. Leste 2
* E.U.A. Leste
* França Central
* Leste do Japão
* Europa do Norte
* Ásia Sudeste
* Sul do Reino Unido
* Europa ocidental
* E.U.A.Oeste 2

As seguintes limitações aplicam-se quando se cria um cluster AKS utilizando zonas de disponibilidade:

* Só é possível definir zonas de disponibilidade quando o cluster ou piscina de nó é criado.
* As definições da zona de disponibilidade não podem ser atualizadas após a criação do cluster. Também não é possível atualizar um cluster de zona de não disponibilidade existente para utilizar zonas de disponibilidade.
* O tamanho do nó escolhido (VM SKU) selecionado deve estar disponível em todas as zonas de disponibilidade selecionadas.
* Os clusters com zonas de disponibilidade ativadas requerem a utilização de Balanceadores de Carga Padrão Azure para distribuição em zonas. Este tipo de equilíbrio de carga só pode ser definido no cluster criar tempo. Para obter mais informações e as limitações do equilibrador de carga padrão, consulte [as limitações padrão do SKU][standard-lb-limitations]do equilíbrio de carga Azure .

### <a name="azure-disks-limitations"></a>Limitações de discos azure

Os volumes que utilizam discos geridos pelo Azure não são atualmente recursos redundantes em zonas. Os volumes não podem ser fixados em zonas e devem ser co-localizados na mesma zona que um determinado nó que acolhe uma cápsula-alvo.

Se tiver de executar cargas de trabalho audais, utilize manchas de piscina e tolerações em especificações de pod para agrupar o agendamento de cápsulas na mesma zona que os seus discos. Em alternativa, utilize armazenamento baseado em rede, como ficheiros Azure, que podem anexar-se a cápsulas à medida que estão programadas entre zonas.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral das zonas de disponibilidade para clusters AKS

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados de falhas no datacenter. As zonas são locais físicos únicos dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas pela zona. A separação física das zonas de disponibilidade numa região protege as aplicações e os dados de falhas do datacenter.

Para mais informações, consulte [As zonas de disponibilidade em Azure?][az-overview]

Os aglomerados AKS que são implantados usando zonas de disponibilidade podem distribuir nós em várias zonas dentro de uma única região. Por exemplo, um cluster na região *leste dos EUA 2*pode criar nós nas três zonas de disponibilidade no Leste dos EUA   *2*. Esta distribuição de recursos de cluster AKS melhora a disponibilidade de cluster, uma vez que são resistentes ao fracasso de uma zona específica.

![Distribuição do nó AKS em zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Se uma única zona ficar indisponível, as suas aplicações continuam a ser executadas se o cluster estiver espalhado por várias zonas.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster AKS em todas as zonas de disponibilidade

Quando se cria um cluster utilizando as [aks az criar][az-aks-create] comando, o `--zones` parâmetro define em que zonas os nós dos agentes são implantados. Os componentes do plano de controlo, como o etcd, são espalhados por três zonas se definir o parâmetro no tempo de criação do `--zones` cluster. As zonas específicas que os componentes do plano de controlo estão espalhados são independentes das zonas explícitas selecionadas para a piscina inicial do nó.

Se não definir nenhuma zona para o pool de agente padrão quando criar um cluster AKS, os componentes de plano de controlo não são garantidos para se espalhar em zonas de disponibilidade. Você pode adicionar piscinas de nó adicionais usando o [az aks nodepool adicionar][az-aks-nodepool-add] comando e especificar `--zones` para novos nós, mas não vai alterar a forma como o plano de controlo foi espalhado por zonas. As definições da zona de disponibilidade só podem ser definidas em conjunto ou piscina de nó criar tempo.

O exemplo seguinte cria um cluster AKS chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. São criados um total de *3* nós - um agente na zona *1,* um em *cada 2*, e um em *cada 3*.

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

Leva alguns minutos para criar o aglomerado AKS.

Ao decidir a que zona um novo nó deve pertencer, um dado nó aks piscina usará uma melhor zona de [esforço equilibrando oferecido por conjuntos de escala de máquinas virtuais azure subjacentes][vmss-zone-balancing]. Uma dada piscina de nó AKS é considerada "equilibrada" se cada zona tiver o mesmo número de VMs ou + \- 1 VM em todas as outras zonas para o conjunto de escala.

## <a name="verify-node-distribution-across-zones"></a>Verifique a distribuição do nó através de zonas

Quando o cluster estiver pronto, lista os nós do agente na escala definida para ver em que zona de disponibilidade estão implantadas.

Primeiro, obtenha as credenciais de cluster AKS usando o comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, use o [kubectl descrever][kubectl-describe] o comando para listar os nós no cluster. Filtrar o *valor failure-domain.beta.kubernetes.io/zone,* como mostra o seguinte exemplo:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A saída de exemplo seguinte mostra os três nós distribuídos pela região especificada e zonas de disponibilidade, tais como *leste2-1* para a primeira zona de disponibilidade e *leste2-2* para a segunda zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

À medida que adiciona nós adicionais a um conjunto de agentes, a plataforma Azure distribui automaticamente os VMs subjacentes através das zonas de disponibilidade especificadas.

Note que nas versões kubernetes mais recentes (1.17.0 e mais tarde), a AKS está a usar a etiqueta mais recente `topology.kubernetes.io/zone` para além das depreciadas `failure-domain.beta.kubernetes.io/zone` .

## <a name="verify-pod-distribution-across-zones"></a>Verificar a distribuição de casulos em todas as zonas

Tal como documentado em [Etiquetas, Anotações e Manchas Bem Conhecidas,][kubectl-well_known_labels]a Kubernetes utiliza a `failure-domain.beta.kubernetes.io/zone` etiqueta para distribuir automaticamente cápsulas num controlador ou serviço de replicação nas diferentes zonas disponíveis. Para testar isto, pode aumentar o seu cluster de 3 a 5 nós, para verificar a propagação correta da cápsula:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Quando a operação de escala estiver concluída após alguns minutos, o comando `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` deve dar uma saída semelhante a esta amostra:

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

Agora temos dois nós adicionais nas zonas 1 e 2. Pode implementar uma aplicação composta por três réplicas. Usaremos o NGINX como exemplo:

```console
kubectl run nginx --image=nginx --replicas=3
```

Ao ver os nós onde as suas cápsulas estão a funcionar, vê-se que as cápsulas estão a correr nos nós correspondentes a três zonas de disponibilidade diferentes. Por exemplo, com o comando `kubectl describe pod | grep -e "^Name:" -e "^Node:"` obteria uma saída semelhante a esta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Como pode ver na saída anterior, a primeira cápsula está a funcionar no nó 0, que está localizado na zona de disponibilidade `eastus2-1` . A segunda cápsula está a funcionar no nó 2, que corresponde a `eastus2-3` , e a terceira no nó 4, em `eastus2-2` . Sem qualquer configuração adicional, a Kubernetes está a espalhar corretamente as cápsulas pelas três zonas de disponibilidade.

## <a name="next-steps"></a>Próximos passos

Este artigo detalhou como criar um cluster AKS que utiliza zonas de disponibilidade. Para obter mais considerações sobre clusters altamente disponíveis, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres em AKS.][best-practices-bc-dr]

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
