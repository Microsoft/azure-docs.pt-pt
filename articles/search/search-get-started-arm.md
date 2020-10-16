---
title: Utilize um modelo de Gestor de Recursos Azure para implementar o seu serviço
titleSuffix: Azure Cognitive Search
description: Pode implementar rapidamente uma instância de serviço de Pesquisa Cognitiva Azure utilizando o modelo de gestor de recursos Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/15/2020
ms.openlocfilehash: abaf9abe9c52afba095fa52eee32a598ab6b3fcd
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058459"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>Quickstart: Implementar a Pesquisa Cognitiva utilizando um modelo ARM

Este artigo acompanha-o através do processo de utilização de um modelo de Gestor de Recursos Azure (modelo ARM) para implantar um recurso de Pesquisa Cognitiva Azure no portal Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

O recurso Azure definido neste modelo:

- [Microsoft.Search/searchServices](/azure/templates/Microsoft.Search/searchServices): criar um serviço de Pesquisa Cognitiva Azure

## <a name="deploy-the-template"></a>Implementar o modelo

Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um recurso de Pesquisa Cognitiva Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

O portal apresenta um formulário que lhe permite fornecer facilmente valores de parâmetros. Alguns parâmetros são pré-preenchidos com os valores predefinidos do modelo. Terá de fornecer a sua subscrição, grupo de recursos, localização e nome de serviço. Se quiser utilizar os Serviços Cognitivos num oleoduto [de enriquecimento de IA,](cognitive-search-concept-intro.md) por exemplo, para analisar ficheiros de imagem binários para texto, escolha um local que ofereça tanto a Pesquisa Cognitiva como os Serviços Cognitivos. Ambos os serviços são obrigados a estar na mesma região para cargas de trabalho de enriquecimento de IA. Uma vez preenchido o formulário, terá de concordar com os termos e condições e, em seguida, selecionar o botão de compra para completar a sua implementação.

> [!div class="mx-imgBorder"]
> ![Exibição do portal Azure do modelo](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Quando a sua implementação estiver concluída, pode aceder ao seu novo grupo de recursos e ao novo serviço de pesquisa no portal.

## <a name="clean-up-resources"></a>Limpar recursos

Outros quickstarts e tutoriais de Pesquisa Cognitiva baseiam-se neste arranque rápido. Se pretender continuar a trabalhar com os rápidos e tutoriais subsequentes, pode desejar deixar este recurso no lugar. Quando já não é necessário, pode eliminar o grupo de recursos, que elimina o serviço de Pesquisa Cognitiva e recursos relacionados.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um serviço de Pesquisa Cognitiva usando um modelo ARM e validou a implementação. Para saber mais sobre a Pesquisa Cognitiva e O Gestor de Recursos Azure, continue para os artigos abaixo.

- Leia uma [visão geral da Pesquisa Cognitiva Azure.](search-what-is-azure-search.md)
- [Crie um índice](search-get-started-portal.md) para o seu serviço de pesquisa.
- [Crie uma aplicação de demonstração](search-create-app-portal.md) utilizando o assistente do portal.
- [Crie um skillset](cognitive-search-quickstart-blob.md) para extrair informação dos seus dados.
