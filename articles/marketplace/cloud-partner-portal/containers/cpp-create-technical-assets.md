---
title: Criar ativos técnicos de imagem de contêineres do Azure | Azure Marketplace
description: Crie os ativos técnicos para um contêiner do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819056"
---
# <a name="prepare-your-container-technical-assets"></a>Preparar seus ativos técnicos do contêiner

Este artigo descreve as etapas e os requisitos para configurar um contêiner que oferece o Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Examine a documentação de [instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances) , que fornece guias de início rápido, tutoriais e exemplos.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, criar e testar esses ativos leva tempo e exige conhecimento técnico da plataforma Azure e das tecnologias usadas para criar a oferta.
 
Além do seu domínio de solução, sua equipe de engenharia deve ter conhecimento das seguintes tecnologias da Microsoft:

-   Noções básicas sobre os [Serviços do Azure](https://azure.microsoft.com/services/) 
-   Como [projetar e arquitetar aplicativos do Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento de trabalho de [máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/), [armazenamento do Azure](https://azure.microsoft.com/services/?filter=storage) e [rede do Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento de trabalho do [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento de trabalho do [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerenciar sua imagem de contêiner:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [CLI do Azure](https://docs.microsoft.com/cli/azure)

Além disso, é recomendável adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

-   [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extensão: [ferramentas de Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão: [melhorar JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Também sugerimos a revisão das ferramentas disponíveis na página [ferramentas para desenvolvedores do Azure](https://azure.microsoft.com/tools/) e, se você estiver usando o Visual Studio, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Criar a imagem de contêiner

Consulte o seguinte para obter mais informações:

* [Tutorial: criar uma imagem de contêiner para implantação em instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Tutorial: criar e implantar imagens de contêiner na nuvem com tarefas de registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Passos seguintes

[Criar sua oferta de contêiner](./cpp-create-offer.md)
