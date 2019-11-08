---
title: Criar ativos técnicos de aplicativo do Azure | Azure Marketplace
description: Crie os ativos técnicos para uma oferta de aplicativo do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827609"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Preparar seus ativos técnicos de aplicativo do Azure

Este artigo descreve os recursos para preparar os ativos técnicos para sua oferta de aplicativo do Azure.

## <a name="before-you-begin"></a>Antes de começar

Examine o vídeo a seguir, [criando modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), uma visão geral sobre como criar um modelo de Azure Resource Manager para definir uma solução de aplicativo do Azure e como publicar subseqüentemente o oferta de aplicativo para o Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Examine a seguinte documentação do aplicativo do Azure, que fornece guias de início rápido, tutoriais e exemplos.

- [Entender modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Guias

  - [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/)
  - [Modelos de início rápido do Azure para GitHub](https://github.com/azure/azure-quickstart-templates)
  - [Publicar definição de aplicativo](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Implantar aplicativo do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Tutoriais:

  - [Criar arquivos de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicar a aplicação do mercado](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Amostras

    - [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Soluções de aplicativos gerenciados](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta.

Sua equipe de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

- Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage)e [rede do Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de trabalho do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de trabalho do [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerenciar seu aplicativo do Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

É recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) com as seguintes extensões:

  - Extensão: [ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [melhorar JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também sugerimos a revisão das ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se você estiver usando o Visual Studio, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Passos seguintes

[Criar oferta de aplicação do Azure](./cpp-create-offer.md)

