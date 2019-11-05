---
title: Intervalos de IP autorizados do servidor de API no serviço kubernetes do Azure (AKS)
description: Saiba como proteger seu cluster usando um intervalo de endereços IP para acessar o servidor de API no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472966"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteger o acesso ao servidor de API usando intervalos de endereços IP autorizados no serviço de kubernetes do Azure (AKS)

No kubernetes, o servidor de API recebe solicitações para executar ações no cluster, como para criar recursos ou dimensionar o número de nós. O servidor de API é a maneira central de interagir com e gerenciar um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor de API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra como usar intervalos de endereços IP autorizados do servidor de API para limitar quais endereços IP e CIDR podem acessar o plano de controle.

> [!IMPORTANT]
> Em novos clusters, os intervalos de endereços IP autorizados do servidor de API só têm suporte no balanceador de carga SKU *padrão* . Os clusters existentes com o balanceador de carga de SKU *básico* e os intervalos de endereços IP autorizados do servidor de API configurados continuarão funcionando como estão. Esses clusers existentes também podem ser atualizados e eles continuarão a funcionar.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você está trabalhando com clusters que usam [kubenet][kubenet].  Com os clusters baseados em [CNI (interface de rede de contêiner do Azure)][cni-networking] , você não terá a tabela de rotas necessária para proteger o acesso.  Você precisará criar a tabela de rotas manualmente.  Consulte [Gerenciando tabelas de rotas](https://docs.microsoft.com/azure/virtual-network/manage-route-table) para obter mais informações.

Os intervalos de IP autorizados do servidor de API só funcionam para novos clusters AKS que você criar. Este artigo mostra como criar um cluster AKS usando o CLI do Azure.

Você precisa do CLI do Azure versão 2.0.76 ou posterior instalado e configurado. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você configura intervalos de IP autorizados do servidor de API:

* No momento, não é possível usar Azure Dev Spaces porque a comunicação com o servidor de API também está bloqueada.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral dos intervalos de IP autorizados do servidor de API

O servidor de API do kubernetes é como as APIs subjacentes do kubernetes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, como `kubectl` ou o painel kubernetes. O AKS fornece um mestre de cluster de locatário único, com um servidor de API dedicado. Por padrão, o servidor de API recebe um endereço IP público e você deve controlar o acesso usando controles de acesso baseado em função (RBAC).

Para proteger o acesso ao servidor do plano de controle AKS/API publicamente acessível, você pode habilitar e usar intervalos de IP autorizados. Esses intervalos de IP autorizados permitem que os intervalos de endereços IP definidos se comuniquem com o servidor de API. Uma solicitação feita ao servidor de API de um endereço IP que não faz parte desses intervalos de IP autorizado é bloqueada. Você deve continuar a usar o RBAC para autorizar os usuários e as ações que eles solicitam.

Para obter mais informações sobre o servidor de API e outros componentes de cluster, consulte [kubernetes Core Concepts for AKs][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Os intervalos de IP autorizados do servidor de API funcionam apenas para novos clusters AKS. Você não pode habilitar intervalos de IP autorizados como parte da operação de criação de cluster. Se você tentar habilitar intervalos de IP autorizados como parte do processo de criação do cluster, os nós de cluster não poderão acessar o servidor de API durante a implantação, pois o endereço IP de saída não está definido nesse ponto.

Primeiro, crie um cluster usando o comando [AZ AKs Create][az-aks-create] . O exemplo a seguir cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Atualizar cluster com intervalos de IP autorizados

Por padrão, o cluster usa o [balanceador de carga SKU padrão][standard-sku-lb], que pode ser usado para configurar o gateway de saída. Use [AZ Network Public-IP List][az-network-public-ip-list] e especifique o grupo de recursos do seu cluster AKs, que geralmente começa com *MC_* . Isso exibe o IP público para o cluster, que você pode permitir. Use o comando [AZ AKs Update][az-aks-update] e especifique o parâmetro *--API-Server-conauthorized-IP-Ranges* para permitir o IP do cluster. Por exemplo:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Para habilitar os intervalos de IP autorizados do servidor de API, use o comando [AZ AKs Update][az-aks-update] e especifique o parâmetro *--API-Server-Authorized-IP-Ranges* para fornecer uma lista de intervalos de endereços IP autorizados. Esses intervalos de endereços IP geralmente são intervalos de endereços usados por suas redes locais ou IPs públicos. Quando você especificar um intervalo CIDR, comece com o primeiro endereço IP no intervalo. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP no intervalo, como *137.117.106.88/29*.

O exemplo a seguir habilita os intervalos de IP autorizados do servidor de API no cluster chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group. Os intervalos de endereços IP a serem autorizados são *172.0.0.0/16* (intervalo de endereços Pod/nós) e *168.10.0.0/18* (CIDR):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Você deve adicionar esses intervalos a uma lista de permissões:
> - O endereço IP público do firewall
> - O CIDR do serviço
> - O intervalo de endereços para as sub-redes, com os nós e pods
> - Qualquer intervalo que represente as redes das quais você administrará o cluster

## <a name="disable-authorized-ip-ranges"></a>Desabilitar intervalos de IP autorizados

Para desabilitar intervalos IP autorizados, use [AZ AKs Update][az-aks-update] e especifique um intervalo vazio para desabilitar intervalos de IP autorizados do servidor de API. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você habilitou intervalos de IP autorizados do servidor de API. Essa abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicativos e clusters no AKs][concepts-security] e [as práticas recomendadas para segurança de cluster e atualizações no AKs][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
