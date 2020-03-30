---
title: Endereço IP estático para tráfego de egress no Serviço Azure Kubernetes (AKS)
description: Aprenda a criar e usar um endereço IP público estático para o tráfego de saída num cluster do Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 5850f8dfc08ed80dfe5e5e13f49808c3fd9338c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595761"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Utilize um endereço IP público estático para o tráfego de egress no Serviço Azure Kubernetes (AKS)

Por padrão, o endereço IP de saída de um cluster do Serviço Azure Kubernetes (AKS) é atribuído aleatoriamente. Esta configuração não é a ideal quando é necessário identificar um endereço IP para acesso a serviços externos, por exemplo. Em vez disso, poderá ter de atribuir um endereço IP estático que pode ser listado em branco para acesso ao serviço.

Este artigo mostra-lhe como criar e usar um endereço IP público estático para uso com tráfego de saída num cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de Egress

O tráfego de saída de um cluster AKS segue [as convenções do Azure Load Balancer.][outbound-connections] Antes de o primeiro serviço `LoadBalancer` de tipo Kubernetes ser criado, os nós de agente num cluster AKS não fazem parte de qualquer piscina azure Load Balancer. Nesta configuração, os nós não têm endereço IP público de nível de instância. O Azure traduz o fluxo de saída para um endereço IP de origem pública que não é configurável ou determinista.

Uma vez criado um serviço `LoadBalancer` kubernetes do tipo, os nós de agente são adicionados a uma piscina Azure Load Balancer. Para o fluxo de saída, o Azure traduz-o para o primeiro endereço IP público configurado no equilibrador de carga. Este endereço IP público é válido para a vida útil desse recurso. Se eliminar o serviço Kubernetes LoadBalancer, o balancedor de carga associado e o endereço IP também são eliminados. Se pretender atribuir um endereço IP específico ou reter um endereço IP para serviços Kubernetes reimplantados, pode criar e utilizar um endereço IP público estático.

## <a name="create-a-static-public-ip"></a>Criar um IP público estático

Obtenha o nome do grupo de recursos com `--query nodeResourceGroup` o comando [az aks mostrar][az-aks-show] e adicionar o parâmetro de consulta. O exemplo seguinte obtém o grupo de recursos do nó para o nome de cluster AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora crie um endereço IP público estático com a [rede az public ip criar][az-network-public-ip-create] comando. Especifique o nome do grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso de endereço IP, como *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é mostrado, como mostra a seguinte saída de exemplo condensado:

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

Mais tarde, pode obter o endereço IP público utilizando o comando da lista ip pública da [rede Az.][az-network-public-ip-list] Especifique o nome do grupo de recursos do nó e, em seguida, consulta para o *ipAddress,* como mostrado no seguinte exemplo:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Criar um serviço com o IP estático

Para criar um serviço com o endereço `loadBalancerIP` IP público estático, adicione o imóvel e o valor do endereço IP público estático ao manifesto YAML. Crie um `egress-service.yaml` ficheiro nomeado e copie no seguinte YAML. Forneça o seu próprio endereço IP público criado no passo anterior.

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

Crie o serviço `kubectl apply` e a implantação com o comando.

```console
kubectl apply -f egress-service.yaml
```

Este serviço configura um novo IP frontend no Azure Load Balancer. Se não tiver outros IPs configurados, **todo o** tráfego deve agora utilizar este endereço. Quando vários endereços são configurados no Equilíbrio de Carga Azure, a saída utiliza o primeiro IP nesse equilíbrio de carga.

## <a name="verify-egress-address"></a>Verifique o endereço de saída

Para verificar se o endereço IP público estático está a ser `checkip.dyndns.org`utilizado, pode utilizar o serviço de reparação dNS, tais como .

Iniciar e fixar a uma cápsula *básica de Debian:*

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Para aceder a um web site `apt-get` a `curl` partir do recipiente, utilize para instalar no recipiente.

```console
apt-get update && apt-get install curl -y
```

Agora use caracóis para aceder ao site *checkip.dyndns.org.* O endereço IP de saída é mostrado, como mostrado na saída de exemplo seguinte. Este endereço IP corresponde ao endereço IP público estático criado e definido para o serviço loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Passos seguintes

Para evitar a manutenção de vários endereços IP públicos no Equilíbrio de Carga Azure, pode utilizar um controlador de entrada. Os controladores de ingressos proporcionam benefícios adicionais, tais como a rescisão ssl/TLS, suporte para reescritas URI e encriptação SSL/TLS a montante. Para mais informações, consulte Criar um controlador básico de [ingresso no AKS][ingress-aks-cluster].

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
