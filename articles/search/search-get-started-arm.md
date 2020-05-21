---
title: Use um modelo de Gestor de Recursos Azure para implementar o seu serviço
titleSuffix: Azure Cognitive Search
description: Pode implementar rapidamente uma instância de serviço de pesquisa cognitiva Azure utilizando o modelo de gestor de recursos Azure.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 670cebe1c0f1c9002e33b729d0db9ee9f9a01283
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682524"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Quickstart: Implementar pesquisa cognitiva usando um modelo de Gestor de Recursos

Este artigo acompanha-o através do processo de utilização de um modelo de Gestor de Recursos para implantar um recurso de Pesquisa Cognitiva Azure no portal Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-cognitive-search-service"></a>Criar um serviço de Pesquisa Cognitiva

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50,70-85":::

O recurso Azure definido neste modelo:

- [Microsoft.Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): crie um serviço de pesquisa cognitiva Azure

### <a name="deploy-the-template"></a>Implementar o modelo

Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um recurso de pesquisa cognitiva Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

O portal exibe um formulário que lhe permite fornecer facilmente valores de parâmetros. Alguns parâmetros são pré-preenchidos com os valores padrão do modelo. Terá de fornecer a sua subscrição, grupo de recursos, localização e nome de serviço. Se quiser utilizar os Serviços Cognitivos num oleoduto de enriquecimento de [IA,](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) por exemplo, para analisar ficheiros de imagem binários para texto, escolha um local que ofereça tanto a Pesquisa Cognitiva como os Serviços Cognitivos. Ambos os serviços são obrigados a estar na mesma região para cargas de trabalho de enriquecimento de IA. Uma vez preenchido o formulário, terá de concordar com os termos e condições e, em seguida, selecionar o botão de compra para completar a sua implementação.

> [!div class="mx-imgBorder"]
> ![Exibição do portal Azure do modelo](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Rever os recursos implantados

Quando a sua implementação estiver concluída, pode aceder ao seu novo grupo de recursos e ao seu novo serviço de pesquisa no portal.

## <a name="clean-up-resources"></a>Limpar recursos

Outros quickstarts e tutoriais de Pesquisa Cognitiva baseiam-se neste arranque rápido. Se pretende continuar a trabalhar com quickstarts e tutoriais subsequentes, poderá desejar deixar este recurso no lugar. Quando já não for necessário, pode eliminar o grupo de recursos, que elimina o serviço de Pesquisa Cognitiva e recursos conexos.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou um serviço de Pesquisa Cognitiva utilizando um modelo de Gestor de Recursos Azure e validou a implementação. Para saber mais sobre a Pesquisa Cognitiva e o Gestor de Recursos Azure, continue para os artigos abaixo.

 - Leia uma [visão geral da Pesquisa Cognitiva de Azure](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Crie um índice](https://docs.microsoft.com/azure/search/search-get-started-portal) para o seu serviço de pesquisa
 - [Criar uma aplicação de pesquisa](https://docs.microsoft.com/azure/search/search-create-app-portal) usando o assistente do portal
 - [Crie uma habilidade](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) para extrair informações dos seus dados


