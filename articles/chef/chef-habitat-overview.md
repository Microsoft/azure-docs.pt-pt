---
title: Utilizar o Habitat para implementar a aplicação no Azure
description: Aprenda a implementar de forma consistente a sua aplicação para máquinas virtuais do Azure e contentores
keywords: Azure, chef, devops, máquinas virtuais, visão geral, automatizar, habitat
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 2bdcd4c504822a2e60156b0ac565465e0cf23a85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60388816"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Utilizar o Habitat para implementar a aplicação no Azure
[Habitat](https://www.habitat.sh/) é um sistema de tempo de execução e empacotamento de aplicativo que agrupa em conjunto, como a unidade de implementação do aplicativo e sua automação. Esta ação cria o ultimate portabilidade da aplicação, permitindo que ele devem ser implantados nos contentores, máquinas virtuais, bare-metal ou PaaS, sem uma reescrita ou o reempacotamento.

Este artigo descreve os principais benefícios do uso Habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernize e mover os aplicativos herdados
Aplicativos herdados gratuitos da mais antigas de sistemas operativos e middleware reempacotando-los com o Habitat. O artefacto resultante é portátil e facilmente replatforms numa infra-estrutura mais recente, como máquinas virtuais ou contentores em execução na cloud.

## <a name="accelerate-container-adoption"></a>Acelerar a adoção de contentor
Habitat resolve a implementação contínua de aplicativos complexos e orientadas para microsserviços com precisão que representa as dependências de tempo de execução. Vá além da simple implementação de azul/verde de componentes individuais e a arquitetura de comportamento de implementação sofisticadas sem gerar fluxos de orquestração complexa.

## <a name="run-any-application-anywhere"></a>Executar qualquer aplicação em qualquer lugar
Com o Habitat, aplicativos podem ser executados sem modificações em qualquer ambiente de tempo de execução. Isso inclui tudo, desde bare-metal e máquinas virtuais para contentores (por exemplo, o Docker), sistemas de gerenciamento de cluster (por exemplo, Mesosphere ou Kubernetes) e sistemas de PaaS (por exemplo, o Cloud Foundry da Pivotal).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrar o fluxo de trabalho de DevOps do Chef
O projeto de Habitat é um de um projeto de código-fonte aberto da Chef Software, com uma forte Comunidade de suporte. Habitat tira partido experiência do Chef em automatização de infraestruturas para trazer capacidades de automatização sem precedentes para os aplicativos. Chef oferece suporte comercial para Habitat e cria uma integração perfeita entre Habitat e Chef Automate para automatizar totalmente o ciclo de lançamento de aplicação, desde o desenvolvimento para a implementação.

## <a name="next-steps"></a>Passos Seguintes

* [Experimente o Habitat](https://www.habitat.sh/learn/)
