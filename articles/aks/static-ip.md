---
title: Usar um endereço IP estático com o balanceador de carga do AKS (serviço kubernetes do Azure)
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (serviço kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/06/2019
ms.author: mlearned
ms.openlocfilehash: 8457f1c0c5b6107c4b44f6f00236a33f7c67452a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325448"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar um endereço IP público estático com o balanceador de carga do AKS (serviço kubernetes do Azure)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster AKS só é válido durante o ciclo de vida desse recurso. Se você excluir o serviço kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou reter um endereço IP para serviços kubernetes reimplantados, poderá criar e usar um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

Este artigo aborda o uso de um IP de SKU *padrão* com um balanceador de carga SKU *padrão* . Para obter mais informações, consulte [tipos de endereço IP e métodos de alocação no Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Crie um endereço IP público estático com o comando [AZ Network Public IP Create][az-network-public-ip-create] . O seguinte cria um recurso de IP estático chamado *myAKSPublicIP* no grupo de recursos *MyResource* Group:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Se você estiver usando um balanceador de carga de SKU *básico* em seu cluster AKs, use *básico* para o parâmetro *SKU* ao definir um IP público. Somente os IPs de SKU *básicos* funcionam com o balanceador de carga de SKU *básico* e somente os IPs de SKU *padrão* funcionam com balanceadores de carga SKU *padrão* . 

O endereço IP é exibido, conforme mostrado na seguinte saída de exemplo condensada:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Posteriormente, você poderá obter o endereço IP público usando o comando [AZ Network Public-IP List][az-network-public-ip-list] . Especifique o nome do grupo de recursos do nó e o endereço IP público que você criou e consulte o *ipAddress* , conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Antes de criar um serviço, verifique se a entidade de serviço usada pelo cluster AKS tem permissões delegadas para outro grupo de recursos. Por exemplo:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Para criar um serviço *Balancer* com o endereço IP público estático, adicione a propriedade `loadBalancerIP` e o valor do endereço IP público estático ao manifesto YAML. Crie um arquivo chamado `load-balancer-service.yaml` e copie o YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior. O exemplo a seguir também define a anotação para o grupo de recursos chamado *MyResource*Group. Forneça seu próprio nome de grupo de recursos.

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

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
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

## <a name="next-steps"></a>Passos seguintes

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
