---
title: Criar imagem de contentores do Azure ativos técnicos | O Azure Marketplace
description: Crie os recursos técnicos para um contentor do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794331"
---
# <a name="prepare-your-container-technical-assets"></a>Preparar o seu contentor ativos técnicos

Este artigo descreve os passos e os requisitos para configurar uma oferta de contentor no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Reveja os [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) documentação, que fornece inícios rápidos, tutoriais e amostras.

## <a name="fundamental-technical-knowledge"></a>Conhecimentos técnicos fundamentais

Estruturar, criar e testar esses ativos demorar algum tempo e requer conhecimento técnico de plataforma do Azure e as tecnologias usadas para criar a oferta.
 
Além de seu domínio de solução, a sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

-   Noções básicas sobre [dos serviços do Azure](https://azure.microsoft.com/services/) 
-   Como [de design e a arquitetura de aplicações do Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento prático do [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage) e [redes do Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento prático do [do Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento prático do [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Sugestões de ferramentas

Escolha um ou ambos dos seguintes ambientes de criação de scripts para ajudar a gerir a sua imagem de contentor:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

-   [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extensão: [Ferramentas do Gestor de recursos do Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerimos também a rever as ferramentas disponíveis no [ferramentas de programação do Azure](https://azure.microsoft.com/tools/) página e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Criar a imagem de contentor

Veja o seguinte para obter mais informações:

* [Tutorial: Criar uma imagem de contentor para implementação no Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Tutorial: Criar e implementar imagens de contentor na cloud com tarefas de registo de contentor do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Passos Seguintes

[Criar a sua oferta de contentor](./cpp-create-offer.md)
