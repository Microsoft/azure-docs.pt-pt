---
title: Contentores sem servidor em Azure
description: O serviço Azure Container Instances oferece a forma mais rápida e simples de executar contentores isolados em Azure, sem ter que gerir máquinas virtuais e sem ter que adotar um orquestrador de nível superior.
ms.topic: overview
ms.date: 08/10/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 873f7cc51d1b369503a72501ae000000ff06f805
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573018"
---
# <a name="what-is-azure-container-instances"></a>O que é o Azure Container Instances?

Os contentores estão a tornar-se na forma preferida de empacotar, implementar e gerir aplicações na cloud. O Azure Container Instances é a forma mais rápida e simples de executar um contentor no Azure, sem que tenha de gerir máquinas virtuais e sem que tenha de adotar um serviço de nível superior.

O Azure Container Instances é uma ótima solução para qualquer cenário que possa funcionar em contentores isolados, incluindo aplicações simples, automatização de tarefas e tarefas de compilação. Para cenários onde você precisa de orquestração completa de contentores, incluindo a descoberta de serviço em vários contentores, escala automática e atualizações coordenadas de aplicações, recomendamos [serviço Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempos de arranque rápidos

Os contentores oferecem benefícios de arranque significativos em relação às máquinas virtuais (VMs). Com o Azure Container Instances, pode iniciar contentores no Azure em segundos, sem ter de aprovisionar e gerir VMs.

Traga imagens de contentores Linux ou Windows do Docker Hub, um registo privado de [contentores Azure,](../container-registry/index.yml)ou outro registo de estivadores baseado na nuvem. Visite as [FAQ](container-instances-faq.md) para saber quais os registos suportados pelo ACI. Azure Container Instances caches várias imagens de SO de base comum, ajudando a acelerar a implementação das suas imagens de aplicação personalizadas.

## <a name="container-access"></a>Acesso a contentores

Azure Container Instances permite expor os seus grupos de contentores diretamente à internet com um endereço IP e um nome de domínio totalmente qualificado (FQDN). Quando cria uma instância de contentor, pode especificar uma etiqueta de nome DNS personalizada para que a aplicação esteja acessível em *customlabel*.*azureregion*.azurecontainer.io.

A azure Container Instances também suporta a execução de um comando num recipiente de funcionamento, fornecendo uma concha interativa para ajudar no desenvolvimento de aplicações e resolução de problemas. O acesso passa por HTTPS, utilizando o TLS para garantir ligações ao cliente.

> [!IMPORTANT]
> A partir de 13 de janeiro de 2020, as Instâncias de Contentores Azure exigirão todas as ligações seguras dos servidores e aplicações para utilizar o TLS 1.2. O suporte para tLS 1.0 e 1.1 será retirado.

## <a name="compliant-deployments"></a>Implementações compatíveis

### <a name="hypervisor-level-security"></a>Segurança ao nível do hipervisor

Historicamente, os contentores têm vindo a oferecer isolamento de dependências de aplicações e governação de recursos, mas não têm sido considerados suficientemente fortalecidos para utilização multi-inquilinos hostil. O Azure Container Instances garante que a sua aplicação está tão isolada num contentor como estaria numa VM.

### <a name="customer-data"></a>Dados do cliente

O serviço ACI armazena os dados mínimos do cliente necessários para garantir que os seus grupos de contentores estão a funcionar como esperado. O armazenamento de dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Região Geo do Pacífico Asiático e Do Brasil Sul (Estado de São Paulo) do Brasil Geo. Para todas as outras regiões, os dados dos clientes são armazenados na [Geo.](https://azure.microsoft.com/global-infrastructure/geographies/) Por favor, entre em contato com a Azure Support para saber mais.

## <a name="custom-sizes"></a>Tamanhos personalizados

Normalmente, os contentores são otimizados para executarem apenas uma única aplicação, mas as necessidades exatas de cada uma dessas aplicações podem ser bastante diferentes. O Azure Container Instances proporciona uma utilização ideal ao permitir especificações exatas de núcleos de CPU e memória. A faturação é ao segundo e consoante o que precisar, para que possa ajustar os gastos com base nas suas necessidades reais.

Para trabalhos intensivos de computação, tais como machine learning, as instâncias de contentores do Azure podem agendar contentores Linux para utilizar [recursos GPU](container-instances-gpu.md) da NVIDIA Tesla (pré-visualização).

## <a name="persistent-storage"></a>Armazenamento persistente

Para recuperar e persistir estado com Azure Container Instances, oferecemos montagem direta [de ações da Azure Files](./container-instances-volume-azure-files.md) apoiadas pela Azure Storage.

## <a name="linux-and-windows-containers"></a>Contentores de Linux e Windows

O Azure Container Instances permite agendar contentores do Windows e do Linux com a mesma API. Basta especificar o tipo de SO quando cria os [grupos de contentor](container-instances-container-groups.md).

Algumas funcionalidades estão atualmente restritas aos contentores Linux:

* Vários contentores por grupo de contentores
* Montagem de volume[(Ficheiros Azure,](container-instances-volume-azure-files.md) [emptyDir,](container-instances-volume-emptydir.md) [GitRepo,](container-instances-volume-gitrepo.md) [segredo)](container-instances-volume-secret.md)
* [Métricas de utilização de recursos](container-instances-monitor.md) com monitor Azure
* [Implementação de rede virtual](container-instances-vnet.md)
* [Recursos da GPU](container-instances-gpu.md) (pré-visualização)

Para a implementação de contentores do Windows, utilize imagens com base em [imagens comuns do Windows](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="co-scheduled-groups"></a>Grupos agendados conjuntamente

O Azure Container Instances suporta o agendamento de [grupos de vários contentores](container-instances-container-groups.md) que partilhem um computador anfitrião, a rede local, o armazenamento e o ciclo de vida. Desta forma, pode combinar o contentor da aplicação principal com outros contentores que têm um papel secundário, tais como sidecars de registo.

## <a name="virtual-network-deployment"></a>Implementação de rede virtual

Azure Container Instances permite [a colocação de casos de contentores numa rede virtual Azure](container-instances-vnet.md). Quando implantados numa sub-rede dentro da sua rede virtual, as instâncias de contentores podem comunicar de forma segura com outros recursos na rede virtual, incluindo os que estão no local (através do [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute).](../expressroute/expressroute-introduction.md)

## <a name="next-steps"></a>Passos seguintes

Experimente implementar um contentor no Azure com um único comando através do nosso guia de início rápido:

> [!div class="nextstepaction"]
> [Início Rápido do Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
