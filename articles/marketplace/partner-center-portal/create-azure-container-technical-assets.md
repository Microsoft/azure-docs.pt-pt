---
title: Criar um ativo técnico Azure VM
description: Este artigo descreve os passos e requisitos para configurar uma oferta de contentores no Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 262129084be5c6a68bfd8d8708c9a10fbb606144
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383733"
---
# <a name="create-an-azure-vm-technical-asset"></a>Criar um ativo técnico Azure VM

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas de contentores Azure do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, siga as instruções em Prepare os [seus ativos técnicos](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) de contentores para o Portal do Parceiro cloud para gerir as suas ofertas.

Este artigo descreve os passos e requisitos para configurar uma oferta de contentores no Azure Marketplace.

## <a name="before-you-begin"></a>Antes de começar

Para Quickstarts, Tutoriais e Amostras, consulte as instâncias do [contentor azure.](https://docs.microsoft.com/azure/container-instances)

## <a name="fundamental-technical-knowledge"></a>Conhecimento técnico fundamental

Projetar, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias usadas para construir a oferta.

Além do seu domínio de solução, a sua equipa de engenharia deve ter conhecimento sobre as seguintes tecnologias da Microsoft:

- Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
- Como [conceber e arquiteto sintetizar aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage)e [Networking Azure](https://azure.microsoft.com/services/?filter=networking)
- Conhecimento de trabalho do Gestor de [Recursos Azure](https://azure.microsoft.com/features/resource-manager/)
- Conhecimento de trabalho da [JSON.](https://www.json.org/)

## <a name="suggested-tools"></a>Ferramentas sugeridas

Escolha um ou ambos os seguintes ambientes de script para ajudar a gerir a sua imagem de Contentor:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

Recomendamos adicionar estas ferramentas ao seu ambiente de desenvolvimento:

- [Explorador do Storage do Azure](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensão: Ferramentas de [Gestor de Recursos Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensão: [Embelezar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensão: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Reveja as ferramentas disponíveis na página Ferramentas de [Desenvolvimento Do Azure.](https://azure.microsoft.com/) Se estiver a utilizar o Visual Studio, reveja as ferramentas disponíveis no [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>Criar a imagem do recipiente

Para mais informações, consulte os seguintes tutoriais:

- [Tutorial: Criar uma imagem de contentor para implantação em Instâncias de Contentores Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Tutorial: Construa e implante imagens de contentores na nuvem com tarefas](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)de registo de contentores Azure .

## <a name="next-step"></a>Passo seguinte

- [Crie a sua oferta de contentores.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)
