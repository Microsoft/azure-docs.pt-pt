---
title: Endereço IP estático para o tráfego de saída no Azure Kubernetes Service (AKS)
description: Saiba como criar e utilizar um endereço IP público estático para o tráfego de saída num cluster do Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: 6612d801804cdd1e092b50977230f24b378e64ba
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407141"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Utilizar um endereço IP público estático para o tráfego de saída no Azure Kubernetes Service (AKS)

Por predefinição, o endereço de IP de saída de um cluster do Azure Kubernetes Service (AKS) é atribuído aleatoriamente. Esta configuração não é ideal quando precisa identificar um endereço IP para o acesso a serviços externos, por exemplo. Em vez disso, terá de atribuir um endereço IP estático, que pode estar na lista de permissões para acesso ao serviço.

Este artigo mostra-lhe como criar e utilizar um endereço IP público estático para utilização com o tráfego de saída num cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="egress-traffic-overview"></a>Descrição geral do tráfego de saída

Segue-se o tráfego de saída de um cluster do AKS [convenções de Balanceador de carga do Azure][outbound-connections]. Antes do primeiro serviço Kubernetes do tipo `LoadBalancer` é criado, o agente de nós num cluster do AKS não fazem parte de qualquer conjunto de Balanceador de carga do Azure. Nesta configuração, os nós ter nenhum endereço IP público de nível de instância. Azure traduz-se o fluxo de saída para um endereço IP público de origem que não seja configurável ou determinística.

Uma vez um serviço de Kubernetes do tipo `LoadBalancer` é criado, o agente de nós são adicionados a um conjunto de Balanceador de carga do Azure. Fluxo de saída, Azure traduz-se o primeiro endereço IP público configurado no balanceador de carga. Este endereço IP público só é válido para o tempo de vida desse recurso. Se eliminar o serviço de LoadBalancer do Kubernetes, o Balanceador de carga associado e o endereço IP também são eliminados. Se pretender atribuir um endereço IP específico ou manter um endereço IP para reimplantados serviços do Kubernetes, pode criar e utilizar um endereço IP público estático.

## <a name="create-a-static-public-ip"></a>Criar um IP público estático

Quando cria um endereço IP público estático para utilização com o AKS, o recurso de endereço IP tem de ser criado no **nó** grupo de recursos. Obtenha o nome do grupo de recursos com o [show do az aks] [ az-aks-show] comando e adicione o `--query nodeResourceGroup` parâmetro de consulta. O exemplo seguinte obtém o grupo de recursos de nó para o nome de cluster do AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um endereço IP público estático com o [criar ip público de rede de az] [ az-network-public-ip-create] comando. Especifique o nome de grupo de recursos de nó obteve no comando anterior e, em seguida, um nome para o IP cobrem recursos, como *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é apresentado, conforme mostrado no seguinte exemplo condensado:

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

Pode obter mais tarde utilizando endereços IP públicos a [lista de public-ip de rede de az] [ az-network-public-ip-list] comando. Especifique o nome do grupo de recursos de nó e, em seguida, consultar para o *ipAddress* conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Criar um serviço com o IP estático

Para criar um serviço com o endereço IP público estático, adicione o `loadBalancerIP` propriedade e o valor do IP público estático de endereços para o manifesto YAML. Crie um ficheiro denominado `egress-service.yaml` e copie o YAML seguinte. Fornece seu próprio endereço IP público criado no passo anterior.

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

Criar o serviço e a implantação com o `kubectl apply` comando.

```console
kubectl apply -f egress-service.yaml
```

Este serviço configura um novo IP de front-end do Balanceador de carga do Azure. Se não tiver qualquer outro IPs configurada, em seguida, **todos os** o tráfego de saída deve, agora, utilize este endereço. Quando vários endereços são configurados no balanceador de carga do Azure, saída utiliza o IP primeiro nesse Balanceador de carga.

## <a name="verify-egress-address"></a>Verifique se o endereço de saída

Para verificar se está a ser utilizado o endereço IP público estático, pode utilizar o serviço de pesquisa DNS como `checkip.dyndns.org`.

Iniciar e anexar a um basic *Debian* pod:

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Para aceder a um site de dentro do contentor, utilize `apt-get` para instalar `curl` para o contentor.

```console
apt-get update && apt-get install curl -y
```

Agora utilize o curl para aceder a *checkip.dyndns.org* site. O endereço de IP de saída é mostrado, tal como apresentado na seguinte saída de exemplo. O endereço IP público estático criado e definido para o serviço de loadBalancer corresponde a este endereço IP:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Passos Seguintes

Para evitar a manutenção de vários endereços IP públicos no balanceador de carga do Azure, pode usar um controlador de entrada. Controladores de entrada fornecem benefícios adicionais, tais como a terminação de SSL/TLS, o suporte para reescritas URI e a encriptação de SSL/TLS a montante. Para obter mais informações, consulte [criar um controlador de entrada básico no AKS][ingress-aks-cluster].

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