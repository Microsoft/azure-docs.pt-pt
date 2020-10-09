---
title: Visão geral do Azure DevOps Starter / Microsoft Docs
description: Saiba como o Azure DevOps Starter ajuda a lançar, implementar e gerir a sua aplicação a partir de uma única vista no portal Azure.
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
ms.openlocfilehash: 3e6f28da37980717e15b05ab94c582aa5c77e9e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856867"
---
# <a name="overview-of-azure-devops-starter"></a>Visão geral do Azure DevOps Starter

 O Azure DevOps Starter facilita o arranque do Azure. Ajuda-o a lançar a sua aplicação preferida no serviço Azure à sua escolha a poucos passos do portal Azure. 

 O DevOps Starter configura tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. Pode utilizar o painel de arranque de DevOps para monitorizar compromissos, construções e implementações de código, tudo a partir de uma única vista no portal Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantagens de usar o DevOps Starter

  O DevOps Starter automatiza a configuração de todo um pipeline de integração contínua (CI) e entrega contínua (CD) para a Azure.  Pode começar pelo código existente ou utilizar uma das aplicações de amostra fornecidas. Em seguida, pode implementar rapidamente essa aplicação em vários serviços Azure, tais como Máquinas Virtuais, Serviço de Aplicações, Serviços Azure Kubernetes (AKS), Base de Dados Azure SQL e Azure Service Fabric.  

  O DevOps Starter faz todo o trabalho para a configuração inicial de um oleoduto DevOps, incluindo tudo, desde a criação do repositório inicial de Git, configurando o pipeline CI/CD, criando um recurso application Insights para monitorização, e fornecendo uma única visão de toda a solução com a criação de um dashboard de Projetos DevOps no portal Azure.

Pode utilizar o DevOps Starter para:

* Implementar rapidamente a sua aplicação no Azure
* Automatizar a instalação de um gasoduto CI/CD
* Ver e compreender como configurar corretamente um pipeline CI/CD
* Personalize ainda mais os oleodutos de libertação com base nos seus cenários específicos

## <a name="how-to-use-devops-starter"></a>Como utilizar o DevOps Starter?

  O DevOps Starter está disponível a partir do portal Azure. Cria um recurso DevOps Starter tal como cria qualquer outro recurso Azure a partir do portal. DevOps Projects proporciona uma experiência passo a passo como um assistente para as várias opções de configuração.  

Pode escolher várias opções de configuração como parte da configuração inicial. As opções incluem:

* Usando a aplicação de amostra fornecida, ou trazendo o seu próprio código
* Selecionando um idioma de aplicação
* Escolher uma estrutura de aplicativos com base no idioma
* Selecionando um serviço Azure (alvo de implantação)
* Criar uma nova organização Azure DevOps ou usar uma organização existente 
* Escolher a sua subscrição Azure
* Escolher a localização dos serviços da Azure
* Escolha entre vários níveis de preços para serviços Azure

Depois de utilizar o DevOps Starter, também pode eliminar todos os recursos de um único local a partir do painel de arranque de DevOps no portal Azure.

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter e Integração de DevOps Azure

O DevOps Starter é alimentado pela Azure DevOps. O DevOps Starter automatiza todo o trabalho necessário nos Gasodutos Azure para criar um pipeline CI/CD. Cria um repositório git numa nova organização Azure DevOps ou existente, e em seguida, compromete uma aplicação de amostra ou o seu código existente para um novo repositório git.  

A automatização também estabelece um gatilho de CI para a construção para que cada novo código comprometa uma construção. DevOps Starter cria um gatilho de CD e implementa cada nova construção bem sucedida para o serviço Azure à sua escolha.  

Os pipelines de compilação e versão podem ser personalizados para mais cenários. Além disso, pode clonar os oleodutos de construção e libertação para uso em outros projetos.

Depois de criar o seu DevOps Starter, pode:

* Personalizar a sua compilação e pipeline da versão
* Utilizar os pedidos Pull para gerir o seu fluxo de códigos e manter uma qualidade elevada
* Testar e compilar cada consolidação antes de unir o seu código para aumentar a qualidade
* Acompanhe o seu atraso e os seus problemas bem juntamente com a sua aplicação

## <a name="getting-started-with-devops-starter"></a>Começando com DevOps Starter

* [Começar com DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Vídeos de arranque de DevOps

* [Criar CI/CD com Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)