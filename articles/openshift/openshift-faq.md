---
title: Perguntas mais frequentes sobre a Azure Red Hat OpenShift | Documentos da Microsoft
description: Aqui estão as respostas a perguntas comuns sobre o Microsoft Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 4f11eac106fe984aa06e41083c2d4f66c7d0e62c
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872919"
---
# <a name="azure-red-hat-openshift-faq"></a>FAQ do Azure de Red Hat OpenShift

Este artigo aborda perguntas mais frequentes (FAQ) sobre o Microsoft Azure Red Hat OpenShift.

## <a name="how-do-i-get-started"></a>Como posso começar?

Antes de poder utilizar a Azure Red Hat OpenShift, terá de comprar um mínimo de 4 nós de aplicação do Azure Red Hat OpenShift reservados.

Se for um cliente do Azure,[comprar instâncias reservadas do Azure Red Hat OpenShift](https://aka.ms/openshift/buy) através do portal do Azure. Após a compra, a sua subscrição será ativada dentro de 24 horas, após o qual será capaz de clusters de aprovisionamento.

Se não for um cliente do Azure, [contactar as vendas](https://aka.ms/openshift/contact-sales) e preencha o formulário de vendas na parte inferior da página para iniciar o processo.

Consulte a [página de preços do Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) para obter mais informações.

## <a name="which-azure-regions-are-supported"></a>Que regiões do Azure são suportados?

Ver [recursos suportados](supported-resources.md#azure-regions) para obter uma lista de regiões globais, onde a Azure Red Hat OpenShift é suportada.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Pode implementar um cluster numa rede virtual existente?

Não. Mas pode ligar um cluster do Azure Red Hat OpenShift a uma VNET existente através do peering. Ver [ligar uma rede virtual de um cluster a uma rede virtual existente ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) para obter detalhes.

## <a name="what-cluster-operations-are-available"></a>As operações de cluster estão disponíveis?

Apenas pode aumentar ou reduzir verticalmente o número de nós de computação. Não existem outras modificações que são permitidas para o `Microsoft.ContainerService/openShiftManagedClusters` recursos após a criação. O número máximo de nós de computação é limitado a 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Os tamanhos de máquina virtual posso utilizar?

Ver [tamanhos de máquinas virtuais do Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) para obter uma lista de tamanhos de máquina virtual pode utilizar com um cluster do Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>São os dados no meu cluster encriptado?

Por predefinição, existe a encriptação em repouso. A plataforma de armazenamento do Azure encripta automaticamente os dados antes de persistir e desencripta os dados antes da obtenção. Ver [encriptação do serviço de armazenamento do Azure para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) para obter detalhes.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Pode utilizar Prometheus/Grafana para monitorizar contentores e gerir a capacidade?

Não, não o momento atual.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>O registo do Docker está disponível externamente para poder usar ferramentas como o Jenkins?

O registo do Docker está disponível a partir `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` no entanto, não é fornecida uma garantia de durabilidade de armazenamento forte. Também pode utilizar [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Espaço de nomes entre redes é suportada?

Cliente e os administradores de projeto individuais podem personalizar o sistema de rede entre-espaço de nomes (incluindo negá-la) sobre como utilizar uma base por projeto `NetworkPolicy` objetos.

## <a name="can-an-admin-manage-users-and-quotas"></a>Um administrador pode gerir utilizadores e de quotas?

Sim. Um administrador do Azure Red Hat OpenShift pode gerir utilizadores e de quotas, além de aceder a todos os utilizadores criados projetos.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Posso restringir a um cluster apenas a determinados utilizadores do Azure AD?

Sim. Pode restringir o qual os utilizadores do AD podem iniciar sessão a um cluster ao configurar a aplicação do Azure AD. Para obter detalhes, consulte [como: Restringir a sua aplicação para um conjunto de utilizadores](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Pode um cluster tiver nós de computação em várias regiões do Azure?

Não. Todos os nós num cluster do Azure Red Hat OpenShift têm de ter origem na mesma região do Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Foram nós mestre e infraestrutura abstraídos como acontece com o Azure Kubernetes Service (AKS)?

Não. Todos os recursos, incluindo o mestre de cluster, executam na sua subscrição de cliente. Esses tipos de recursos são colocados num grupo de recursos só de leitura.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>É o Open Service Broker for Azure (OSBA) suportada?

Sim. Pode usar o OSBA com a Azure Red Hat OpenShift. Ver [Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) para obter mais informações.
