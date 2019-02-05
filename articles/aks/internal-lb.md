---
title: Criar um balanceador de carga interno no Azure Kubernetes Service (AKS)
description: Saiba como criar e utilizar um balanceador de carga interno para expor os seus serviços com o Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: e4b5b6085dbe9a09c90e059a5db8bee5d6d7a004
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699328"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Utilizar um balanceador de carga interno com o Azure Kubernetes Service (AKS)

Para restringir o acesso às suas aplicações no Azure Kubernetes Service (AKS), pode criar e utilizar um balanceador de carga interno. Um balanceador de carga interno torna um serviço do Kubernetes acessível apenas para aplicações em execução na mesma rede virtual do cluster de Kubernetes. Este artigo mostra-lhe como criar e utilizar um balanceador de carga interno com o Azure Kubernetes Service (AKS).

> [!NOTE]
> O Balanceador de carga do Azure está disponível em dois SKUs - *básica* e *padrão*. Para obter mais informações, consulte [comparação SKU do Balanceador de carga do Azure][azure-lb-comparison]. AKS suporta atualmente o *básica* SKU. Se pretender utilizar o *padrão* SKU, pode usar o montante [motor de aks][aks-engine].

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Para criar um balanceador de carga interno, criar um manifesto de serviço com o nome `internal-lb.yaml` com o tipo de serviço *LoadBalancer* e o *interno do azure-load-balancer* anotação, conforme mostrado na seguinte exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Depois de implantados com `kubectl apply -f internal-lb.yaml`, um balanceador de carga do Azure é criado e disponibilizado na mesma rede virtual que o cluster do AKS.

Ao visualizar os detalhes do serviço, o endereço IP do Balanceador de carga interno é mostrado na *EXTERNAL-IP* coluna. Poderá demorar um minuto ou dois para que o endereço IP para alterar a partir *\<pendente\>* para um real endereço IP interno, conforme mostrado no exemplo a seguir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Especifique um endereço IP

Se gostaria de utilizar um endereço IP específico com o Balanceador de carga interno, adicione a *loadBalancerIP* propriedade para o manifesto YAML de Balanceador de carga. O endereço IP especificado tem de residir na mesma sub-rede que o cluster do AKS e já não deve ser atribuído a um recurso.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Ao visualizar os detalhes do serviço, o endereço IP no *EXTERNAL-IP* coluna reflete o seu endereço IP especificado:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Utilizar redes privadas

Ao criar o cluster do AKS, pode especificar definições de rede avançadas. Esta abordagem permite-lhe implementar o cluster numa rede virtual do Azure existente e sub-redes. Um cenário é implementar o seu cluster do AKS para uma rede privada ligada ao seu ambiente no local e executar serviços acessíveis apenas internamente. Para obter mais informações, consulte [configuração de rede avançada no AKS][advanced-networking].

Sem alterações para os passos anteriores são necessários para implementar um balanceador de carga interno num cluster do AKS que utiliza uma rede privada. O Balanceador de carga é criado no mesmo grupo de recursos do seu cluster do AKS mas ligado à sua rede privada virtual e sub-rede, conforme mostrado no exemplo a seguir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Poderá ter de conceder o principal de serviço para o seu cluster do AKS a *contribuinte de rede* função para o grupo de recursos onde os recursos de rede virtual do Azure são implementados. Ver o principal de serviço com [show do az aks][az-aks-show], tais como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Para criar uma atribuição de função, utilize o [criação da atribuição de função de az] [ az-role-assignment-create] comando.

## <a name="specify-a-different-subnet"></a>Especifique uma sub-rede diferente

Para especificar uma sub-rede para o Balanceador de carga, adicione a *azure-carga-balanceador--sub-rede interna* anotação ao seu serviço. A sub-rede especificada tem de ser na mesma rede virtual que o cluster do AKS. Quando implementada, o Balanceador de carga *EXTERNAL-IP* endereço faz parte da sub-rede especificada.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Eliminar o Balanceador de carga

Quando são eliminados todos os serviços que utilizam o Balanceador de carga interno, o próprio Balanceador de carga também é eliminado.

Diretamente também pode eliminar um serviço como com qualquer recurso do Kubernetes, tais como `kubectl delete service internal-app`, que também, em seguida, elimina o Balanceador de carga do Azure subjacente.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre os serviços do Kubernetes com o [documentação dos serviços de Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus