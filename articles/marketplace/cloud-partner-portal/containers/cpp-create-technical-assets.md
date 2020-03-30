---
title: Criar ativos técnicos de imagem de contentores Azure [ Mercado Azure
description: Crie os ativos técnicos para um contentor Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280070"
---
# <a name="prepare-your-container-technical-assets"></a>Preparar os recursos técnicos dos contentores

Este artigo descreve etapas e requisitos para configurar um contentor oferecer o Mercado Azure.

## <a name="before-you-begin"></a>Antes de começar

Reveja a documentação de instâncias de [contentores Azure,](https://docs.microsoft.com/azure/container-instances) que fornece Quickstarts, Tutoriais e Amostras.

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Conceber, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias utilizadas para construir a oferta.
 
Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

-   Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/) 
-   Como [conceber e arquiteto sintetizar aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
-   Conhecimento de trabalho de [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage) e [Networking Azure](https://azure.microsoft.com/services/?filter=networking)
-   Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
-   Conhecimento de Trabalho da [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerir a sua imagem de contentor:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Além disso, recomendamos adicionar as seguintes ferramentas ao seu ambiente de desenvolvimento:

-   [Explorador do Armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Código de estúdio visual](https://code.visualstudio.com/)
    *   Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Sugerimos também rever as ferramentas disponíveis na página [Azure Developer Tools](https://azure.microsoft.com/tools/) e, se estiver a utilizar o Visual Studio, o [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>Criar a imagem do recipiente

Consulte o seguinte para mais informações:

* [Tutorial: Criar uma imagem de contentor para implantação em Instâncias de Contentores Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Tutorial: Construir e implantar imagens de contentores na nuvem com tarefas de registo de contentores azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Passos seguintes

[Crie a sua oferta de contentores](./cpp-create-offer.md)
