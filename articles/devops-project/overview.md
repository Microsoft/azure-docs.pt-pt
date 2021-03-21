---
title: Visão geral do DevOps Starter para Azure | Microsoft Docs
description: Compreenda o valor para DevOps Starter
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
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330653"
---
# <a name="overview-of-devops-starter"></a>Visão geral do DevOps Starter

 O DevOps Starter facilita o arranque do Azure utilizando ações do GitHub ou do Azure DevOps. Ajuda-o a lançar a sua aplicação preferida no serviço Azure à sua escolha a poucos passos do portal Azure. 

 O DevOps Starter configura tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. Pode utilizar o painel de arranque de DevOps para monitorizar compromissos, construções e implementações de código, tudo a partir de uma única vista no portal Azure.

## <a name="advantages-of-using-devops-starter"></a>Vantagens de usar o DevOps Starter

  DevOps starter o seguinte suporta dois fornecedores de CI/CD, para automatizar as suas implementações
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  O DevOps Starter automatiza a configuração de toda uma integração contínua (CI) e entrega contínua (CD) para a sua aplicação ao Azure.  Pode começar pelo código existente ou utilizar uma das aplicações de amostra fornecidas. Em seguida, pode implementar rapidamente essa aplicação em vários serviços Azure, tais como Máquinas Virtuais, Serviço de Aplicações, Serviços Azure Kubernetes (AKS), Base de Dados Azure SQL e Azure Service Fabric.  

  O DevOps Starter faz todo o trabalho para a configuração inicial de um oleoduto DevOps, incluindo tudo, desde a criação do repositório inicial do Git, configurando o pipeline CI/CD, criando um recurso application Insights para monitorização, e fornecendo uma única visão de toda a solução com a criação de um dashboard DevOps Starter no portal Azure.

Pode utilizar o DevOps Starter para:

* Implementar rapidamente a sua aplicação no Azure
* Automatizar a configuração de um fluxo de trabalho ou oleoduto CI/CD
* Ver e compreender como configurar corretamente um fluxo de trabalho ou oleoduto CI/CD
* Personalize ainda mais os oleodutos de libertação com base nos seus cenários específicos

## <a name="how-to-use-devops-starter"></a>Como utilizar o DevOps Starter?

  O DevOps Starter está disponível a partir do portal Azure. Cria um recurso DevOps Starter tal como cria qualquer outro recurso Azure a partir do portal. DevOps Projects proporciona uma experiência passo a passo como um assistente para as várias opções de configuração.  

Pode escolher várias opções de configuração como parte da configuração inicial. As opções incluem:

* Selecionando o seu fornecedor de CI/CD a favor
* Utilizando a aplicação de amostra fornecida ou trazendo o seu próprio código (apenas para Azure DevOps)
* Selecionando um idioma de aplicação
* Escolher uma estrutura de aplicativos com base no idioma
* Selecionando um serviço Azure (alvo de implantação)
* Selecione a sua organização GitHub ou Azure DevOps
* Escolher a sua subscrição Azure
* Escolher a localização dos serviços da Azure
* Escolha entre vários níveis de preços para serviços Azure

Depois de criar o seu DevOps Starter, pode:

* Personalize o seu fluxo de trabalho GitHub ou pipeline Azure DevOps
* Utilizar os pedidos Pull para gerir o seu fluxo de códigos e manter uma qualidade elevada
* Testar e compilar cada consolidação antes de unir o seu código para aumentar a qualidade

Depois de utilizar o DevOps Starter, também pode eliminar todos os recursos de um único local a partir do painel de arranque de DevOps no portal Azure.

## <a name="devops-starter-and-github-integration"></a>DevOps Starter e integração GitHub

O DevOps Starter suporta agora as ações do GitHub como fornecedor de CI/CD. Automatiza todo o trabalho necessário no GitHub para criar um fluxo de trabalho ci/CD usando as ações do GitHub. Cria um repositório GitHub numa organização gitHub existente, e depois compromete uma aplicação de amostra para o novo repositório GitHub.  

A automatização também estabelece um gatilho para o fluxo de trabalho para que cada novo código comprometa iniciar um trabalho de construção e implantação dentro do fluxo de trabalho. A aplicação é implantada no serviço Azure à sua escolha. O fluxo de trabalho do GitHub pode ser personalizado para cenários adicionais. 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter e Integração de DevOps Azure

O DevOps Starter utilizando o Azure DevOps automatiza todo o trabalho necessário nos gasodutos Azure para configurar um pipeline CI/CD. Cria um repositório git numa nova organização Azure DevOps ou existente, e em seguida, compromete uma aplicação de amostra ou o seu código existente para um novo repositório git.  

A automatização também estabelece um gatilho de CI para a construção para que cada novo código comprometa uma construção. DevOps Starter cria um gatilho de CD e implementa cada nova construção bem sucedida para o serviço Azure à sua escolha.  

Os pipelines de compilação e versão podem ser personalizados para mais cenários. Além disso, pode clonar os oleodutos de construção e libertação para uso em outros projetos.

## <a name="getting-started-with-devops-starter"></a>Começando com DevOps Starter

* [Começar com DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Vídeos de arranque de DevOps

* [Criar CI/CD com Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
