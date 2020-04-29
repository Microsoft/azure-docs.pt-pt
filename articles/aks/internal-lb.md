---
title: Criar um balanceador de carga interno
titleSuffix: Azure Kubernetes Service
description: Aprenda a criar e utilizar um equilibrador de carga interno para expor os seus serviços com o Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 9c2966215d07c4ddf052d30a5757a2deee2e0b5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392782"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Utilize um equilibrante de carga interna com o Serviço Azure Kubernetes (AKS)

Para restringir o acesso às suas aplicações no Serviço Azure Kubernetes (AKS), pode criar e utilizar um equilibrador de carga interna. Um equilibrador de carga interno torna um serviço Kubernetes acessível apenas a aplicações que executam na mesma rede virtual que o cluster Kubernetes. Este artigo mostra-lhe como criar e utilizar um equilibrador de carga interno com o Serviço Azure Kubernetes (AKS).

> [!NOTE]
> O Equilíbrio de Carga Azure está disponível em duas SKUs - *Basic* and *Standard*. Por padrão, o SKU Padrão é usado quando cria um cluster AKS.  Ao criar um Serviço com tipo de LoadBalancer, obterá o mesmo tipo LB que quando fornecer o cluster. Para mais informações, consulte a [comparação SKU do equilíbrior][azure-lb-comparison]de carga Azure .

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

O principal do serviço de cluster AKS precisa de permissão para gerir os recursos da rede se utilizar uma subnet ou grupo de recursos existente. Em geral, atribuir o papel de colaborador da *Rede* ao seu principal de serviço sobre os recursos delegados. Em vez de um diretor de serviço, pode utilizar o sistema de identidade gerida para obter permissões. Para mais informações, consulte [Use identidades geridas](use-managed-identity.md). Para obter mais informações sobre permissões, consulte o [acesso do Delegado AKS a outros recursos Do Azure.][aks-sp]

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Para criar um equilibrante interno de `internal-lb.yaml` carga, crie um manifesto de serviço nomeado com o tipo de serviço *LoadBalancer* e a anotação *interna de equilíbrio-carga azul,* como mostra o seguinte exemplo:

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

Coloque o equilibrador de carga interno utilizando o [kubectl e][kubectl-apply] especifique o nome do seu manifesto YAML:

```console
kubectl apply -f internal-lb.yaml
```

Um equilibrista de carga Azure é criado no grupo de recursos do nó e ligado à mesma rede virtual que o cluster AKS.

Quando visualiza os dados do serviço, o endereço IP do equilibrista interno de carga é mostrado na coluna *EXTERNAL-IP.* Neste contexto, a *External* está relacionada com a interface externa do equilibrista de carga, não que receba um endereço IP público e externo. Pode levar um minuto ou dois para * \<que\> * o endereço IP mude de pendente para um endereço IP interno real, como se pode ver no seguinte exemplo:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Especificar um endereço IP

Se quiser utilizar um endereço IP específico com o equilibrante interno de carga, adicione a propriedade *loadBalancerIP* ao manifesto YAML do equilibrador de carga. O endereço IP especificado deve residir na mesma sub-rede que o cluster AKS e não deve já ser atribuído a um recurso.

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

Quando implementado e visualiza os dados do serviço, o endereço IP na coluna *EXTERNAL-IP* reflete o seu endereço IP especificado:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Utilizar redes privadas

Quando criar o seu cluster AKS, pode especificar definições avançadas de rede. Esta abordagem permite-lhe implantar o cluster numa rede virtual e subnets existentes do Azure. Um dos cenários é implantar o seu cluster AKS numa rede privada ligada ao ambiente no local e executar serviços apenas acessíveis internamente. Para mais informações, consulte configurar as suas próprias redes virtuais com [kubenet][use-kubenet] ou [Azure CNI][advanced-networking].

Não são necessárias alterações aos passos anteriores para implantar um equilibrador de carga interno num cluster AKS que utilize uma rede privada. O equilibrador de carga é criado no mesmo grupo de recursos que o seu cluster AKS, mas ligado à sua rede virtual privada e subnet, como mostra o seguinte exemplo:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Poderá ter de conceder ao diretor de serviço si o cluster AKS, a função de *Colaborador de Rede* para o grupo de recursos onde os seus recursos de rede virtual Azure são implantados. Veja o diretor de serviço com [az aks show][az-aks-show], como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Para criar uma atribuição de funções, use a atribuição de [funções az criar][az-role-assignment-create] comando.

## <a name="specify-a-different-subnet"></a>Especificar uma sub-rede diferente

Para especificar uma sub-rede para o seu equilibrador de carga, adicione ao seu serviço a anotação de sub-rede de rede de rede de rede de rede *de carga azul-carregador-interno.* A sub-rede especificada deve estar na mesma rede virtual que o seu cluster AKS. Quando implantado, o endereço *EXTERNO-IP* do equilibrador de carga faz parte da sub-rede especificada.

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

Quando todos os serviços que utilizam o equilibrista interno de carga são eliminados, o próprio equilibrador de carga é também eliminado.

Também pode eliminar diretamente um serviço como qualquer recurso `kubectl delete service internal-app`kubernetes, como, como, que também elimina o equilibrador de carga Azure subjacente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços da Kubernetes na documentação dos [serviços da Kubernetes.][kubernetes-services]

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
