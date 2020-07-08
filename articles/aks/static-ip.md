---
title: Use IP estático com equilibrador de carga
titleSuffix: Azure Kubernetes Service
description: Saiba como criar e utilizar um endereço IP estático com o balançador de carga Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 5051232f29ad51d9fee893a4a660fc81f6e60d77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80886743"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Utilize um endereço IP público estático e etiqueta DNS com o equilibrador de carga Azure Kubernetes Service (AKS)

Por predefinição, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster AKS é válido apenas para o tempo de vida desse recurso. Se eliminar o serviço Kubernetes, o equilibrador de carga associado e o endereço IP também serão eliminados. Se pretender atribuir um endereço IP específico ou reter um endereço IP para serviços de Kubernetes redistribuídos, pode criar e utilizar um endereço IP público estático.

Este artigo mostra-lhe como criar um endereço IP público estático e atribuí-lo ao seu serviço Kubernetes.

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Corre  `az --version` para encontrar a versão. Se necessitar de instalar ou atualizar, consulte [instalar o Azure CLI][install-azure-cli].

Este artigo abrange utilizando um *IP Standard* SKU com um *balanceador de* carga Standard SKU. Para obter mais informações, consulte [os tipos de endereços IP e os métodos de atribuição em Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Crie um endereço IP público estático com o comando [ip público da rede az.][az-network-public-ip-create] O seguinte cria um recurso IP estático chamado *myAKSPublicIP* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Se estiver a utilizar um *balanceador de* carga Basic SKU no seu cluster AKS, utilize *Basic* para o parâmetro *sku* ao definir um IP público. Apenas os IPs *Básicos* SKU funcionam com o *balanceador de* carga Basic SKU e apenas os IPs *Standard* SKU funcionam com os equilibradores de carga *SKU padrão.* 

O endereço IP é apresentado, como mostra a seguinte saída de exemplo condensado:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Mais tarde, pode obter o endereço IP público utilizando o comando [da lista de ip público da rede Az.][az-network-public-ip-list] Especifique o nome do grupo de recursos de nó e endereço IP público que criou e consulta para o *ipAddress* como mostrado no exemplo seguinte:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço utilizando o endereço IP estático

Antes de criar um serviço, certifique-se de que o principal de serviço utilizado pelo cluster AKS delegou permissões ao outro grupo de recursos. Por exemplo:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Em alternativa, pode utilizar o sistema atribuído à identidade gerida para permissões em vez do principal serviço. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md)

Para criar um serviço *LoadBalancer* com o endereço IP público estático, adicione o `loadBalancerIP` imóvel e o valor do endereço IP público estático ao manifesto YAML. Crie um ficheiro nomeado `load-balancer-service.yaml` e copie no seguinte YAML. Forneça o seu próprio endereço IP público criado no passo anterior. O exemplo a seguir também define a anotação para o grupo de recursos chamado *myResourceGroup*. Forneça o seu próprio nome de grupo de recursos.

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

Crie o serviço e a implantação com o `kubectl apply` comando.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Aplicar uma etiqueta DNS ao serviço

Se o seu serviço estiver a utilizar um endereço IP público dinâmico ou estático, pode utilizar a anotação de serviço `service.beta.kubernetes.io/azure-dns-label-name` para definir uma etiqueta DNS virada para o público. Isto publica um nome de domínio totalmente qualificado para o seu serviço usando os servidores DNS públicos da Azure e o domínio de alto nível. O valor de anotação deve ser único dentro da localização Azure, por isso é recomendado usar uma etiqueta suficientemente qualificada.   

O Azure irá então anexar automaticamente uma sub-rede predefinida, como `<location>.cloudapp.azure.com` (onde a localização é a região selecionada), ao nome que fornece, para criar o nome DNS totalmente qualificado. Por exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Para publicar o serviço no seu próprio domínio, consulte [o Azure DNS][azure-dns-zone] e o projeto [external-dns.][external-dns]

## <a name="troubleshoot"></a>Resolução de problemas

Se o endereço IP estático definido na propriedade *loadBalancerIP* do manifesto de serviço Kubernetes não existir, ou não tiver sido criado no grupo de recursos de nó e nenhuma delegação adicional configurada, a criação do serviço de balançador de carga falha. Para resolver problemas, reveja os eventos de criação de serviço com o comando [de descrever kubectl.][kubectl-describe] Fornecer o nome do serviço conforme especificado no manifesto YAML, como mostra o seguinte exemplo:

```console
kubectl describe service azure-load-balancer
```

São apresentadas informações sobre o recurso de serviço Kubernetes. Os *Eventos* no final da seguinte produção de exemplo indicam que o *endereço IP fornecido pelo utilizador não foi encontrado*. Nestes cenários, verifique se criou o endereço IP público estático no grupo de recursos de nó e que o endereço IP especificado no manifesto de serviço Kubernetes está correto.

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

## <a name="next-steps"></a>Próximos passos

Para um controlo adicional sobre o tráfego da rede para as suas aplicações, poderá antes [pretender criar um controlador de entrada][aks-ingress-basic]. Também pode [criar um controlador de entrada com um endereço IP público estático][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
