---
title: Utilize zonas de disponibilidade no Serviço Azure Kubernetes (AKS)
description: Saiba como criar um cluster que distribui nós através de zonas de disponibilidade no Serviço Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.custom: fasttrack-edit
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: b73cb09f95fa2b23fb23fb719fe57143e1731ceb
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086518"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Criar um cluster azure Kubernetes Service (AKS) que utiliza zonas de disponibilidade

Um cluster azure Kubernetes Service (AKS) distribui recursos como os nós e armazenamento em secções lógicas da infraestrutura computacional azure subjacente. Este modelo de implementação assegura-se de que os nós são executados através de domínios de atualização e falha separados num único datacenter Azure. Os clusters AKS implantados com este comportamento padrão fornecem um alto nível de disponibilidade para proteger contra uma falha de hardware ou evento de manutenção planeado.

Para fornecer um nível mais elevado de disponibilidade para as suas aplicações, os clusters AKS podem ser distribuídos por zonas de disponibilidade. Estas zonas são centros de dados fisicamente separados dentro de uma determinada região. Quando os componentes do cluster são distribuídos por várias zonas, o seu cluster AKS é capaz de tolerar uma falha numa dessas zonas. As suas aplicações e operações de gestão continuam disponíveis mesmo que um centro de dados inteiro tenha um problema.

Este artigo mostra-lhe como criar um cluster AKS e distribuir os componentes do nó em zonas de disponibilidade.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.76 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

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
* Europa Ocidental
* E.U.A.Oeste 2

As seguintes limitações aplicam-se quando se cria um cluster AKS utilizando zonas de disponibilidade:

* Só é possível ativar zonas de disponibilidade quando o cluster é criado.
* As definições da zona de disponibilidade não podem ser atualizadas após a criação do cluster. Também não é possível atualizar um cluster de zona de não disponibilidade existente para utilizar zonas de disponibilidade.
* Não é possível desativar as zonas de disponibilidade de um cluster AKS uma vez criado.
* O tamanho do nó (VM SKU) selecionado deve estar disponível em todas as zonas de disponibilidade.
* Os clusters com zonas de disponibilidade ativadas requerem a utilização de Balanceadores de Carga Padrão Azure para distribuição em zonas.
* Deve utilizar a versão Kubernetes 1.13.5 ou superior para implementar os Equilíbrios de Carga Padrão.

Os clusters AKS que utilizam zonas de disponibilidade devem utilizar o SKU *padrão* de balanceadores de carga Azure, que é o valor padrão para o tipo de equilíbrio de carga. Este tipo de equilíbrio de carga só pode ser definido no cluster criar tempo. Para obter mais informações e as limitações do equilibrador de carga padrão, consulte [as limitações padrão do SKU][standard-lb-limitations]do equilíbrio de carga Azure .

### <a name="azure-disks-limitations"></a>Limitações de discos azure

Os volumes que utilizam discos geridos pelo Azure não são atualmente recursos zonais. As cápsulas reagendadas numa zona diferente da sua zona original não podem voltar a ligar o disco anterior. É recomendado executar cargas de trabalho apátridas que não requerem armazenamento persistente que pode encontrar problemas zonais.

Se tiver de executar cargas de trabalho audais, use manchas e tolerâncias nas especificações do seu casulo para dizer ao programador kubernetes para criar cápsulas na mesma zona que os seus discos. Em alternativa, utilize armazenamento baseado em rede, como ficheiros Azure, que podem anexar-se a cápsulas à medida que estão programadas entre zonas.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral das zonas de disponibilidade para clusters AKS

As zonas de disponibilidade são uma oferta de alta disponibilidade que protege as suas aplicações e dados de falhas no datacenter. As zonas são locais físicos únicos dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das zonas de disponibilidade numa região protege as aplicações e os dados de falhas do datacenter. Os serviços redundantes em zonas replicam as suas aplicações e dados em zonas de disponibilidade para proteger contra pontos únicos de falha.

Para mais informações, consulte [As zonas de disponibilidade em Azure?][az-overview]

Os aglomerados AKS que são implantados usando zonas de disponibilidade podem distribuir nós em várias zonas dentro de uma única região. Por exemplo, um cluster na região de  *leste dos EUA 2* pode criar nós nas três zonas de disponibilidade no Leste dos EUA *2*. Esta distribuição de recursos de cluster AKS melhora a disponibilidade de cluster, uma vez que são resistentes ao fracasso de uma zona específica.

![Distribuição do nó AKS em zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Numa paragem de zona, os nós podem ser reequilibrados manualmente ou utilizando o autoescalador do cluster. Se uma única zona ficar indisponível, as suas aplicações continuam a ser executadas.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster AKS em todas as zonas de disponibilidade

Quando se cria um cluster utilizando as [aks az criar][az-aks-create] comando, o parâmetro `--zones` define em que zonas os nós dos agentes são implantados. Os componentes do plano de controlo AKS para o seu cluster também estão espalhados por zonas na configuração mais elevada disponível quando define o parâmetro `--zones` no tempo de criação do cluster.

Se não definir nenhuma zona para o pool de agente padrão quando criar um cluster AKS, os componentes do plano de controlo AKS para o seu cluster não usarão zonas de disponibilidade. Você pode adicionar piscinas de nó adicionais usando o [az aks nodepool adicionar][az-aks-nodepool-add] comando e especificar `--zones` para esses novos nós, no entanto os componentes do plano de controlo permanecem sem a consciência da zona de disponibilidade. Não se pode mudar a consciência da zona para uma piscina de nó ou os componentes do plano de controlo AKS uma vez que estão implantados.

O exemplo seguinte cria um cluster AKS chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. São criados um total de *3* nós - um agente na zona *1,* um em *cada 2*, e um em *cada 3*. Os componentes do plano de controlo AKS também são distribuídos por zonas na configuração mais alta disponível, uma vez que são definidos como parte do processo de criação de cluster.

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

Leva alguns minutos para criar o cluster AKS.

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

Note que nas versões kubernetes mais recentes (1.17.0 e mais tarde), a AKS está a usar a nova etiqueta `topology.kubernetes.io/zone` para além da `failure-domain.beta.kubernetes.io/zone`depreciada .

## <a name="verify-pod-distribution-across-zones"></a>Verificar a distribuição de casulos em todas as zonas

Tal como documentado em [Etiquetas, Anotações e Manchas Bem Conhecidas,][kubectl-well_known_labels]a Kubernetes utiliza a etiqueta `failure-domain.beta.kubernetes.io/zone` para distribuir automaticamente cápsulas num controlador ou serviço de replicação nas diferentes zonas disponíveis. Para testar isto, pode aumentar o seu cluster de 3 a 5 nós, para verificar a propagação correta da cápsula:

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

Como podem ver, agora temos dois nós adicionais nas zonas 1 e 2. Pode implementar uma aplicação composta por três réplicas. Usaremos o NGINX como exemplo:

```console
kubectl run nginx --image=nginx --replicas=3
```

Se verificar se os nós de onde as suas cápsulas estão a funcionar, verá que as cápsulas estão a funcionar nas cápsulas correspondentes a três zonas de disponibilidade diferentes. Por exemplo, com o comando `kubectl describe pod | grep -e "^Name:" -e "^Node:"` obteria uma saída semelhante a esta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Como pode ver na saída anterior, a primeira cápsula está a funcionar no nó 0, que está localizado na zona de disponibilidade `eastus2-1`. A segunda cápsula está a funcionar no nó 2, que corresponde a `eastus2-3`, e a terceira no nó 4, em `eastus2-2`. Sem qualquer configuração adicional, a Kubernetes está a espalhar corretamente as cápsulas pelas três zonas de disponibilidade.

## <a name="next-steps"></a>Passos seguintes

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

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
