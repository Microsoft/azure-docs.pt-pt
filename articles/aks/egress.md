---
title: Use IP estático para tráfego de saídas
titleSuffix: Azure Kubernetes Service
description: Saiba como criar e utilizar um endereço IP público estático para tráfego de saídas num cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: e1f81bf4c4d35108557449a8bebd126bdf744191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592375"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Utilize um endereço IP público estático para tráfego de saída com um balanceador de carga *SKU básico* no Serviço Azure Kubernetes (AKS)

Por predefinição, o endereço IP de saída de um cluster Azure Kubernetes Service (AKS) é atribuído aleatoriamente. Esta configuração não é ideal quando precisa de identificar um endereço IP para acesso a serviços externos, por exemplo. Em vez disso, poderá ter de atribuir um endereço IP estático para ser adicionado a uma lista de admissões para acesso ao serviço.

Este artigo mostra-lhe como criar e usar um endereço IP público estático para uso com tráfego de saída num cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que está a utilizar o Balançador de Carga Básica Azure.  Recomendamos a utilização do [Balançador de Carga Padrão Azure,](../load-balancer/load-balancer-overview.md)e pode utilizar funcionalidades mais avançadas para [controlar o tráfego de saídas AKS](./limit-egress-traffic.md).

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

> [!IMPORTANT]
> Este artigo utiliza o *balanceador de* carga Basic SKU com uma única piscina de nós. Esta configuração não está disponível para várias piscinas de nós, uma vez que o *balanceador de* carga Basic SKU não é suportado com várias piscinas de nós. Consulte [um balanceador de carga padrão público no Serviço Azure Kubernetes (AKS)][slb] para obter mais detalhes sobre a utilização do balançador de carga *Standard* SKU.

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de Egress

O tráfego de saída de um cluster AKS segue as [convenções do Azure Load Balancer][outbound-connections]. Antes da criação do primeiro serviço de tipo Kubernetes, os nós de `LoadBalancer` agente num cluster AKS não fazem parte de qualquer piscina Azure Load Balancer. Nesta configuração, os nós não têm endereço IP público de nível de exemplo. O Azure traduz o fluxo de saída para um endereço IP de fonte pública que não seja configurável ou determinístico.

Uma vez criado um serviço de tipo Kubernetes, os nós de `LoadBalancer` agente são adicionados a uma piscina Azure Load Balancer. Load Balancer Basic escolhe um único frontend para ser usado para fluxos de saída quando vários frontends IP (públicos) são candidatos para fluxos de saída. Esta seleção não é configurável, e você deve considerar o algoritmo de seleção como aleatório. Este endereço IP público é válido apenas para o tempo de vida desse recurso. Se eliminar o serviço Kubernetes LoadBalancer, o equilibrador de carga associado e o endereço IP também serão eliminados. Se pretender atribuir um endereço IP específico ou reter um endereço IP para serviços de Kubernetes redistribuídos, pode criar e utilizar um endereço IP público estático.

## <a name="create-a-static-public-ip"></a>Criar um IP público estático

Obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o `--query nodeResourceGroup` parâmetro de consulta. O exemplo a seguir obtém o grupo de recursos de nó para o nome de cluster AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora crie um endereço IP público estático com a [rede az via ip criar][az-network-public-ip-create] comando. Especificar o nome do grupo de recursos de nó obtido no comando anterior e, em seguida, um nome para o recurso de endereço IP, como *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é mostrado, como mostrado na seguinte saída de exemplo condensado:

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

Mais tarde, pode obter o endereço IP público utilizando o comando [da lista de ip público da rede Az.][az-network-public-ip-list] Especificar o nome do grupo de recursos de nó e, em seguida, consultar o *ipAddress* como mostrado no exemplo seguinte:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Criar um serviço com o IP estático

Para criar um serviço com o endereço IP público estático, adicione o `loadBalancerIP` imóvel e o valor do endereço IP público estático ao manifesto YAML. Crie um ficheiro nomeado `egress-service.yaml` e copie no seguinte YAML. Forneça o seu próprio endereço IP público criado no passo anterior.

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

Crie o serviço e a implantação com o `kubectl apply` comando.

```console
kubectl apply -f egress-service.yaml
```

Este serviço configura um novo FRONTend IP no Balançador de Carga Azure. Se não tiver outros IPs configurados, **todo** o tráfego de saída deve agora utilizar este endereço. Quando vários endereços são configurados no Balançador de Carga Azure, qualquer um destes endereços IP públicos é um candidato para fluxos de saída, e um é selecionado aleatoriamente.

## <a name="verify-egress-address"></a>Verifique o endereço de saída

Para verificar se o endereço IP público estático está a ser utilizado, pode utilizar o serviço de procuração DNS, como `checkip.dyndns.org` .

Iniciar e anexar a uma cápsula *de debian* básica:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Para aceder a um web site a partir do recipiente, utilize `apt-get` para instalar `curl` no recipiente.

```console
apt-get update && apt-get install curl -y
```

Agora use caracóis para aceder ao site *checkip.dyndns.org.* O endereço IP de saída é mostrado, como mostrado na saída de exemplo a seguir. Este endereço IP corresponde ao endereço IP público estático criado e definido para o serviço loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Passos seguintes

Para evitar a manutenção de vários endereços IP públicos no Balançador de Carga Azure, pode utilizar um controlador de entrada. Os controladores ingress fornecem benefícios adicionais, tais como a rescisão SSL/TLS, suporte para reescritas URI e encriptação SSL/TLS a montante. Para obter mais informações, consulte Criar um controlador básico de entrada [em AKS][ingress-aks-cluster].

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
[slb]: load-balancer-standard.md
