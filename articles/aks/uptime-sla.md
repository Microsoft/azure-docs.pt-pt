---
title: Serviço Azure Kubernetes (AKS) com Uptime SLA
description: Saiba mais sobre a oferta opcional uptime SLA para o Serviço Azure Kubernetes (AKS) API Server.
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: b360f36dfc80033ac95e4face438b66eed33cec4
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945516"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Serviço Azure Kubernetes (AKS) Uptime SLA

Uptime SLA é uma característica opcional para permitir um SLA mais alto e apoiado financeiramente para um cluster. Uptime SLA garante 99,95% de disponibilidade do ponto final do servidor API de Kubernetes para clusters que usam [Zonas de Disponibilidade][availability-zones] e 99,9% de disponibilidade para clusters que não usam Zonas de Disponibilidade. A AKS utiliza réplicas de nó master em domínios de atualização e avaria para garantir que os requisitos de SLA são cumpridos.

Os clientes que necessitem de um SLA para cumprir os requisitos de conformidade ou que exijam o alargamento de um SLA aos seus utilizadores finais devem ativar esta funcionalidade. Os clientes com cargas de trabalho críticas que beneficiarão de um SLA de maior uptime também podem beneficiar. A utilização da funcionalidade Uptime SLA com Zonas de Disponibilidade permite uma maior disponibilidade para o tempo de funcionação do servidor API de Kubernetes.  

Os clientes podem ainda criar clusters gratuitos ilimitados com um objetivo de nível de serviço (SLO) de 99,5% e optar pelo SLO ou SLA Uptime preferidos, se necessário.

> [!Important]
> Para aglomerados com isolamento de saídas, consulte [o tráfego de saída limite](limit-egress-traffic.md) para abrir portas apropriadas.

## <a name="region-availability"></a>Disponibilidade de região

O Uptime SLA está disponível em regiões públicas onde [a AKS é apoiada.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

* O Governo de Azure não é apoiado neste momento.
* Azure China 21Vianet não é atualmente apoiada.

## <a name="sla-terms-and-conditions"></a>Termos e condições do SLA

Uptime SLA é uma funcionalidade paga e ativada por cluster. Os preços de SLA de uptime são determinados pelo número de clusters discretos, e não pelo tamanho dos clusters individuais. Pode ver [os detalhes dos preços do Uptime SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) para mais informações.

## <a name="before-you-begin"></a>Antes de começar

* Instale a versão [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.7.0 ou posterior

## <a name="creating-a-cluster-with-uptime-sla"></a>Criar um cluster com Uptime SLA

Para criar um novo cluster com o Uptime SLA, utilize o Azure CLI.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. O Azure Monitor para contentores também é ativado com o parâmetro *--enable-addons monitoring*.  Esta operação leva vários minutos para ser concluída.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster. O seguinte corte JSON mostra o nível pago para o SKU, indicando que o seu cluster está ativado com Uptime SLA.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Limitações

* Atualmente, não é possível converter como cluster existente para permitir o Uptime SLA.
* Atualmente, não há forma de remover o Uptime SLA de um cluster AKS após a criação com ele ativado.  
* Os aglomerados privados não são suportados atualmente.

## <a name="next-steps"></a>Passos seguintes

Utilize [Zonas de disponibilidade][availability-zones] para aumentar a disponibilidade com as cargas de trabalho do cluster AKS.
Configure o seu cluster para [limitar o tráfego de saídas](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
