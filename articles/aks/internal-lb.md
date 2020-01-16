---
title: Criar um balanceador de carga interno no serviço kubernetes do Azure (AKS)
description: Saiba como criar e usar um balanceador de carga interno para expor seus serviços com o AKS (serviço kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 8b0b0358534c30407c9841d5c23560623545cf64
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045000"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usar um balanceador de carga interno com o serviço de kubernetes do Azure (AKS)

Para restringir o acesso aos seus aplicativos no AKS (serviço kubernetes do Azure), você pode criar e usar um balanceador de carga interno. Um balanceador de carga interno torna um serviço kubernetes acessível somente para aplicativos em execução na mesma rede virtual que o cluster kubernetes. Este artigo mostra como criar e usar um balanceador de carga interno com o AKS (serviço kubernetes do Azure).

> [!NOTE]
> O Azure Load Balancer está disponível em dois SKUs- *básico* e *Standard*. Por padrão, o SKU padrão é usado quando você cria um cluster AKS.  Ao criar um serviço com o tipo como Balancer, você obterá o mesmo tipo de lb que ao provisionar o cluster. Para obter mais informações, consulte [comparação de SKU do Azure Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

A entidade de serviço de cluster AKS precisará de permissão para gerenciar recursos de rede se você usar uma sub-rede ou grupo de recursos existente. Em geral, atribua a função de *colaborador de rede* à sua entidade de serviço nos recursos delegados. Para obter mais informações sobre permissões, consulte [delegar acesso AKs a outros recursos do Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Para criar um balanceador de carga interno, crie um manifesto de serviço chamado `internal-lb.yaml` com o tipo de serviço *Balancer* e a anotação *interna do balanceador de carga do Azure* , conforme mostrado no exemplo a seguir:

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

Implante o balanceador de carga interno usando o [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f internal-lb.yaml
```

Um balanceador de carga do Azure é criado no grupo de recursos do nó e conectado à mesma rede virtual que o cluster AKS.

Quando você exibe os detalhes do serviço, o endereço IP do balanceador de carga interno é mostrado na coluna *IP externo* . Nesse contexto, *external* está em relação à interface externa do balanceador de carga, e não ao recebimento de um endereço IP público e externo. Pode levar um minuto ou dois para que o endereço IP seja alterado de *\<pendente\>* para um endereço IP interno real, conforme mostrado no exemplo a seguir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Especificar um endereço IP

Se você quiser usar um endereço IP específico com o balanceador de carga interno, adicione a propriedade *loadBalancerIP* ao manifesto YAML do balanceador de carga. O endereço IP especificado deve residir na mesma sub-rede que o cluster AKS e ainda não deve ser atribuído a um recurso.

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

Quando implantado e você exibe os detalhes do serviço, o endereço IP na coluna *external-IP* reflete o endereço IP especificado:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Usar redes privadas

Ao criar o cluster AKS, você pode especificar configurações avançadas de rede. Essa abordagem permite que você implante o cluster em uma rede virtual e sub-redes do Azure existentes. Um cenário é implantar o cluster AKS em uma rede privada conectada ao seu ambiente local e executar serviços somente acessíveis internamente. Para obter mais informações, consulte configurar suas próprias sub-redes de rede virtual com [Kubenet][use-kubenet] ou [CNI do Azure][advanced-networking].

Nenhuma alteração nas etapas anteriores é necessária para implantar um balanceador de carga interno em um cluster AKS que usa uma rede privada. O balanceador de carga é criado no mesmo grupo de recursos que o cluster AKS, mas conectado à sua rede virtual privada e sub-rede, conforme mostrado no exemplo a seguir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Talvez seja necessário conceder à entidade de serviço para o cluster AKS a função de *colaborador de rede* para o grupo de recursos em que os recursos da rede virtual do Azure são implantados. Exiba a entidade de serviço com [AZ AKs show][az-aks-show], como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Para criar uma atribuição de função, use o comando [AZ role Assignment Create][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Especificar uma sub-rede diferente

Para especificar uma sub-rede para o balanceador de carga, adicione a anotação *Azure-Load-Balancer-Internal-subnet* ao seu serviço. A sub-rede especificada deve estar na mesma rede virtual que o cluster AKS. Quando implantado, o endereço *IP externo* do balanceador de carga faz parte da sub-rede especificada.

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

## <a name="delete-the-load-balancer"></a>Excluir o balanceador de carga

Quando todos os serviços que usam o balanceador de carga interno são excluídos, o próprio balanceador de carga também é excluído.

Você também pode excluir diretamente um serviço como com qualquer recurso kubernetes, como `kubectl delete service internal-app`, que também exclui o balanceador de carga do Azure subjacente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços Kubernetess na [documentação dos serviços Kubernetess][kubernetes-services].

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
