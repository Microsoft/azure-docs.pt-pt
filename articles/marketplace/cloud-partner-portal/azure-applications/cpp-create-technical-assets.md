---
title: Criar ativos técnicos da aplicação do Azure | Documentos da Microsoft
description: Crie os recursos técnicos para uma oferta de aplicação do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a498fb2bc3efcc3a081a0ab854df107135a4e008
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104697"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Preparar os recursos técnicos de aplicação do Azure

Este artigo descreve os recursos para preparar os recursos técnicos para a sua oferta de aplicação do Azure.

## <a name="before-you-begin"></a>Antes de começar

Reveja o seguinte vídeo [criando modelos de soluções e aplicações geridas para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), uma descrição geral sobre como criar um modelo do Azure Resource Manager para definir uma solução de aplicação do Azure e, em seguida, como em seguida, publicar a oferta da aplicação no Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Reveja a seguinte documentação de aplicações do Azure, que fornece inícios rápidos, tutoriais e amostras.

- [Compreender os modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Inícios rápidos:

  - [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/)
  - [Modelos de início rápido do Azure do GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publicar a definição da aplicação](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Implementar o serviço catálogo de aplicações](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Tutoriais:

  - [Criar ficheiros de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicar a aplicação do mercado](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Exemplos:

    - [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Soluções de aplicação gerida](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Conhecimentos técnicos fundamentais

Estruturar, criar e testar esses ativos demorar algum tempo e requer conhecimento técnico de plataforma do Azure e as tecnologias usadas para criar a oferta.

A equipa de engenharia deve ter conhecimento sobre as tecnologias da Microsoft seguintes:

- Noções básicas sobre [dos serviços do Azure](https://azure.microsoft.com/services/)
- Como [de design e a arquitetura de aplicações do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento prático do [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage), e [redes do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento prático do [do Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento prático do [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Sugestões de ferramentas

Escolha um ou ambos dos seguintes ambientes de criação de scripts para o ajudar a gerir a sua aplicação do Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

Recomendamos que adicione as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) com as seguintes extensões:

  - Extensão: [Ferramentas do Gestor de recursos do Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerimos também a rever as ferramentas disponíveis no [ferramentas de programação do Azure](https://azure.microsoft.com/tools/) página e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Passos Seguintes

[Criar oferta de aplicação do Azure](./cpp-create-offer.md)

