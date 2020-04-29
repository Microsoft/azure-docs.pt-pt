---
title: Visão geral do Arranque de Azure DevOps [ Microsoft Docs
description: Compreenda o valor para O Arranque de Azure DevOps
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
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 99a1fdb8caff9953041c996d0f5581318ce11c66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233691"
---
# <a name="overview-of-azure-devops-starter"></a>Visão geral do arranque de Azure DevOps

 O Arranque azure DevOps facilita o arranque do Azure. Ajuda-o a lançar a sua aplicação preferida no serviço Azure à sua escolha em poucos passos rápidos do portal Azure. 

 DevOps Starter configura tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. Pode utilizar o painel de arranque de DevOps para monitorizar os compromissos, construções e implementações de códigos, tudo a partir de uma única vista no portal Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantagens de usar devOps Starter

  DevOps Starter automatiza a configuração de toda uma integração contínua (CI) e de entrega contínua (CD) para o Azure.  Pode começar com o código existente ou utilizar uma das aplicações de amostra fornecidas. Em seguida, você pode rapidamente implementar essa aplicação para vários serviços Azure, tais como Máquinas Virtuais, Serviço de Aplicações, Serviços Azure Kubernetes (AKS), Base de Dados Azure SQL e Tecido de Serviço Azure.  

  DevOps Starter faz todo o trabalho para a configuração inicial de um oleoduto DevOps, incluindo tudo, desde a configuração do repositório inicial git, configuração do pipeline CI/CD, criação de um recurso Application Insights para monitorização, e proporcionando uma única visão de toda a solução com a criação de um dashboard DevOps Projects no portal Azure.

Pode utilizar o DevOps Starter para:

* Implementar rapidamente a sua aplicação no Azure
* Automatizar a instalação de um oleoduto CI/CD
* Ver e entender como configurar corretamente um oleoduto CI/CD
* Personalize ainda mais os oleodutos de lançamento com base nos seus cenários específicos

## <a name="how-to-use-devops-starter"></a>Como usar o DevOps Starter?

  DevOps Starter está disponível no portal Azure. Cria um recurso DevOps Starter tal como cria qualquer outro recurso Azure a partir do portal. A DevOps Projects proporciona uma experiência passo-a-passo para as várias opções de configuração.  

Pode escolher várias opções de configuração como parte da configuração inicial. As opções incluem:

* Usando a aplicação de amostra fornecida, ou trazendo o seu próprio código
* Selecionar um idioma de aplicativo
* Escolher um quadro de aplicativos baseado na linguagem
* Selecionar um serviço Azure (alvo de implantação)
* Criar uma nova organização Azure DevOps ou usar uma organização existente 
* Escolha a sua subscrição Azure
* Escolher a localização dos serviços do Azure
* Escolha de vários níveis de preços para serviços Azure

Depois de utilizar o DevOps Starter, também pode eliminar todos os recursos de um único local do painel de arranque de DevOps no portal Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter e Azure DevOps integração

DevOps Starter é alimentado por Azure DevOps. DevOps Starter automatiza todo o trabalho necessário em Pipelines Azure para configurar um oleoduto CI/CD. Cria um repositório Git numa organização nova ou existente da Azure DevOps, e depois comete uma aplicação de amostra ou o seu código existente para um novo repositório Git.  

A automatização também estabelece um gatilho CI para a construção para que cada novo código comprometa iniciar uma construção. DevOps Starter cria um gatilho de CD e implementa cada nova construção bem sucedida para o serviço Azure à sua escolha.  

Os pipelines de compilação e versão podem ser personalizados para mais cenários. Além disso, pode clonar os oleodutos de construção e lançamento para utilização noutros projetos.

Depois de criar o seu Starter DevOps, pode:

* Personalizar a sua compilação e pipeline da versão
* Utilizar os pedidos Pull para gerir o seu fluxo de códigos e manter uma qualidade elevada
* Testar e compilar cada consolidação antes de unir o seu código para aumentar a qualidade
* Acompanhe o seu atraso e problemas junto com a sua aplicação

## <a name="getting-started-with-devops-starter"></a>Começando com DevOps Starter

* [Começar com DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Vídeos de arranque de DevOps

* [Criar CI/CD com Arranque de DevOps Azure](https://www.youtube.com/watch?v=NuYDAs3kNV8)
