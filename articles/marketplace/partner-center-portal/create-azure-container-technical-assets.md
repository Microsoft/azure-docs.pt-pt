---
title: Conceitos técnicos para ofertas de contentores Azure - Mercado comercial da Microsoft
description: Recursos técnicos e diretrizes para ajudá-lo a configurar uma oferta de contentores no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/09/2020
ms.openlocfilehash: 46102305e99c94fc71ad0934a2c0063fdcbeec4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317795"
---
# <a name="create-an-azure-container-offer"></a>Criar uma oferta de recipienteS Azure

Este artigo fornece recursos técnicos e recomendações para ajudá-lo a criar uma oferta de contentores no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Para quickstarts, tutoriais e amostras, consulte a [documentação das instâncias do recipiente Azure](../../container-instances/index.yml).

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar estes ativos leva tempo e requer conhecimento técnico tanto da plataforma Azure como das tecnologias usadas para construir a oferta.

Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimentos sobre as seguintes tecnologias da Microsoft:

- Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
- Como [projetar e arquiteto aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimentos de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)e [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de Trabalho da [JSON.](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerir a sua imagem de Recipiente:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Recomendamos que adicione estas ferramentas ao seu ambiente de desenvolvimento:

- [Explorador de Armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: [Ferramentas do gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Embelezamento](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Reveja as ferramentas disponíveis na página [Azure Developer Tools.](https://azure.microsoft.com/) Se estiver a utilizar o Visual Studio, reveja as ferramentas disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Criar a imagem do recipiente

Para mais informações, consulte os seguintes tutoriais:

- [Tutorial: Criar uma imagem de recipiente para implantação em Instâncias de Contentores Azure](../../container-instances/container-instances-tutorial-prepare-app.md)
- [Tutorial: Construa e implemente imagens de contentores na nuvem com tarefas de registo de contentores Azure](../../container-registry/container-registry-tutorial-quick-task.md).

## <a name="next-steps"></a>Passos seguintes

- [Crie a sua oferta de contentores.](create-azure-container-offer.md)
