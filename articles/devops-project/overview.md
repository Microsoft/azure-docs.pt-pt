---
title: Visão geral de Azure DevOps Projects | Microsoft Docs
description: Entender o valor de Azure DevOps Projects
services: devops-project
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/03/2018
ms.author: mlearned
ms.openlocfilehash: fed4f0976dd118186c122b0152f255d0c452c0ce
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404496"
---
# <a name="overview-of-azure-devops-projects"></a>Visão geral do Azure DevOps Projects

 Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Ele ajuda a iniciar seu aplicativo favorito no serviço do Azure de sua escolha em apenas algumas etapas rápidas do portal do Azure. 

 DevOps Projects configura tudo o que você precisa para desenvolver, implantar e monitorar seu aplicativo. Você pode usar o painel de DevOps Projects para monitorar confirmações, compilações e implantações de código, tudo a partir de uma única exibição no portal do Azure.

## <a name="why-should-i-use-devops-projects"></a>Por que devo usar DevOps Projects?

  O projeto DevOps automatiza a configuração de um pipeline de integração contínua (CI) e entrega contínua (CD) para o Azure.  Você pode começar com o código existente ou usar um dos aplicativos de exemplo fornecidos. Em seguida, você pode implantar rapidamente esse aplicativo em vários serviços do Azure, como máquinas virtuais, serviço de aplicativo, AKS (serviços de kubernetes do Azure), banco de dados SQL do Azure e Service Fabric do Azure.  

  DevOps Projects faz todo o trabalho para a configuração inicial de um pipeline DevOps, incluindo tudo, desde a configuração do repositório git inicial, a configuração do pipeline de CI/CD, a criação de um recurso de Application Insights para monitoramento e o fornecimento de um único exibição de toda a solução com a criação de um painel de DevOps Projects no portal do Azure.

Você pode usar DevOps Projects para:

* Implementar rapidamente a sua aplicação no Azure
* Automatizar a configuração de um pipeline de CI/CD
* Exibir e entender como configurar corretamente um pipeline de CI/CD
* Personalize ainda mais os pipelines de lançamento com base em seus cenários específicos

## <a name="how-do-i-use-devops-projects"></a>Como fazer usar DevOps Projects?

  DevOps Projects está disponível na portal do Azure. Você cria um recurso de DevOps Projects assim como você cria qualquer outro recurso do Azure no Portal. O DevOps Projects fornece uma experiência passo a passo com o assistente para as várias opções de configuração.  

Pode escolher várias opções de configuração como parte da configuração inicial. As opções incluem:

* Usando o aplicativo de exemplo fornecido ou trazendo seu próprio código
* Selecionando um idioma de aplicativo
* Escolhendo uma estrutura de aplicativo com base no idioma
* Selecionando um serviço do Azure (destino de implantação)
* Criando uma nova organização do Azure DevOps ou usando uma organização existente 
* Escolhendo sua assinatura do Azure
* Escolhendo o local dos serviços do Azure
* Escolhendo entre vários tipos de preço para os serviços do Azure

Depois de usar DevOps Projects, você também pode excluir todos os recursos de um único lugar do painel de DevOps Projects no portal do Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>DevOps Projects e integração do Azure DevOps

O DevOps Projects é fornecido pelo Azure DevOps. DevOps Projects automatiza todo o trabalho necessário no Azure Pipelines para configurar um pipeline de CI/CD. Ele cria um repositório git em uma organização nova ou existente do Azure DevOps e, em seguida, confirma um aplicativo de exemplo ou seu código existente para um novo repositório git.  

A automação também estabelece um gatilho de CI para a compilação de forma que cada nova confirmação de código inicie uma compilação. DevOps Projects cria um gatilho de CD e implanta todas as novas compilações bem-sucedidas para o serviço do Azure de sua escolha.  

Os pipelines de compilação e versão podem ser personalizados para mais cenários. Além disso, você pode clonar os pipelines de compilação e versão para uso em outros projetos.

Depois de criar o seu Projeto de DevOps, pode:

* Personalizar a sua compilação e pipeline da versão
* Utilizar os pedidos Pull para gerir o seu fluxo de códigos e manter uma qualidade elevada
* Testar e compilar cada consolidação antes de unir o seu código para aumentar a qualidade
* Acompanhe suas pendências e problemas bem junto com seu aplicativo

## <a name="how-do-i-start-using-devops-projects"></a>Como fazer começar a usar o DevOps Projects?

* [Introdução ao DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Vídeos de DevOps Projects

* [Criar CI/CD com Azure DevOps Projects](https://www.youtube.com/watch?v=NuYDAs3kNV8)
