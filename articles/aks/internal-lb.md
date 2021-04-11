---
title: Criar um balanceador de carga interno
titleSuffix: Azure Kubernetes Service
description: Saiba como criar e utilizar um equilibrador de carga interno para expor os seus serviços ao Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4c2c0866aa9a721a73e1eb8fa230f0022cf6b8ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505635"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Utilize um equilibrador interno de carga com o Serviço Azure Kubernetes (AKS)

Para restringir o acesso às suas aplicações no Serviço Azure Kubernetes (AKS), pode criar e utilizar um equilibrador de carga interno. Um equilibrador de carga interno torna um serviço Kubernetes acessível apenas a aplicações que estão a funcionar na mesma rede virtual que o cluster Kubernetes. Este artigo mostra-lhe como criar e utilizar um equilibrador de carga interno com o Serviço Azure Kubernetes (AKS).

> [!NOTE]
> O Azure Load Balancer está disponível em dois SKUs - *Básico* e *Standard*. Por padrão, o SKU padrão é utilizado quando cria um cluster AKS.  Ao criar um Serviço com o tipo de LoadBalancer, obterá o mesmo tipo LB que quando forctifica o cluster. Para obter mais informações, consulte [a comparação SKU do balanceador de carga Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

A identidade do cluster AKS necessita de permissão para gerir os recursos da rede se utilizar uma sub-rede ou grupo de recursos existentes. Para obter [informações, utilize a rede kubenet com as suas próprias gamas de endereços IP no Serviço Azure Kubernetes (AKS)][use-kubenet] ou [Configure CNI em Azure Kubernetes Service (AKS)][advanced-networking]. Se estiver a configurar o seu equilibrador de carga para utilizar um [endereço IP numa sub-rede diferente,][different-subnet]certifique-se de que a identidade do cluster AKS também leu o acesso a essa sub-rede.

Para obter mais informações sobre permissões, consulte [o delegado AKS acesso a outros recursos Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Para criar um equilibrador de carga interno, crie um manifesto de serviço denominado `internal-lb.yaml` com o tipo de serviço *LoadBalancer* e a anotação *interna de balançadores de carga azure-load-internal,* como mostra o seguinte exemplo:

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

Coloque o balançador de carga interno utilizando o [kubectl e][kubectl-apply] especifique o nome do seu manifesto YAML:

```console
kubectl apply -f internal-lb.yaml
```

Um equilibrador de carga Azure é criado no grupo de recursos de nó e ligado à mesma rede virtual que o cluster AKS.

Quando vê os dados de serviço, o endereço IP do balançador de carga interno é indicado na coluna *EXTERNAL-IP.* Neste contexto, *o External* está relacionado com a interface externa do esquilibrador de carga, não que receba um endereço IP público e externo. Pode levar um minuto ou dois para que o endereço IP mude de *\<pending\>* um endereço IP interno real, como mostra o seguinte exemplo:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Especificar um endereço IP

Se quiser utilizar um endereço IP específico com o balançador de carga interno, adicione a propriedade *loadBalancerIP* ao manifesto YAML do balançador de carga. Neste cenário, o endereço IP especificado deve residir na mesma sub-rede que o cluster AKS e não deve ser atribuído a um recurso. Por exemplo, não deve utilizar um endereço IP no intervalo designado para a sub-rede Kubernetes.

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

Quando implementado e visualiza os dados de serviço, o endereço IP na coluna *EXTERNAL-IP* reflete o seu endereço IP especificado:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Para obter mais informações sobre a configuração do seu balançador de carga numa sub-rede diferente, consulte [Especificar uma sub-rede diferente][different-subnet]

## <a name="use-private-networks"></a>Utilizar redes privadas

Quando criar o seu cluster AKS, pode especificar definições avançadas de rede. Esta abordagem permite-lhe implantar o cluster numa rede virtual Azure existente e sub-redes. Um dos cenários é implantar o seu cluster AKS numa rede privada ligada ao seu ambiente no local e executar serviços apenas acessíveis internamente. Para obter mais informações, consulte configurar as suas próprias sub-redes de rede virtuais com [Kubenet][use-kubenet] ou [Azure CNI][advanced-networking].

Não são necessárias alterações nos passos anteriores para implantar um equilibrador de carga interno num cluster AKS que utilize uma rede privada. O equilibrador de carga é criado no mesmo grupo de recursos que o seu cluster AKS, mas ligado à sua rede virtual privada e sub-rede, como mostra o seguinte exemplo:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Poderá ser necessário conceder a identidade do cluster para o seu cluster AKS, a função *de Contribuinte de Rede* para o grupo de recursos onde os seus recursos de rede virtual Azure são implantados. Veja a identidade do cluster com [az aks show][az-aks-show], tais como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Para criar uma atribuição de funções, use a [atribuição de funções az criar][az-role-assignment-create] comando.

## <a name="specify-a-different-subnet"></a>Especificar uma sub-rede diferente

Para especificar uma sub-rede para o seu balanceador de carga, adicione a anotação *da sub-rede azure-load-balancer-internal-subnet* ao seu serviço. A sub-rede especificada deve estar na mesma rede virtual que o seu cluster AKS. Quando implantado, o endereço *EXTERNAL-IP* do balanceador de carga faz parte da sub-rede especificada.

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

## <a name="delete-the-load-balancer"></a>Eliminar o equilibrador de carga

Quando todos os serviços que utilizam o balançador de carga interno são eliminados, o próprio equilibrador de carga também é eliminado.

Também pode eliminar diretamente um serviço como qualquer recurso Kubernetes, como `kubectl delete service internal-app` , que também elimina o equilibrador de carga Azure subjacente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços da Kubernetes na documentação dos [serviços kubernetes.][kubernetes-services]

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet