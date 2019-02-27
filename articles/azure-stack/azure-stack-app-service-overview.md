---
title: Serviço de aplicações no Descrição geral do Azure Stack | Documentos da Microsoft
description: Descrição geral do serviço de aplicações no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: cbdf864c2c10d5eb1c0f9a903bd186370b96a72f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879643"
---
# <a name="app-service-on-azure-stack-overview"></a>Descrição geral do Serviço de Aplicações no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Serviço de aplicações do Azure no Azure Stack é uma oferta de plataforma-como-serviço (PaaS) do Microsoft Azure está disponível para o Azure Stack. O serviço ativa a seus clientes internos ou externos criar as funções do Azure, API e web, aplicativos para qualquer plataforma ou dispositivo. Podem integrar as suas aplicações em aplicações no local e automatizar os processos empresariais. Os operadores de cloud do Azure Stack podem executar aplicações dos clientes em máquinas virtuais (VMs) totalmente geridas e escolher os recursos de VM partilhados ou VMs dedicadas que preferem.

Serviço de aplicações do Azure permite-lhe automatizar processos empresariais e alojar APIs da nuvem. Como um serviço integrado único, o serviço de aplicações do Azure permite-lhe combinar vários componentes, como Web sites, as APIs REST e processos de negócios, numa única solução.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Por que oferece o serviço de aplicações do Azure no Azure Stack?

São a seguir apresentadas algumas funcionalidades-chave e capacidades do App Service:

- **Várias linguagens e arquiteturas**: Serviço de aplicações tem suporte de primeira classe para ASP.NET, node. js, Java, PHP e Python. Pode também executar o Windows PowerShell e outros scripts ou executáveis em VMs do App Service.
- **Otimização de DevOps**: Configure a integração contínua e implementação com o GitHub, local Git ou o BitBucket. Pode promover atualizações através de ambientes de teste e gerir as suas aplicações no serviço de aplicações com o Azure PowerShell ou a interface de linha de comandos para várias plataformas (CLI).
- **Integração do Visual Studio**: Ferramentas dedicadas do Visual Studio simplificam o trabalho de criação e implantação de aplicativos.

## <a name="app-types-in-app-service"></a>Tipos de aplicação no App Service

Serviço de aplicações oferece vários tipos de aplicação, sendo que cada um concebido para alojar uma carga de trabalho específica:

- [Aplicações Web](../app-service/overview.md) para alojar sites e aplicações web.
- [Aplicações API](../app-service/overview.md) para alojar REST APIs.
- Funções do Azure para alojar o evento condicionada por cargas de trabalho sem servidor.

A palavra *aplicação* refere-se ao alojamento de recursos dedicados à execução de uma carga de trabalho. Levando *aplicação web* como por exemplo, está provavelmente habituado a pensar de uma aplicação web, como os recursos de computação e o aplicativo de código essa funcionalidade de entrega em conjunto para um browser. No serviço de aplicações, uma aplicação web é o recurso de computação do Azure Stack fornece para alojar o código da aplicação.

Seu aplicativo pode ser composto por várias aplicações de serviço de aplicações de diferentes tipos. Por exemplo, se a sua aplicação é composta por um front-end da web e de fim de volta uma API de REST, pode:

- Implementar ambos (front-end e api) numa aplicação Web única
- Implementar o código de front-end numa aplicação Web e o código de back-end numa aplicação API.

   [![Descrição geral do serviço de aplicações com dados de monitorização](media/azure-stack-app-service-overview/image01.png "visão geral do serviço de aplicações com dados de monitorização")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>O que é um plano do Serviço de Aplicações?

O fornecedor de recursos do serviço de aplicações utiliza o mesmo código que utiliza o serviço de aplicações do Azure e, portanto, compartilha alguns conceitos comuns. No serviço de aplicações, denomina-se o contentor de preços para as aplicações do *plano do App Service*. Ele representa o conjunto de máquinas virtuais dedicadas, utilizado para armazenar as suas aplicações. Dentro de uma determinada subscrição, pode ter vários planos de serviço de aplicações.

No Azure, existem funções de trabalho dedicadas e partilhadas. Uma função de trabalho compartilhada suporta o alojamento de alta densidade aplicações de multi-inquilino e não existe apenas um conjunto de funções de trabalho partilhados. Servidores dedicados são utilizados por apenas um inquilino e são fornecidos em três tamanhos: pequeno, médio e grande. As necessidades dos clientes no local sempre não podem ser descritas usando esses termos. No serviço de aplicações no Azure Stack, os administradores do fornecedor de recursos podem definir os escalões de worker que pretendem disponibilizar. Com base nas suas necessidades exclusivas de hospedagem, pode definir vários conjuntos de funções de trabalho compartilhadas ou conjuntos diferentes de funções de trabalho dedicados. Ao utilizar as definições de escalão de worker, podem, em seguida, definir suas próprias SKUs de preços.

## <a name="portal-features"></a>Recursos de portal

Serviço de aplicações no Azure Stack utiliza a mesma interface de utilizador que utiliza o serviço de aplicações do Azure. O mesmo acontece com o back-end. No entanto, algumas funcionalidades estão desativadas no Azure Stack. As expectativas específicos do Azure ou serviços que exigem essas funcionalidades não estão atualmente disponíveis no Azure Stack.

## <a name="next-steps"></a>Passos Seguintes

- [Antes de começar com o serviço de aplicações no Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instalar o fornecedor de recursos do serviço de aplicações](azure-stack-app-service-deploy.md)

Também pode tentar outros [plataforma como um serviço de serviço (PaaS)](azure-stack-tools-paas-services.md), como o [fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md) e o [fornecedor de recursos do MySQL](azure-stack-mysql-resource-provider-deploy.md).
