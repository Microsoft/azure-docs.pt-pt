---
title: Descrição geral dos projetos de Azure DevOps | Documentos da Microsoft
description: Compreender o valor para projetos de DevOps do Azure
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
ms.openlocfilehash: 6ae2d0723b195ed59f8c6a7320245509145094df
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776602"
---
# <a name="overview-of-azure-devops-projects"></a>Descrição geral dos projetos de Azure DevOps

 Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Ajuda-o inicie a sua aplicação favorita no serviço do Azure à sua escolha em apenas alguns passos rápidos do portal do Azure. 

 Projetos de DevOps configura tudo o que precisa para desenvolver, implementar e monitorizar a sua aplicação. Pode utilizar o dashboard de projetos de DevOps consolidações de código do monitor, compilações e implementações, tudo a partir de uma única vista no portal do Azure.

## <a name="why-should-i-use-devops-projects"></a>Por que motivo devo utilizar projetos de DevOps?

  Projeto de DevOps automatiza a configuração de um inteira integração contínua (CI) e o pipeline de entrega contínua (CD) para o Azure.  Pode começar com o código existente ou utilize uma das aplicações de exemplo fornecido. Em seguida, pode implementar rapidamente essa aplicação para vários serviços do Azure como máquinas virtuais, o serviço de aplicações, serviços de Kubernetes do Azure (AKS), base de dados do Azure SQL e do Azure Service Fabric.  

  Projetos de DevOps faz todo o trabalho para a configuração inicial de um pipeline de DevOps, incluindo tudo, desde como configurar o repositório de Git inicial, configurar o pipeline de CI/CD, criação de um recurso do Application Insights para monitorização e fornecer uma única Vista de toda a solução com a criação de um dashboard de projetos de DevOps no portal do Azure.

Pode utilizar projetos de DevOps para:

* Implementar rapidamente a sua aplicação no Azure
* Automatizar a configuração de um pipeline CI/CD
* Ver e compreender como configurar corretamente a um pipeline CI/CD
* Personalizar ainda mais os pipelines de versão com base nos seus cenários específicos

## <a name="how-do-i-use-devops-projects"></a>Como posso utilizar projetos de DevOps?

  Projetos de DevOps está disponível a partir do portal do Azure. Criar um recurso de projetos de DevOps tal como criar qualquer outro recurso do Azure a partir do portal. Projetos de DevOps fornece uma experiência semelhante ao assistente passo a passo para as várias opções de configuração.  

Pode escolher várias opções de configuração como parte da configuração inicial. As opções incluem:

* Utilizar a aplicação de exemplo fornecidos, ou trazer o seu próprio código
* Selecionar um idioma de aplicação
* Escolher uma estrutura de aplicação com base no idioma
* Selecionar um serviço do Azure (destino de implementação)
* Criar uma nova organização de DevOps do Azure ou através de uma organização existente 
* Escolher a sua subscrição do Azure
* Escolher a localização de serviços do Azure
* Escolher a partir de vários escalões de preço para serviços do Azure

Depois de utilizar projetos de DevOps, também pode eliminar todos os recursos de um único lugar a partir do dashboard de projetos de DevOps no portal do Azure.

## <a name="devops-projects-and-azure-devops-integration"></a>Integração de projetos de DevOps e de DevOps do Azure

Projetos de DevOps com tecnologia do Azure DevOps. Projetos de DevOps automatiza todo o trabalho que é necessário nos Pipelines do Azure para configurar um pipeline CI/CD. Cria um repositório de Git numa organização de DevOps do Azure nova ou existente e, em seguida, consolida um exemplo de aplicação ou o seu código existente para um novo repositório de Git.  

A automação também estabelece um acionador de CI para a compilação, de modo a que cada consolidação de código novo inicia uma compilação. Projetos de DevOps cria um acionador de CD e implementa todas as novas compilações com êxito ao serviço do Azure à sua escolha.  

Os pipelines de compilação e versão podem ser personalizados para mais cenários. Além disso, pode clonar os pipelines de compilação e versão para uso em outros projetos.

Depois de criar o seu Projeto de DevOps, pode:

* Personalizar a sua compilação e pipeline da versão
* Utilizar os pedidos Pull para gerir o seu fluxo de códigos e manter uma qualidade elevada
* Testar e compilar cada consolidação antes de unir o seu código para aumentar a qualidade
* Monitorize a lista de pendências e problemas diretamente junto com a sua aplicação

## <a name="how-do-i-start-using-devops-projects"></a>Como posso começar a utilizar projetos de DevOps?

* [Começar com projetos de DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-projects-videos"></a>Vídeos de projetos de DevOps

* [Criar CI/CD com projetos de Azure DevOps](https://channel9.msdn.com/Events/Connect/2017/T174/player/)
