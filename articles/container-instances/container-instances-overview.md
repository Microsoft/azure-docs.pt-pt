---
title: Recipientes sem servidor em Azure
description: O serviço De casos de contentores Azure oferece a forma mais rápida e simples de executar contentores isolados em Azure, sem ter de gerir máquinas virtuais e sem ter de adotar um orquestrador de nível superior.
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c871c09e29b64c4f0dcd107361154efdce306481
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365032"
---
# <a name="what-is-azure-container-instances"></a>O que é o Azure Container Instances?

Os contentores estão a tornar-se na forma preferida de empacotar, implementar e gerir aplicações na cloud. O Azure Container Instances é a forma mais rápida e simples de executar um contentor no Azure, sem que tenha de gerir máquinas virtuais e sem que tenha de adotar um serviço de nível superior.

O Azure Container Instances é uma ótima solução para qualquer cenário que possa funcionar em contentores isolados, incluindo aplicações simples, automatização de tarefas e tarefas de compilação. Em cenários nos quais precisa de orquestração de contentores completa, incluindo deteção de serviços em vários contentores, dimensionamento automático e atualizações coordenadas de aplicações, recomendamos o [Serviço Kubernetes do Azure (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempos de arranque rápidos

Os contentores oferecem benefícios de arranque significativos em relação às máquinas virtuais (VMs). Com o Azure Container Instances, pode iniciar contentores no Azure em segundos, sem ter de aprovisionar e gerir VMs.

## <a name="container-access"></a>Acesso a contentores

As instâncias de contentores Azure permitem expor os seus grupos de contentores diretamente à internet com um endereço IP e um nome de domínio totalmente qualificado (FQDN). Quando cria uma instância de contentor, pode especificar uma etiqueta de nome DNS personalizada para que a aplicação esteja acessível em *customlabel*.*azureregion*.azurecontainer.io.

A Azure Container Instances também suporta a execução de um comando num contentor de corrida, fornecendo uma concha interativa para ajudar no desenvolvimento de aplicações e na resolução de problemas. O acesso ocupa lugares sobre HTTPS, usando TLS para garantir ligações ao cliente.

> [!IMPORTANT]
> A partir de 13 de janeiro de 2020, as Instâncias de Contentores Azure exigirão todas as ligações seguras dos servidores e aplicações para utilizar o TLS 1.2. O suporte para TLS 1.0 e 1.1 será retirado.

## <a name="hypervisor-level-security"></a>Segurança ao nível do hipervisor

Historicamente, os contentores têm vindo a oferecer isolamento de dependências de aplicações e governação de recursos, mas não têm sido considerados suficientemente fortalecidos para utilização multi-inquilinos hostil. O Azure Container Instances garante que a sua aplicação está tão isolada num contentor como estaria numa VM.


## <a name="custom-sizes"></a>Tamanhos personalizados

Normalmente, os contentores são otimizados para executarem apenas uma única aplicação, mas as necessidades exatas de cada uma dessas aplicações podem ser bastante diferentes. O Azure Container Instances proporciona uma utilização ideal ao permitir especificações exatas de núcleos de CPU e memória. A faturação é ao segundo e consoante o que precisar, para que possa ajustar os gastos com base nas suas necessidades reais.

Para trabalhos intensivos de computação, como machine learning, o Azure Container Instances pode agendar contentores Linux para usar [recursos GPU](container-instances-gpu.md) NVIDIA Tesla (pré-visualização).

## <a name="persistent-storage"></a>Armazenamento persistente

Para recuperar e persistir o estado com as Instâncias de Contentores Azure, oferecemos a montagem direta [de ações da Azure Files](container-instances-mounting-azure-files-volume.md) apoiadas pelo Armazenamento Azure.

## <a name="linux-and-windows-containers"></a>Contentores de Linux e Windows

O Azure Container Instances permite agendar contentores do Windows e do Linux com a mesma API. Basta especificar o tipo de SO quando cria os [grupos de contentor](container-instances-container-groups.md).

Algumas funcionalidades estão atualmente restritas aos contentores Linux:

* Vários contentores por grupo de contentores
* Montagem de volume[(Ficheiros Azure,](container-instances-volume-azure-files.md) [EmptyDir,](container-instances-volume-emptydir.md) [GitRepo,](container-instances-volume-gitrepo.md) [segredo)](container-instances-volume-secret.md)
* [Métricas de utilização](container-instances-monitor.md) de recursos com monitor Azure
* [Implantação de rede virtual](container-instances-vnet.md)
* [Recursos gpu](container-instances-gpu.md) (pré-visualização)

Para implementações de contentores Windows, utilize imagens baseadas em [imagens comuns](container-instances-faq.md#what-windows-base-os-images-are-supported)da base do Windows .

> [!NOTE]
> A utilização de imagens baseadas no Windows Server 2019 em Instâncias de Contentores Azure está em pré-visualização.

## <a name="co-scheduled-groups"></a>Grupos agendados conjuntamente

O Azure Container Instances suporta o agendamento de [grupos de vários contentores](container-instances-container-groups.md) que partilhem um computador anfitrião, a rede local, o armazenamento e o ciclo de vida. Desta forma, pode combinar o contentor da aplicação principal com outros contentores que têm um papel secundário, tais como sidecars de registo.

## <a name="virtual-network-deployment"></a>Implantação de rede virtual

Atualmente disponível para cargas horárias de produção num subconjunto de regiões azure, esta característica de Instâncias de Contentores Azure permite [a colocação de instâncias de contentores numa rede virtual Azure.](container-instances-vnet.md) Ao implementar instâncias de contentor numa sub-rede da sua rede virtual, estes podem comunicar em segurança com outros recursos na rede virtual, incluindo os que estão no local (através da [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou de [ ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Passos seguintes

Experimente implementar um contentor no Azure com um único comando através do nosso guia de início rápido:

> [!div class="nextstepaction"]
> [Início Rápido do Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
