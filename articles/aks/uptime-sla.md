---
title: Serviço Azure Kubernetes (AKS) com Uptime SLA
description: Conheça a oferta opcional uptime SLA para o Serviço Azure Kubernetes (AKS) API Server.
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e0e1399f69640dddfd618ac99637023390f28a92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683216"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Serviço Azure Kubernetes (AKS) Uptime SLA

Uptime SLA é uma funcionalidade opcional para permitir um SLA financeiramente apoiado e mais alto para um cluster. Uptime SLA garante 99,95% de disponibilidade do ponto final do servidor Kubernetes API para clusters que usam Zonas de [Disponibilidade][availability-zones] e 99,9% da disponibilidade para clusters que não usam Zonas de Disponibilidade. O AKS utiliza réplicas de nó mestre em domínios de atualização e falhas para garantir que os requisitos de SLA são cumpridos.

Os clientes que necessitem de um SLA para satisfazer os requisitos de conformidade ou exigir o alargamento de um SLA aos seus utilizadores finais devem ativar esta funcionalidade. Os clientes com cargas de trabalho críticas que beneficiarão de um SLA de uptime mais elevado também podem beneficiar. A utilização da funcionalidade Uptime SLA com Zonas de Disponibilidade permite uma maior disponibilidade para o tempo de uptime do servidor Kubernetes API.  

Os clientes podem ainda criar clusters gratuitos ilimitados com um objetivo de nível de serviço (SLO) de 99,5% e optar pelo Horário de SLO ou SLA preferido, conforme necessário.

> [!Important]
> Para aglomerados com bloqueio de saída, consulte o [tráfego de egress limite](limit-egress-traffic.md) para abrir portos apropriados.

## <a name="sla-terms-and-conditions"></a>Termos e condições sLA

Uptime SLA é uma característica paga e ativada por cluster. Os preços uptime SLA são determinados pelo número de clusters discretos, e não pelo tamanho dos clusters individuais. Pode ver os detalhes de preços do [Uptime SLA](https://azure.microsoft.com/pricing/details/kubernetes-service/) para mais informações.

## <a name="region-availability"></a>Disponibilidade da Região

O Uptime SLA está disponível nas seguintes regiões:

* Leste da Austrália
* Canadá Central
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Centro-Sul
* Ásia Sudeste
* E.U.A.Oeste 2

## <a name="before-you-begin"></a>Antes de começar

* A versão Azure CLI 2.7.0 ou posterior

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
Após alguns minutos, o comando completa e devolve informações formatadas da JSON sobre o cluster. O seguinte snippet JSON mostra o nível pago para o SKU, indicando que o seu cluster está ativado com Uptime SLA.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Limitações

* Atualmente, não pode converter-se como cluster existente para permitir o Uptime SLA.
* Atualmente, não há como remover uptime SLA de um cluster AKS após a criação com ele habilitado.  
* Os agrupamentos privados não são atualmente apoiados.

## <a name="next-steps"></a>Passos seguintes

Utilize zonas de [disponibilidade][availability-zones] para aumentar a alta disponibilidade com as suas cargas de trabalho de cluster AKS.
Configure o seu cluster para limitar o tráfego de [saída](limit-egress-traffic.md).

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
