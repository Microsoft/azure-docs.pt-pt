---
title: Utilize um endereço IP estático e etiqueta DNS com o equilibrador de carga do Serviço Azure Kubernetes (AKS)
description: Aprenda a criar e utilizar um endereço IP estático com o equilibrador de carga do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 32889dbbcafd9510f8d04cb9c602d4802c6d1a1a
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943573"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Utilize um endereço IP público estático e etiqueta DNS com o equilibrador de carga do Serviço Azure Kubernetes (AKS)

Por predefinição, o endereço IP público atribuído a um recurso de equilíbrio de carga criado por um cluster AKS só é válido para o tempo de vida desse recurso. Se eliminar o serviço Kubernetes, o balanceador de carga associado e o endereço IP também são eliminados. Se pretender atribuir um endereço IP específico ou reter um endereço IP para serviços Kubernetes reimplantados, pode criar e utilizar um endereço IP público estático.

Este artigo mostra-lhe como criar um endereço IP público estático e atribuí-lo ao seu serviço Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

Este artigo cobre a utilização de um IP *SKU Standard* com um equilibrador de carga *SKU Padrão.* Para mais informações, consulte os tipos de [endereços IP e os métodos][ip-sku]de atribuição no Azure .

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Crie um endereço IP público estático com a [rede az public ip criar][az-network-public-ip-create] comando. O seguinte cria um recurso IP estático chamado *myAKSPublicIP* no grupo de recursos *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Se estiver a utilizar um equilíbrio de carga *SKU Básico* no seu cluster AKS, utilize *o Basic* para o parâmetro *sku* ao definir um IP público. Apenas Os IPs *Básicos* sKU funcionam com o equilíbrio de carga *SKU Básico* e apenas os IPs *SKU Standard* funcionam com os equilibradores de carga *SKU Standard.* 

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

Mais tarde, pode obter o endereço IP público utilizando o comando da lista ip pública da [rede Az.][az-network-public-ip-list] Especifique o nome do grupo de recursos do nó e endereço IP público que criou, e consulta para o *ipAddress,* como mostra o seguinte exemplo:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço utilizando o endereço IP estático

Antes de criar um serviço, certifique-se de que o principal de serviço utilizado pelo cluster AKS delegou permissões para o outro grupo de recursos. Por exemplo:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Para criar um serviço *LoadBalancer* com o endereço IP público estático, adicione o `loadBalancerIP` imóvel e o valor do endereço IP público estático ao manifesto YAML. Crie um ficheiro chamado `load-balancer-service.yaml` e copie no seguinte YAML. Forneça o seu próprio endereço IP público criado no passo anterior. O exemplo que se segue também define a anotação ao grupo de recursos chamado *myResourceGroup*. Forneça o seu próprio nome de grupo de recursos.

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

## <a name="apply-a-dns-label-to-the-service"></a>Aplicar uma etiqueta DNS no serviço

Se o seu serviço estiver a utilizar um endereço IP público dinâmico ou estático, pode utilizar a anotação de serviço `service.beta.kubernetes.io/azure-dns-label-name` para definir uma etiqueta DNS virada para o público. Isto publica um nome de domínio totalmente qualificado para o seu serviço utilizando os servidores Públicos DNS do Azure e domínio de alto nível. O valor da anotação deve ser único dentro da localização Azure, pelo que é recomendado utilizar uma etiqueta suficientemente qualificada.   

O Azure anexará automaticamente uma sub-rede predefinida, como `<location>.cloudapp.azure.com` (onde a localização é a região selecionada), ao nome que fornece, para criar o nome DNS totalmente qualificado. Por exemplo:

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
> Para publicar o serviço no seu próprio domínio, consulte [o Azure DNS][azure-dns-zone] e o projeto [external-DNS.][external-dns]

## <a name="troubleshoot"></a>Resolver Problemas

Se o endereço IP estático definido na propriedade *loadBalancerIP* do manifesto de serviço Kubernetes não existir, ou não tiver sido criado no grupo de recursos do nó e nenhuma delegação adicional configurada, a criação do serviço de equilíbrio de carga falha. Para resolver problemas, reveja os eventos de criação de serviço com o [kubectl descrever][kubectl-describe] o comando. Fornecer o nome do serviço conforme especificado no manifesto YAML, como mostra o seguinte exemplo:

```console
kubectl describe service azure-load-balancer
```

São apresentadas informações sobre o recurso de serviço Kubernetes. Os *Eventos* no final da saída do exemplo seguinte indicam que o *endereço IP fornecido pelo utilizador não foi encontrado*. Nestes cenários, verifique se criou o endereço IP público estático no grupo de recursos do nó e que o endereço IP especificado no manifesto de serviço kubernetes está correto.

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

Para um controlo adicional sobre o tráfego de rede para as suas aplicações, é melhor criar um controlador de [ingresso][aks-ingress-basic]. Também pode criar um controlador de [ingresso com um endereço IP público estático][aks-static-ingress].

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
