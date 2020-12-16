---
title: Serviço Azure Kubernetes (AKS) com Uptime SLA
description: Saiba mais sobre a oferta opcional uptime SLA para o Serviço Azure Kubernetes (AKS) API Server.
services: container-service
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: d9d22b54ef2534cf0adb205175fdc3f1c57205fb
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607344"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Serviço Azure Kubernetes (AKS) Uptime SLA

Uptime SLA é uma característica opcional para permitir um SLA mais alto e apoiado financeiramente para um cluster. Uptime SLA garante 99,95% de disponibilidade do ponto final do servidor API de Kubernetes para clusters que usam [Zonas de Disponibilidade][availability-zones] e 99,9% de disponibilidade para clusters que não usam Zonas de Disponibilidade. A AKS utiliza réplicas de nó master em domínios de atualização e avaria para garantir que os requisitos de SLA são cumpridos.

Os clientes que necessitem de um SLA para cumprir os requisitos de conformidade ou que exijam o alargamento de um SLA aos seus utilizadores finais devem ativar esta funcionalidade. Os clientes com cargas de trabalho críticas que beneficiarão de um SLA de maior uptime também podem beneficiar. A utilização da funcionalidade Uptime SLA com Zonas de Disponibilidade permite uma maior disponibilidade para o tempo de funcionação do servidor API de Kubernetes.  

Os clientes podem ainda criar clusters gratuitos ilimitados com um objetivo de nível de serviço (SLO) de 99,5% e optar pelo SLO ou SLA Uptime preferidos, se necessário.

> [!Important]
> Para aglomerados com isolamento de saídas, consulte [o tráfego de saída limite](limit-egress-traffic.md) para abrir portas apropriadas.

## <a name="region-availability"></a>Disponibilidade de região

* O Uptime SLA está disponível nas regiões públicas e nas regiões do Governo de Azure onde [a AKS é apoiada.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)
* Uptime SLA está disponível para [clusters privados AKS][private-clusters] em todas as regiões onde a AKS é apoiada.

## <a name="sla-terms-and-conditions"></a>Termos e condições do SLA

Uptime SLA é uma funcionalidade paga e ativada por cluster. Os preços de SLA de uptime são determinados pelo número de clusters discretos, e não pelo tamanho dos clusters individuais. Pode ver [os detalhes dos preços do Uptime SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) para mais informações.

## <a name="before-you-begin"></a>Before you begin

* Instale a versão [Azure CLI](/cli/azure/install-azure-cli) 2.8.0 ou posterior

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Criar um novo cluster com Uptime SLA

> [!NOTE]
> Atualmente, se ativar o Uptime SLA, não há como removê-lo de um cluster.

Para criar um novo cluster com o Uptime SLA, utilize o Azure CLI.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Utilize o [`az aks create`][az-aks-create] comando para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Esta operação leva vários minutos para ser concluída:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster. O seguinte snippet JSON mostra o nível pago para o SKU, indicando que o seu cluster está ativado com Uptime SLA:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Modificar um cluster existente para utilizar o Uptime SLA

Pode atualizar opcionalmente os seus clusters existentes para utilizar o Uptime SLA.

Se criou um cluster AKS com os passos anteriores, elimine o grupo de recursos:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Criar um novo grupo de recursos:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Crie um novo cluster, e não use o Uptime SLA:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Utilize o [`az aks update`][az-aks-nodepool-update] comando para atualizar o cluster existente:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 O seguinte snippet JSON mostra o nível pago para o SKU, indicando que o seu cluster está ativado com Uptime SLA:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="clean-up"></a>Limpeza

Para evitar acusações, limpe todos os recursos que criou. Para eliminar o cluster, utilize o [`az group delete`][az-group-delete] comando para eliminar o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Passos seguintes

Utilize [Zonas de disponibilidade][availability-zones] para aumentar a disponibilidade com as cargas de trabalho do cluster AKS.

Configure o seu cluster para [limitar o tráfego de saídas](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?#az-aks-nodepool-update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
