---
title: Prepare os seus ativos técnicos do contentor Azure
description: Recursos técnicos e diretrizes para ajudá-lo a configurar uma oferta de contentores no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459331"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Prepare os seus ativos técnicos do contentor Azure

Este artigo fornece recursos técnicos e recomendações para ajudá-lo a criar uma oferta de contentores no Azure Marketplace.

## <a name="before-you-begin"></a>Before you begin

Para quickstarts, tutoriais e amostras, consulte a [documentação das instâncias do recipiente Azure](../container-instances/index.yml).

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

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/).

Recomendamos que adicione estas ferramentas ao seu ambiente de desenvolvimento:

- [Explorador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: [Ferramentas do gestor de recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Embelezamento](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Reveja as ferramentas disponíveis na página [Azure Developer Tools.](https://azure.microsoft.com/) Se estiver a utilizar o Visual Studio, reveja as ferramentas disponíveis no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Criar a imagem do recipiente

Não é possível implantar uma imagem no Azure Container Instances a partir de um registo no local.

- Se já tiver um recipiente de trabalho no seu registo local, crie um Registo Azure e carre fique com a imagem do seu contentor no Registo do Contentor de Azure. Para saber mais, consulte [Tutorial: Construa e implemente imagens de contentores na nuvem com tarefas de registo de contentores Azure](../container-registry/container-registry-tutorial-quick-task.md).

- Se ainda não tiver uma imagem de contentor e precisar de contentorizar a sua aplicação existente ou criar uma nova aplicação baseada em contentores, clone o código fonte de aplicação do GitHub, crie uma imagem de recipiente a partir da fonte de aplicação e teste a imagem num ambiente local do Docker. Para saber mais, consulte [Tutorial: Crie uma imagem de recipiente para implantação em Instâncias de Contentores Azure](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Passos seguintes

- [Crie a sua oferta de contentores](create-azure-container-offer.md)