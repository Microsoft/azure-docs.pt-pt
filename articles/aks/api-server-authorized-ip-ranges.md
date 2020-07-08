---
title: Gamas IP autorizadas do servidor API no Serviço Azure Kubernetes (AKS)
description: Saiba como proteger o seu cluster utilizando uma gama de endereços IP para acesso ao servidor API no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 4d9030e21c3b8f31c18c26fc54dc76d5b8d84a17
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85100058"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Acesso seguro ao servidor API utilizando intervalos de endereços IP autorizados no Serviço Azure Kubernetes (AKS)

Em Kubernetes, o servidor API recebe pedidos para realizar ações no cluster, tais como criar recursos ou escalar o número de nós. O servidor API é a forma central de interagir e gerir um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor API só deve estar acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra como utilizar os intervalos de endereços IP autorizados pelo servidor API para limitar quais endereços IP e CIDRs podem aceder ao plano de controlo.

> [!IMPORTANT]
> Em novos clusters, os intervalos de endereços IP autorizados pelo servidor API são suportados apenas no balanceador de carga *Standard* SKU. Os clusters existentes com o balanceador de carga *Basic* SKU e os intervalos de endereço IP autorizados pelo servidor API configurados continuarão a funcionar como está, mas não podem ser migrados para um balanceador de carga *SKU padrão.* Os clusters existentes também continuarão a funcionar se a sua versão Kubernetes ou o seu plano de controlo forem atualizados.

## <a name="before-you-begin"></a>Before you begin

Este artigo mostra-lhe como criar um cluster AKS usando o Azure CLI.

Precisa da versão 2.0.76 do Azure CLI ou posteriormente instalada e configurada. Corre  `az --version` para encontrar a versão. Se necessitar de instalar ou atualizar, consulte [instalar o Azure CLI][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral dos intervalos de IP autorizados pelo servidor API

O servidor API de Kubernetes é como as APIs subjacentes são expostas. Este componente proporciona a interação para ferramentas de gestão, tais como `kubectl` ou o dashboard Kubernetes. A AKS fornece um mestre de cluster de inquilino único, com um servidor API dedicado. Por predefinição, o servidor API é atribuído a um endereço IP público, e deve controlar o acesso usando controlos de acesso baseados em funções (RBAC).

Para garantir o acesso ao avião de controlo AKS /servidor API acessível ao público, pode ativar e utilizar gamas IP autorizadas. Estes intervalos de IP autorizados apenas permitem que intervalos de endereços IP definidos se comuniquem com o servidor API. Um pedido feito ao servidor API a partir de um endereço IP que não faz parte destes intervalos de IP autorizados está bloqueado. Continue a utilizar o RBAC para autorizar os utilizadores e as ações que solicitam.

Para obter mais informações sobre o servidor API e outros componentes do cluster, consulte [os conceitos centrais de Kubernetes para AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Criar um cluster AKS com gamas IP autorizadas por servidor API ativadas

Os intervalos de IP autorizados pelo servidor API apenas funcionam para novos clusters AKS e não são suportados para clusters privados AKS. Crie um cluster utilizando as [az aks criar][az-aks-create] e especificar o *`--api-server-authorized-ip-ranges`* parâmetro para fornecer uma lista de intervalos de endereços IP autorizados. Estes intervalos de endereços IP são geralmente intervalos de endereços utilizados pelas suas redes no local ou iPs públicos. Quando especificar uma gama CIDR, comece com o primeiro endereço IP na gama. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP na gama, como *137.117.106.88/29*.

> [!IMPORTANT]
> Por predefinição, o seu cluster utiliza o [balanceador de carga Standard SKU][standard-sku-lb] que pode utilizar para configurar o gateway de saída. Quando ativa os intervalos IP autorizados pelo servidor API durante a criação do cluster, o IP público do seu cluster também é permitido por padrão, além das gamas especificadas. Se especificar *""* ou nenhum valor para *`--api-server-authorized-ip-ranges`* , os intervalos IP autorizados do servidor API serão desativados. Note que se estiver a utilizar o PowerShell, utilize *`--api-server-authorized-ip-ranges=""`* (com sinal igual) para evitar problemas de análise.

O exemplo a seguir cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup* com gamas IP autorizadas por servidor API ativadas. Os intervalos de endereços IP permitidos são *73.140.245.0/24*:

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
> Deve adicionar estas gamas a uma lista de admissões:
> - O endereço IP público firewall
> - Qualquer alcance que represente redes a partir de
> - Se estiver a utilizar espaços Azure Dev no seu cluster AKS, tem de permitir [gamas adicionais com base na sua região.][dev-spaces-ranges]

> O limite superior para o número de intervalos de IP que pode especificar é de 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Especificar os IPs de saída para o balanceador de carga SKU Standard

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

No exemplo acima, todos os IPs fornecidos no parâmetro *`--load-balancer-outbound-ip-prefixes`* são permitidos juntamente com os IPs no *`--api-server-authorized-ip-ranges`* parâmetro.

Em alternativa, pode especificar o *`--load-balancer-outbound-ip-prefixes`* parâmetro para permitir prefixos IP do balançador de carga de saída.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Permitir apenas o IP público de saída do balanceador de carga Standard SKU

Quando ativa as gamas IP autorizadas pelo servidor API durante a criação do cluster, o IP público de saída para o balanceador de carga Standard SKU para o seu cluster também é permitido por padrão, além das gamas especificadas. Para permitir apenas o IP público de saída do balanceador de carga Standard SKU, utilize *0.0.0.0/32* ao especificar o *`--api-server-authorized-ip-ranges`* parâmetro.

No exemplo seguinte, apenas é permitido o IP público de saída do balanceador de carga Standard SKU, e só é possível aceder ao servidor API a partir dos nós dentro do cluster.

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

Para atualizar os intervalos IP autorizados pelo servidor API num cluster existente, utilize o comando [de atualização az aks][az-aks-update] e utilize os *`--api-server-authorized-ip-ranges`* parâmetros ,---balanceador-saldo-prefixos de saída-ip-* *`--load-balancer-outbound-ips`* ou-load-balancer-out-out-ip-prefixes.*

O exemplo seguinte atualiza gamas de IP autorizadas no cluster denominado *myAKSCluster* no grupo de recursos denominado *myResourceGroup*. O intervalo de endereços IP a autorizar é *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Também pode utilizar *0.0.0.0.0/32* ao especificar o *`--api-server-authorized-ip-ranges`* parâmetro para permitir apenas o IP público do balançador de carga Standard SKU.

## <a name="disable-authorized-ip-ranges"></a>Desativar os intervalos de IP autorizados

Para desativar os intervalos de IP autorizados, utilize [a atualização az aks][az-aks-update] e especifique um intervalo vazio para desativar os intervalos ip autorizados do servidor API. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Próximos passos

Neste artigo, ativou gamas IP autorizadas pelo servidor API. Esta abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicações e clusters em AKS][concepts-security] e [melhores práticas para segurança de clusters e upgrades em AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
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
