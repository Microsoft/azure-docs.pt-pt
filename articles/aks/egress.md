---
title: Endereço IP estático para o tráfego de saída no serviço de kubernetes do Azure (AKS)
description: Saiba como criar e usar um endereço IP público estático para o tráfego de saída em um cluster do serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 67471d688e64244067a7537bc87c379da4a69c03
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696365"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Usar um endereço IP público estático para o tráfego de saída no serviço de kubernetes do Azure (AKS)

Por padrão, o endereço IP de saída de um cluster do serviço de kubernetes do Azure (AKS) é atribuído aleatoriamente. Essa configuração não é ideal quando você precisa identificar um endereço IP para acesso a serviços externos, por exemplo. Em vez disso, talvez seja necessário atribuir um endereço IP estático que pode estar na lista de permissões para acesso ao serviço.

Este artigo mostra como criar e usar um endereço IP público estático para uso com o tráfego de saída em um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de saída

O tráfego de saída de um cluster AKS segue as [convenções de Azure Load Balancer][outbound-connections]. Antes que o primeiro serviço kubernetes do `LoadBalancer` tipo seja criado, os nós de agente em um cluster AKs não fazem parte de nenhum pool de Azure Load Balancer. Nessa configuração, os nós não têm nenhum endereço IP público em nível de instância. O Azure traduz o fluxo de saída para um endereço IP de origem público que não é configurável ou determinístico.

Depois que um serviço kubernetes do `LoadBalancer` tipo é criado, nós de agente são adicionados a um pool de Azure Load Balancer. Para o fluxo de saída, o Azure converte-o para o primeiro endereço IP público configurado no balanceador de carga. Esse endereço IP público só é válido durante o ciclo de vida desse recurso. Se você excluir o serviço de balanceador de kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou reter um endereço IP para serviços kubernetes reimplantados, poderá criar e usar um endereço IP público estático.

## <a name="create-a-static-public-ip"></a>Criar um IP público estático

Obtenha o nome do grupo de recursos com o comando [AZ AKs show][az-aks-show] e `--query nodeResourceGroup` adicione o parâmetro de consulta. O exemplo a seguir obtém o grupo de recursos do nó para o nome do cluster AKS *myAKSCluster* no nome do grupo de recursos MyResource Group:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um endereço IP público estático com o comando [AZ Network Public IP Create][az-network-public-ip-create] . Especifique o nome do grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso de endereço IP, como *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é mostrado, conforme mostrado na seguinte saída de exemplo condensada:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Posteriormente, você poderá obter o endereço IP público usando o comando [AZ Network Public-IP List][az-network-public-ip-list] . Especifique o nome do grupo de recursos do nó e, em seguida, consulte o *ipAddress* , conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Criar um serviço com o IP estático

Para criar um serviço com o endereço IP público estático, adicione a `loadBalancerIP` Propriedade e o valor do endereço IP público estático ao manifesto YAML. Crie um arquivo chamado `egress-service.yaml` e copie o seguinte YAML. Forneça seu próprio endereço IP público criado na etapa anterior.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Crie o serviço e a implantação com `kubectl apply` o comando.

```console
kubectl apply -f egress-service.yaml
```

Esse serviço configura um novo IP de front-end no Azure Load Balancer. Se você não tiver nenhum outro IPs configurado, **todo** o tráfego de saída agora deverá usar esse endereço. Quando vários endereços são configurados no Azure Load Balancer, a saída usa o primeiro IP nesse balanceador de carga.

## <a name="verify-egress-address"></a>Verificar endereço de saída

Para verificar se o endereço IP público estático está sendo usado, você pode usar o serviço `checkip.dyndns.org`de pesquisa de DNS, como.

Iniciar e anexar a um pod básico de *Debian* :

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Para acessar um site de dentro do contêiner, use `apt-get` para instalar `curl` no contêiner.

```console
apt-get update && apt-get install curl -y
```

Agora, use a rotação para acessar o site *checkip.dyndns.org* . O endereço IP de saída é mostrado, conforme exibido na saída de exemplo a seguir. Esse endereço IP corresponde ao endereço IP público estático criado e definido para o serviço de balanceador de carga:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Passos Seguintes

Para evitar a manutenção de vários endereços IP públicos na Azure Load Balancer, você pode usar um controlador de entrada. Controladores de entrada fornecem benefícios adicionais, como terminação SSL/TLS, suporte para regravações de URI e criptografia SSL/TLS upstream. Para obter mais informações, consulte [criar um controlador de entrada básico em AKs][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
