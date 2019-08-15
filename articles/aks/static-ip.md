---
title: Usar um endereço IP estático com o balanceador de carga do AKS (serviço kubernetes do Azure)
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (serviço kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614474"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar um endereço IP público estático com o balanceador de carga do AKS (serviço kubernetes do Azure)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster AKS só é válido durante o ciclo de vida desse recurso. Se você excluir o serviço kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou reter um endereço IP para serviços kubernetes reimplantados, poderá criar e usar um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

Atualmente, apenas o *SKU de IP básico*tem suporte. O trabalho está em andamento para dar suporte ao SKU do recurso *IP padrão* . Para obter mais informações, consulte [tipos de endereço IP e métodos de alocação no Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Quando você cria um endereço IP público estático para uso com AKS, o recurso de endereço IP deve ser criado no grupo de recursos do **nó** . Se você quiser separar os recursos, consulte a seção a seguir para [usar um endereço IP estático fora do grupo de recursos do nó](#use-a-static-ip-address-outside-of-the-node-resource-group).

Primeiro, obtenha o nome do grupo de recursos do nó com o comando [AZ AKs show][az-aks-show] e adicione o parâmetro de `--query nodeResourceGroup` consulta. O exemplo a seguir obtém o grupo de recursos do nó para o nome do cluster AKS *myAKSCluster* no nome do grupo de recursos MyResource Group:

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

O endereço IP é exibido, conforme mostrado na seguinte saída de exemplo condensada:

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

Posteriormente, você poderá obter o endereço IP público usando o comando [AZ Network Public-IP List][az-network-public-ip-list] . Especifique o nome do grupo de recursos do nó e o endereço IP público que você criou e consulte o *ipAddress* , conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Para criar um serviço com o endereço IP público estático, adicione a `loadBalancerIP` Propriedade e o valor do endereço IP público estático ao manifesto YAML. Crie um arquivo chamado `load-balancer-service.yaml` e copie o seguinte YAML. Forneça seu próprio endereço IP público criado na etapa anterior.

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

Crie o serviço e a implantação com `kubectl apply` o comando.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Usar um endereço IP estático fora do grupo de recursos do nó

Com o kubernetes 1,10 ou posterior, você pode usar um endereço IP estático que é criado fora do grupo de recursos do nó. A entidade de serviço usada pelo cluster AKS deve ter permissões delegadas para outro grupo de recursos, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Para usar um endereço IP fora do grupo de recursos do nó, adicione uma anotação à definição do serviço. O exemplo a seguir define a anotação para o grupo derecursos chamado MyResource Group. Forneça seu próprio nome de grupo de recursos:

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

Se o endereço IP estático definido na propriedade *loadBalancerIP* do manifesto do serviço kubernetes não existir ou não tiver sido criado no grupo de recursos do nó e nenhuma delegação adicional configurada, a criação do serviço do balanceador de carga falhará. Para solucionar problemas, examine os eventos de criação de serviço com o comando [kubectl descrevem][kubectl-describe] . Forneça o nome do serviço conforme especificado no manifesto YAML, conforme mostrado no exemplo a seguir:

```console
kubectl describe service azure-load-balancer
```

As informações sobre o recurso de serviço kubernetes são exibidas. Os *eventos* no final da saída de exemplo a seguir indicam que o *endereço IP fornecido pelo usuário não foi encontrado*. Nesses cenários, verifique se você criou o endereço IP público estático no grupo de recursos do nó e se o endereço IP especificado no manifesto do serviço kubernetes está correto.

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

Para obter controle adicional sobre o tráfego de rede para seus aplicativos, talvez você queira [criar um controlador de entrada][aks-ingress-basic]. Você também pode [criar um controlador de entrada com um endereço IP público estático][aks-static-ingress].

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
