---
title: Utilizar um endereço IP estático com o Balanceador de carga do Azure Kubernetes Service (AKS)
description: Saiba como criar e utilizar um endereço IP estático com o Balanceador de carga do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408688"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Utilizar um endereço IP público estático com o Balanceador de carga do Azure Kubernetes Service (AKS)

Por predefinição, o endereço IP público atribuído a um recurso de Balanceador de carga criado por um cluster do AKS só é válido para o tempo de vida desse recurso. Se eliminar o serviço do Kubernetes, o Balanceador de carga associado e o endereço IP também são eliminados. Se pretender atribuir um endereço IP específico ou manter um endereço IP para reimplantados serviços do Kubernetes, pode criar e utilizar um endereço IP público estático.

Este artigo mostra-lhe como criar um endereço IP público estático e atribuí-lo para o serviço do Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

Atualmente, só *SKU de IP básico*é suportada. Trabalho está em curso para suportar o *Standard IP* recursos SKU. Para obter mais informações, consulte [tipos e métodos de alocação no Azure de endereços IP][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Quando cria um endereço IP público estático para utilização com o AKS, o recurso de endereço IP deve ser criado no **nó** grupo de recursos. Se pretender separar os recursos, consulte a secção seguinte para [utilizar um endereço IP estático fora do grupo de recursos de nó](#use-a-static-ip-address-outside-of-the-node-resource-group).

Primeiro, obtenha o nome de grupo de recursos de nó com o [show do az aks] [ az-aks-show] comando e adicione o `--query nodeResourceGroup` parâmetro de consulta. O exemplo seguinte obtém o grupo de recursos de nó para o nome de cluster do AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

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
    [...]
  }
}
```

Pode obter mais tarde utilizando endereços IP públicos a [lista de public-ip de rede de az] [ az-network-public-ip-list] comando. Especifique o nome do grupo de recursos de nó e endereço IP público que criou e consulta para o *ipAddress* conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço com o endereço IP estático

Para criar um serviço com o endereço IP público estático, adicione o `loadBalancerIP` propriedade e o valor do IP público estático de endereços para o manifesto YAML. Crie um ficheiro denominado `load-balancer-service.yaml` e copie o YAML seguinte. Fornece seu próprio endereço IP público criado no passo anterior.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Criar o serviço e a implantação com o `kubectl apply` comando.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Utilizar um endereço IP estático fora do grupo de recursos de nó

Com o Kubernetes 1.10 ou posterior, pode utilizar um endereço IP estático, que é criado fora do grupo de recursos de nó. O principal de serviço utilizado pelo cluster do AKS tem de ter delegado permissões para outro grupo de recursos, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Para utilizar um endereço IP fora do grupo de recursos de nó, adicione uma anotação a definição do serviço. O exemplo seguinte define a anotação ao grupo de recursos com o nome *myResourceGroup*. Fornece seu próprio nome de grupo de recursos:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Resolução de problemas

Se o endereço IP estático definida no *loadBalancerIP* propriedade do manifesto do serviço de Kubernetes, não existe ou não tiver sido criada no grupo de recursos de nó e não existem delegações adicionais configuradas, o serviço de Balanceador de carga Falha ao criar. Para resolver problemas, reveja os eventos de criação de serviço com o [kubectl descrevem] [ kubectl-describe] comando. Forneça o nome do serviço, conforme especificado no manifesto YAML, conforme mostrado no exemplo a seguir:

```console
kubectl describe service azure-load-balancer
```

São apresentadas informações sobre o recurso de serviço do Kubernetes. O *eventos* no final da saída de exemplo seguintes indicam que a *utilizador não foi possível encontrar o endereço de IP fornecidos*. Nestes cenários, certifique-se de que criou o endereço IP público estático no grupo de recursos de nó e se o endereço IP especificado no manifesto do serviço de Kubernetes está correto.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Passos Seguintes

Para obter controlo adicional sobre o tráfego de rede às suas aplicações, pode querer em vez disso [criar um controlador de entrada][aks-ingress-basic]. Também pode [criar um controlador de entrada com um endereço IP público estático][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
