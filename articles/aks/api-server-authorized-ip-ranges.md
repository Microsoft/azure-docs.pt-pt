---
title: Gamas IP autorizadas do servidor API no Serviço Azure Kubernetes (AKS)
description: Saiba como proteger o seu cluster utilizando uma gama de endereços IP para acesso ao servidor API no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 593f9e0b335e6f4d62c76ce92f833ff4e9143372
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126616"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Acesso seguro ao servidor API utilizando gamas de endereços IP autorizadas no Serviço Azure Kubernetes (AKS)

Em Kubernetes, o servidor API recebe pedidos para realizar ações no cluster, tais como criar recursos ou escalar o número de nós. O servidor API é a forma central de interagir e gerir um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra-lhe como utilizar os intervalos de endereços IP autorizados do servidor API para limitar quais os endereços IP e CIDRs que podem aceder ao plano de controlo.

> [!IMPORTANT]
> Em novos clusters, os intervalos de endereços IP autorizados do servidor API são suportados apenas no equilíbrio de carga *SKU Padrão.* Os clusters existentes com o equilíbrio de carga *SKU Básico* e os intervalos de endereços IP autorizados do servidor API configurados continuarão a funcionar como está, mas não podem ser migrados para um equilíbrio de carga *SKU Padrão.* Esses clusters existentes também continuarão a funcionar se a sua versão Kubernetes ou o plano de controlo forem atualizados.

## <a name="before-you-begin"></a>Antes de começar

Os intervalos IP autorizados do servidor API funcionam apenas para novos clusters AKS que cria. Este artigo mostra-lhe como criar um cluster AKS usando o Azure CLI.

Precisa da versão Azure CLI 2.0.76 ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral das gamas IP autorizadas do servidor API

O servidor Kubernetes API é como as APIs kubernetes subjacentes são expostas. Este componente proporciona a interação para ferramentas de gestão, como `kubectl` ou o dashboard Kubernetes. A AKS fornece um mestre de agrupamento de inquilinos único, com um servidor API dedicado. Por predefinição, o servidor API é atribuído a um endereço IP público, e deve controlar o acesso utilizando controlos de acesso baseados em funções (RBAC).

Para garantir o acesso ao plano de controlo AKS /servidor API acessível ao público, pode ativar e utilizar gamas IP autorizadas. Estas gamas IP autorizadas apenas permitem intervalos de endereçoip definidos para comunicar com o servidor API. Está bloqueado um pedido feito ao servidor API a partir de um endereço IP que não faça parte destas gamas IP autorizadas. Continue a utilizar o RBAC para autorizar os utilizadores e as ações que solicitam.

Para obter mais informações sobre o servidor API e outros componentes de cluster, consulte os [conceitos centrais da Kubernetes para AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Criar um cluster AKS com gamas IP autorizadas do servidor API ativadas

Os intervalos IP autorizados do servidor API funcionam apenas para novos clusters AKS. Crie um cluster utilizando as [aks az criar][az-aks-create] e especificar o parâmetro de --api-server-autorizado-ip-intervalos para fornecer uma lista de intervalos de endereçoip autorizados. Estas gamas de endereços IP são geralmente faixas de endereço utilizadas pelas suas redes no local ou iPs públicos. Quando especificar uma gama CIDR, comece com o primeiro endereço IP na gama. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP na gama, tais como *137.117.106.88/29*.

> [!IMPORTANT]
> Por predefinição, o seu cluster utiliza o [equilíbrio de carga SKU Padrão][standard-sku-lb] que pode utilizar para configurar o gateway de saída. Quando ativa as gamas IP autorizadas pelo servidor API durante a criação do cluster, o IP público para o seu cluster também é permitido por padrão, além das gamas que especifica. Se especificar *""* ou nenhum valor para *--api-server-server-autorizado-ip-intervalos,* os intervalos IP autorizados pelo servidor API serão desativados.

O exemplo seguinte cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup* com gamas IP autorizadas pelo servidor API ativadas. As gamas de endereços IP permitidas são *73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Deve adicionar estas gamas a uma lista de autorizações:
> - O endereço IP público firewall
> - Qualquer gama que represente redes que você vai administrar o cluster de
> - Se estiver a utilizar espaços Azure Dev no seu cluster AKS, tem de permitir [gamas adicionais baseadas na sua região.][dev-spaces-ranges]

> O limite superior para o número de gamas IP que pode especificar é de 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Especifique os IPs de saída para o equilibrador de carga SKU padrão

Ao criar um cluster AKS, se especificar os endereços IP de saída ou prefixos para o cluster, esses endereços ou prefixos também são permitidos. Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

No exemplo acima, todos os IPs fornecidos no parâmetro *--balancer-out-out-ip-prefixos* são permitidos juntamente com os IP no parâmetro *--api-server-autorizado-ip-intervalos.*

Em alternativa, pode especificar o parâmetro *--load-balancer-out-ip-prefixos* para permitir prefixos IP do equilíbrio de carga de saída.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Permitir apenas o IP público de saída do equilibrador de carga SKU Padrão

Quando ativa as gamas IP autorizadas pelo servidor API durante a criação do cluster, o IP público de saída para o equilíbrio de carga SKU Padrão para o seu cluster também é permitido por padrão, além das gamas que especifica. Para permitir apenas o IP público de saída do equilibrador de carga SKU Padrão, utilize *0.0.0.0.0/32* ao especificar o parâmetro *--api-server-autorizado-ip-intervalos.*

No exemplo seguinte, apenas é permitido o IP público de saída do equilibrador de carga Standard SKU, e só pode aceder ao servidor API a partir dos nós dentro do cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Atualizar os intervalos ip autorizados do servidor API de um cluster

Para atualizar as gamas IP autorizadas pelo servidor API num cluster existente, utilize o comando de [atualização az aks][az-aks-update] e utilize os *parâmetros ---api-server-autorizado-ip-ip-intervalos*, *--load-balancer-outbound-ip-prefixos*, *--load-balancer-outbound-ips*, ou *---load-balancer-out-ip-prefixos.*

O exemplo seguinte atualiza as gamas IP autorizadas do servidor API no cluster denominado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. O intervalo de endereçoIP a autorizar é *73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Também pode utilizar *0.0.0.0/32* ao especificar o parâmetro *--api-server-autorizado-ip-intervalos* para permitir apenas o IP público do equilibrador de carga SKU Padrão.

## <a name="disable-authorized-ip-ranges"></a>Desativar gamas IP autorizadas

Para desativar as gamas IP autorizadas, utilize a [atualização az aks][az-aks-update] e especifique uma gama vazia para desativar as gamas IP autorizadas do servidor API. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, ativou os intervalos IP autorizados do servidor API. Esta abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte conceitos de [segurança para aplicações e clusters em AKS][concepts-security] e boas práticas para segurança de [clusters e upgrades em AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
