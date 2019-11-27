---
title: Contêineres sem servidor no Azure
description: O serviço de instâncias de contêiner do Azure oferece a maneira mais rápida e simples de executar contêineres isolados no Azure, sem precisar gerenciar máquinas virtuais e sem precisar adotar um orquestrador de nível superior.
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c8f243bc2bda83a467688ac5828219cd015c8326
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533535"
---
# <a name="what-is-azure-container-instances"></a>O que é o Azure Container Instances?

Os contentores estão a tornar-se na forma preferida de empacotar, implementar e gerir aplicações na cloud. O Azure Container Instances é a forma mais rápida e simples de executar um contentor no Azure, sem que tenha de gerir máquinas virtuais e sem que tenha de adotar um serviço de nível superior.

O Azure Container Instances é uma ótima solução para qualquer cenário que possa funcionar em contentores isolados, incluindo aplicações simples, automatização de tarefas e tarefas de compilação. Em cenários nos quais precisa de orquestração de contentores completa, incluindo deteção de serviços em vários contentores, dimensionamento automático e atualizações coordenadas de aplicações, recomendamos o [Serviço Kubernetes do Azure (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempos de arranque rápidos

Os contentores oferecem benefícios de arranque significativos em relação às máquinas virtuais (VMs). Com o Azure Container Instances, pode iniciar contentores no Azure em segundos, sem ter de aprovisionar e gerir VMs.

## <a name="public-ip-connectivity-and-dns-name"></a>Conectividade de IP público e nome DNS

O Azure Container Instances permite expor os seus contentores diretamente na Internet com um endereço IP e um nome de domínio completamente qualificado (FQDN). Quando cria uma instância de contentor, pode especificar uma etiqueta de nome DNS personalizada para que a aplicação esteja acessível em *customlabel*.*azureregion*.azurecontainer.io.

## <a name="hypervisor-level-security"></a>Segurança ao nível do hipervisor

Historicamente, os contentores têm vindo a oferecer isolamento de dependências de aplicações e governação de recursos, mas não têm sido considerados suficientemente fortalecidos para utilização multi-inquilinos hostil. O Azure Container Instances garante que a sua aplicação está tão isolada num contentor como estaria numa VM.

## <a name="custom-sizes"></a>Tamanhos personalizados

Normalmente, os contentores são otimizados para executarem apenas uma única aplicação, mas as necessidades exatas de cada uma dessas aplicações podem ser bastante diferentes. O Azure Container Instances proporciona uma utilização ideal ao permitir especificações exatas de núcleos de CPU e memória. A faturação é ao segundo e consoante o que precisar, para que possa ajustar os gastos com base nas suas necessidades reais.

Para trabalhos de computação intensiva, como o Machine Learning, as instâncias de contêiner do Azure podem agendar contêineres do Linux para usar os recursos NVIDIA Tesla [GPU](container-instances-gpu.md) (versão prévia).

## <a name="persistent-storage"></a>Armazenamento persistente

Para obter e persistir o estado com o Azure Container Instances, ofereceremos a [montagem direta de partilhas de ficheiros do Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Contentores de Linux e Windows

O Azure Container Instances permite agendar contentores do Windows e do Linux com a mesma API. Basta especificar o tipo de SO quando cria os [grupos de contentor](container-instances-container-groups.md).

Alguns recursos estão atualmente restritos a contêineres do Linux:

* Vários contentores por grupo de contentores
* Montagem de volume ([arquivos do Azure](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [segredo](container-instances-volume-secret.md))
* [Métricas de uso de recursos](container-instances-monitor.md) com Azure monitor
* [Implantação de rede virtual](container-instances-vnet.md) (versão prévia)
* [Recursos de GPU](container-instances-gpu.md) (visualização)

Para implantações de contêiner do Windows, use imagens baseadas em [imagens básicas](container-instances-faq.md#what-windows-base-os-images-are-supported)comuns do Windows.

> [!NOTE]
> O uso de imagens baseadas no Windows Server 2019 em instâncias de contêiner do Azure está em versão prévia.

## <a name="co-scheduled-groups"></a>Grupos agendados conjuntamente

O Azure Container Instances suporta o agendamento de [grupos de vários contentores](container-instances-container-groups.md) que partilhem um computador anfitrião, a rede local, o armazenamento e o ciclo de vida. Desta forma, pode combinar o contentor da aplicação principal com outros contentores que têm um papel secundário, tais como sidecars de registo.

## <a name="virtual-network-deployment-preview"></a>Implementação da rede virtual (pré-visualização)

Atualmente em pré-visualização, esta funcionalidade do Azure Container Instances permite [implementar instâncias de contentor numa rede virtual do Azure](container-instances-vnet.md). Ao implementar instâncias de contentor numa sub-rede da sua rede virtual, estes podem comunicar em segurança com outros recursos na rede virtual, incluindo os que estão no local (através da [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou de [ ExpressRoute](../expressroute/expressroute-introduction.md)).

> [!IMPORTANT]
> Determinados recursos das instâncias de contêiner do Azure estão em versão prévia e algumas [limitações se aplicam](container-instances-vnet.md#preview-limitations). As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspectos desses recursos podem ser alterados antes da disponibilidade geral (GA).

## <a name="next-steps"></a>Passos seguintes

Experimente implementar um contentor no Azure com um único comando através do nosso guia de início rápido:

> [!div class="nextstepaction"]
> [Início Rápido do Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
