---
title: 'Quickstart: Criar um índice de pesquisa usando APIs REST'
titleSuffix: Azure Cognitive Search
description: Neste rest API quickstart, aprenda a chamar as APIs de REPOUSO Cognitivo Azure usando o Código do Carteiro ou do Estúdio Visual.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714184"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure usando APIs de REST

Este artigo explica como formular pedidos de API rest interactivemente usando as APIs de [Rest de Pesquisa Cognitiva Azure](/rest/api/searchservice) e um cliente API para o envio e receção de pedidos. Com um cliente API e estas instruções, pode enviar pedidos e visualizar respostas antes de escrever qualquer código.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Agora que sabe executar tarefas fundamentais, pode avançar com pedidos adicionais de API rest para funcionalidades mais avançadas, como indexantes ou [criação de um pipeline de enriquecimento](cognitive-search-tutorial-blob.md) que adicione transformações de conteúdo à indexação. Para o seu próximo passo, recomendamos o seguinte link:

> [!div class="nextstepaction"]
> [Tutorial: Use REST e IA para gerar conteúdo pesmável a partir de bolhas Azure](cognitive-search-tutorial-blob.md)