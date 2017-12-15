---
title: "Descrição geral do Azure Container Instances"
description: Compreender o Azure Container Instances
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 98feed192f21cbc2bf9197b6d32646f50ddbd0c5
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-instances"></a>Azure Container Instances

Os contentores estão a tornar-se rapidamente na forma preferida de empacotar, implementar e gerir aplicações na cloud. O Azure Container Instances é a forma mais rápida e simples de executar um contentor no Azure, sem que tenha de aprovisionar máquinas virtuais ou de adotar um serviço de nível mais elevado.

O Azure Container Instances é uma ótima solução para qualquer cenário que possa funcionar em contentores isolados, incluindo aplicações simples, automatização de tarefas e tarefas de compilação. Em cenários nos quais precisa de orquestração de contentores completa, incluindo deteção de serviços em vários contentores, dimensionamento automático e atualizações coordenadas de aplicações, recomendamos o [Azure Container Service](https://docs.microsoft.com/azure/container-service/).

## <a name="fast-startup-times"></a>Tempos de arranque rápidos

Os contentores oferecem benefícios de arranque significativos em relação às máquinas virtuais. Com o Azure Container Instances, pode iniciar um contentor no Azure em segundos sem ter de aprovisionar e gerir VMs.

## <a name="hypervisor-level-security"></a>Segurança ao nível do hipervisor

Historicamente, os contentores têm vindo a oferecer isolamento de dependências de aplicações e governação de recursos, mas não têm sido considerados suficientemente fortalecidos para utilização multi-inquilinos hostil. Com o Azure Container Instances, a sua aplicação está tão isolada num contentor como estaria numa VM.

## <a name="custom-sizes"></a>Tamanhos personalizados

Normalmente, os contentores são otimizados para executarem apenas uma única aplicação, mas as necessidades exatas de cada uma dessas aplicações podem ser bastante diferentes. Com o Azure Container Instances, pode pedir exatamente aquilo de que precisa em termos de núcleos de CPU e memória. A faturação é ao segundo e consoante o que pedir, para que possa otimizar eficientemente os gastos com base nas suas necessidades.

## <a name="public-ip-connectivity"></a>Conectividade de IP público

Com o Azure Container Instances, pode expor os seus contentores diretamente na Internet com um endereço IP público. No futuro, vamos expandir as nossas capacidades de rede, de modo a que incluam a integração com redes virtuais, balanceadores de carga e outras partes centrais da infraestrutura de rede do Azure.

## <a name="persistent-storage"></a>Armazenamento persistente

Para obter e persistir o estado com o Azure Container Instances, ofereceremos a montagem direta de partilhas de ficheiros do Azure.

## <a name="linux-and-windows-containers"></a>Contentores de Linux e Windows

O Azure Container Instances permite-lhe agendar contentores do Windows e do Linux com a mesma API. Basta indicar o tipo de SO base e tudo o resto é igual.

## <a name="co-scheduled-groups"></a>Grupos agendados conjuntamente

O Azure Container Instances suporta o agendamento de grupos de vários contentores que partilhem um computador anfitrião, a rede local, o armazenamento e o ciclo de vida. Desta forma, pode combinar a sua aplicação principal com outras que desempenhem um papel de apoio, como, por exemplo, um papel de registo.

## <a name="next-steps"></a>Passos seguintes

Experimente implementar um contentor no Azure com um único comando através do nosso [guia de início rápido](container-instances-quickstart.md).