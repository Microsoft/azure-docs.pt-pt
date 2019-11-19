---
title: Usar o habitat para implantar seu aplicativo no Azure
description: Saiba como implantar consistentemente seu aplicativo em contêineres e máquinas virtuais do Azure
keywords: Azure, chefe, DevOps, máquinas virtuais, visão geral, automatizar, habitat
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: de444836c04d1d37a19a006ce4eafdcae867ca5a
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158053"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Usar o habitat para implantar seu aplicativo no Azure
O [habitat](https://www.habitat.sh/) é um sistema de pacotes de tempo de execução e empacotamento de aplicativos que agrupa o aplicativo e sua automação juntos como a unidade de implantação. Isso cria portabilidade definitiva para o aplicativo, permitindo que ele seja implantado em contêineres, máquinas virtuais, bare-metal ou PaaS, sem uma regravação ou reempacotamento.

Este artigo descreve os principais benefícios do uso do habitat.

## <a name="modernize-and-move-legacy-applications"></a>Modernizar e mover aplicativos herdados
Aplicativos herdados gratuitos de sistemas operacionais mais antigos e middleware Reempacotando-os com habitat. O artefato resultante é portátil e facilmente replataforma na infraestrutura mais recente, como máquinas virtuais ou contêineres em execução na nuvem.

## <a name="accelerate-container-adoption"></a>Acelere a adoção do contêiner
O habitat resolve a implantação contínua de aplicativos complexos, orientados por microserviço, representando com precisão as dependências de tempo de execução. Vá além da simples implantação azul/verde de componentes individuais e projete um comportamento de implantação sofisticado sem gerar fluxos de orquestração complexos.

## <a name="run-any-application-anywhere"></a>Executar qualquer aplicativo em qualquer lugar
Com o habitat, os aplicativos podem ser executados sem modificações em qualquer ambiente de tempo de execução. Isso inclui tudo, desde máquinas virtuais e bare-metal a contêineres (como o Docker), sistemas de gerenciamento de cluster (como Mesosphere ou kubernetes) e sistemas PaaS (como o Cloud Foundry Pivotal).

## <a name="integrate-into-the-chef-devops-workflow"></a>Integre-se ao fluxo de trabalho do chefe DevOps
O projeto habitat é um de um projeto de código aberto do chefe software, com uma forte comunidade de suporte. O habitat aproveita a experiência profunda do chefe com a automação de infraestrutura para trazer recursos de automação sem precedentes aos aplicativos. O chefe oferece suporte comercial para habitat e cria uma integração direta entre habitat e chefe automatizado para automatizar totalmente o ciclo de liberação do aplicativo, desde o desenvolvimento até a implantação.

## <a name="next-steps"></a>Passos seguintes

* [Experimente o habitat](https://www.habitat.sh/learn/)
