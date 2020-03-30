---
title: Visão geral dos projetos Azure DevOps [ Microsoft Docs
description: Compreender o valor dos projetos Azure DevOps
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: d39702f8e30046dd3cf634fc67ed7095471cd629
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "71971481"
---
# <a name="overview-of-azure-devops-projects"></a>Visão geral dos projetos Azure DevOps

 A Azure DevOps Projects facilita o início do Azure. Ajuda-o a lançar a sua aplicação preferida no serviço Azure à sua escolha em poucos passos rápidos do portal Azure. 

 A DevOps Projects configura tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. Você pode usar o dashboard DevOps Projects para monitorizar compromissos de código, construções e implementações, tudo de uma única vista no portal Azure.

## <a name="why-should-i-use-devops-projects"></a>Por que devo usar projetos DevOps?

  O Projeto DevOps automatiza a configuração de todo um oleoduto de integração contínua (CI) e entrega contínua (CD) ao Azure.  Pode começar com o código existente ou utilizar uma das aplicações de amostra fornecidas. Em seguida, você pode rapidamente implementar essa aplicação para vários serviços Azure, tais como Máquinas Virtuais, Serviço de Aplicações, Serviços Azure Kubernetes (AKS), Base de Dados Azure SQL e Tecido de Serviço Azure.  

  A DevOps Projects faz todo o trabalho para a configuração inicial de um oleoduto DevOps, incluindo tudo, desde a criação do repositório inicial git, configuração do pipeline CI/CD, criação de um recurso Application Insights para monitorização, e proporcionando uma única visão de toda a solução com a criação de um dashboard DevOps Projects no portal Azure.

Você pode usar Projetos DevOps para:

* Implementar rapidamente a sua aplicação no Azure
* Automatizar a instalação de um oleoduto CI/CD
* Ver e entender como configurar corretamente um oleoduto CI/CD
* Personalize ainda mais os oleodutos de lançamento com base nos seus cenários específicos

## <a name="how-do-i-use-devops-projects"></a>Como uso projetos DevOps?

  A DevOps Projects está disponível no portal Azure. Cria um recurso de Projetos DevOps tal como cria qualquer outro recurso Azure a partir do portal. A DevOps Projects proporciona uma experiência passo-a-passo para as várias opções de configuração.  

Pode escolher várias opções de configuração como parte da configuração inicial. As opções incluem:

* Usando a aplicação de amostra fornecida, ou trazendo o seu próprio código
* Selecionar um idioma de aplicativo
* Escolher um quadro de aplicativos baseado na linguagem
* Selecionar um serviço Azure (alvo de implantação)
* Criar uma nova organização Azure DevOps ou usar uma organização existente 
* Escolha a sua subscrição Azure
* Escolher a localização dos serviços do Azure
* Escolha de vários níveis de preços para serviços Azure

Depois de utilizar os Projetos DevOps, também pode eliminar todos os recursos de um único local do painel de instrumentos de Projetos DevOps no portal Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>Projetos DevOps e integração azure DevOps

A DevOps Projects é alimentada pela Azure DevOps. A DevOps Projects automatiza todo o trabalho necessário em Pipelines Azure para criar um oleoduto CI/CD. Cria um repositório Git numa organização nova ou existente da Azure DevOps, e depois comete uma aplicação de amostra ou o seu código existente para um novo repositório Git.  

A automatização também estabelece um gatilho CI para a construção para que cada novo código comprometa iniciar uma construção. A DevOps Projects cria um gatilho de CD e implementa todas as novas construções bem sucedidas para o serviço Azure à sua escolha.  

Os pipelines de compilação e versão podem ser personalizados para mais cenários. Além disso, pode clonar os oleodutos de construção e lançamento para utilização noutros projetos.

Depois de criar o seu Projeto de DevOps, pode:

* Personalizar a sua compilação e pipeline da versão
* Utilizar os pedidos Pull para gerir o seu fluxo de códigos e manter uma qualidade elevada
* Testar e compilar cada consolidação antes de unir o seu código para aumentar a qualidade
* Acompanhe o seu atraso e problemas junto com a sua aplicação

## <a name="how-do-i-start-using-devops-projects"></a>Como posso começar a usar projetos DevOps?

* [Começar com projetos DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Vídeos de Projetos DevOps

* [Criar CI/CD com projetos Azure DevOps](https://www.youtube.com/watch?v=NuYDAs3kNV8)
