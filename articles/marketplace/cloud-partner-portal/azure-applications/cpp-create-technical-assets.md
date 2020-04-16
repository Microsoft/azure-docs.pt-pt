---
title: Criar ativos técnicos de aplicação Azure [ Mercado Azure
description: Crie os ativos técnicos para uma oferta de candidatura Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 59f9fecb3d949d9cdf48719e6329b066d9eb3fc5
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393525"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Prepare os seus ativos técnicos de aplicação Azure

Este artigo descreve os recursos para a preparação dos ativos técnicos para a sua oferta de aplicação Azure.

## <a name="before-you-begin"></a>Antes de começar

Reveja o seguinte vídeo, Modelos de [Soluções de Construção e Aplicações Geridas para o Mercado Azure,](https://channel9.msdn.com/Events/Build/2018/BRK3603)uma visão geral sobre como autorde um modelo de Gestor de Recursos Azure para definir uma solução de aplicação Azure e, em seguida, como publicar posteriormente a oferta da aplicação para o Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Reveja a seguinte documentação da aplicação Azure, que fornece Quickstarts, Tutoriais e Amostras.

- [Compreender os modelos de gestor de recursos azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Guias de Início Rápido:

  - [Modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/)
  - [Modelos GitHub Azure Quickstart](https://github.com/azure/azure-quickstart-templates)
  - [Publicar a definição da aplicação](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Implementar a aplicação do catálogo de serviços](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Tutoriais:

  - [Criar ficheiros de definição](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicar a aplicação do mercado](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Amostras:

    - [CLI do Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Soluções de aplicação gerida](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Conceber, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias utilizadas para construir a oferta.

A sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

- Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
- Como [conceber e arquiteto sintetizar aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage)e [Networking Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de trabalho da [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de scripts para ajudar a gerir a sua aplicação Azure:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [CLI do Azure](https://docs.microsoft.com/cli/azure)

Recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

- [Explorador do Storage do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Código de Estúdio Visual](https://code.visualstudio.com/) com as seguintes extensões:

  - Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerimos também rever as ferramentas disponíveis na página [Azure Developer Tools](https://azure.microsoft.com/tools/) e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="next-steps"></a>Passos seguintes

[Criar oferta de aplicação do Azure](./cpp-create-offer.md)

