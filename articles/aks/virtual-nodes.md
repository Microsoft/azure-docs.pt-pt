---
title: Utilizar nós virtuais
titleSuffix: Azure Kubernetes Service
description: Visão geral de como usar o nó virtual com os Serviços Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 1e5a53c80aed90e44ba594dcff3c8ca23afc2c4f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684779"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Criar e configurar um cluster Azure Kubernetes Services (AKS) para usar nós virtuais

Para escalar rapidamente as cargas de trabalho da aplicação num cluster AKS, pode utilizar nós virtuais. Com nós virtuais, você tem um fornecimento rápido de cápsulas, e só paga por segundo pelo seu tempo de execução. Não é preciso esperar que o cluster de Kubernetes possa implantar nós de computação VM para executar as cápsulas adicionais. Os nós virtuais só são suportados com cápsulas e nós Linux.

O complemento de nós virtuais para AKS, é baseado no projeto de código aberto [Virtual Kubelet.][virtual-kubelet-repo]

Este artigo dá-lhe uma visão geral dos requisitos de disponibilidade e networking da região para a utilização de nós virtuais, bem como as limitações conhecidas.

## <a name="regional-availability"></a>Disponibilidade regional

Todas as regiões, onde o ACI suporta skus VNET, são apoiadas para implantações de nós virtuais.

Para os SKUs de CPU e memória disponíveis em cada região, verifique a disponibilidade de recursos de [instâncias de contentores Azure para instâncias de contentores Azure nas regiões de Azure - grupos de contentores Linux](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Requisitos de rede

Os nós virtuais permitem a comunicação de rede entre as cápsulas que funcionam em Instâncias de Contentores Azure (ACI) e o cluster AKS. Para fornecer esta comunicação, é criada uma sub-rede de rede virtual e são atribuídas permissões delegadas. Os nódigos virtuais só funcionam com clusters AKS criados com rede *avançada* (Azure CNI). Por padrão, os clusters AKS são criados com rede *básica* (kubenet).

As cápsulas em execução em Instâncias de Contentores Azure (ACI) precisam de acesso ao ponto final do servidor AKS API, a fim de configurar a rede.

## <a name="known-limitations"></a>Limitações conhecidas

A funcionalidade Nódes Virtuais está fortemente dependente do conjunto de funcionalidades do ACI. Para além das [quotas e limites para as instâncias do contentor Azure,](../container-instances/container-instances-quotas.md)os seguintes cenários ainda não são suportados com nóns virtuais:

* Utilizar o principal de serviço para retirar imagens ACR. [A solução](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) é usar [segredos de Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de rede virtuais,](../container-instances/container-instances-vnet.md) incluindo o peering VNet, as políticas de rede Kubernetes e o tráfego de saída para a internet com grupos de segurança de rede.
* Recipientes init
* [Pseudónimos anfitrião](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para executivos em ACI
* [Os DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não vão implantar cápsulas nos nos acenos virtuais
* Os nós virtuais suportam o agendamento de cápsulas Linux. Pode instalar manualmente o fornecedor [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) de fonte aberta para agendar os contentores do Windows Server para ACI.
* Os nós virtuais requerem clusters AKS com rede CNI Azure.
* Nós virtuais com aglomerados privados.
* Utilizando gamas ip autorizadas do servidor API para AKS.
* A montagem em volume dos Ficheiros Azure partilha suporte [de uso geral V1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Siga as instruções para a montagem [de um volume com a partilha de Ficheiros Azure](azure-files-volume.md)
* A utilização do IPv6 não é suportada.

## <a name="next-steps"></a>Passos seguintes

Configure os nóns virtuais para os seus clusters:

- [Criar nódes virtuais usando O Azure CLI](virtual-nodes-cli.md)
- [Criar nós virtuais utilizando o portal nos Serviços Azure Kubernetes (AKS)](virtual-nodes-portal.md)

Os nós virtuais são frequentemente um componente de uma solução de escala em AKS. Para obter mais informações sobre soluções de escala, consulte os seguintes artigos:

- [Utilize o pod horizontal Kubernetes autoscaler][aks-hpa]
- [Utilize o cluster Kubernetes autoscaler][aks-cluster-autoscaler]
- [Confira a amostra de autoescala para nóns virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca virtual de fonte aberta de Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
