---
title: O que é a API de Pesquisa Personalizada do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas para tópicos com os seus interesses.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 12c255f0e4ed11ce8acbd762530604218f8bc12b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448728"
---
# <a name="what-is-the-bing-custom-search-api"></a>O que é a API de Pesquisa Personalizada do Bing?

A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas sem anúncios para tópicos com os seus interesses. Pode especificar os domínios e páginas web para o Bing pesquisar, bem como pin, impulsionar ou despromover conteúdo específico para criar uma visão personalizada da web e ajudar os seus utilizadores a encontrar rapidamente resultados de pesquisa relevantes. 

## <a name="features"></a>Funcionalidades

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa personalizadas em tempo real](define-custom-suggestions.md)     | Forneça sugestões de pesquisa que podem ser apresentadas como uma lista de dropdown à medida que os seus utilizadores escrevem.       | 
|[Experiências personalizadas de pesquisa de imagem](get-images-from-instance.md)     | Ative os seus utilizadores a procurar em imagens dos domínios e websites especificados na sua instância de pesquisa personalizada.        |        
|[Experiências personalizadas de pesquisa de vídeo](get-videos-from-instance.md)     | Ative os seus utilizadores a procurar em vídeos a partir dos domínios e sites especificados na sua instância de pesquisa personalizada.        |    
|[Partilhar a sua instância de pesquisa personalizada](share-your-custom-search.md)     | Edite e teste colaborativamente a sua instância de pesquisa partilhando-a com membros da sua equipa.        | 
|[Configure um UI para as suas aplicações e websites](hosted-ui.md)     | Edite e teste colaborativamente a sua instância de pesquisa partilhando-a com membros da sua equipa.        | 
## <a name="workflow"></a>Fluxo de trabalho

Pode criar uma instância de pesquisa personalizada utilizando o [portal bing Custom Search](https://customsearch.ai). O portal permite-lhe criar uma instância de pesquisa personalizada que especifica os domínios, websites e páginas web que pretende que o Bing procure, juntamente com os que não pretende que ele procure. Também pode utilizar o portal para: pré-visualizar a experiência de pesquisa, ajustar os rankings de pesquisa que a API fornece e configurar opcionalmente uma interface de utilizador pesquisável para ser renderizada nos seus websites e aplicações.

Depois de criar a sua instância de pesquisa, pode integrá-la (e opcionalmente, uma interface de utilizador) no seu website ou aplicação, ligando para a API de Pesquisa Personalizada bing:

![Imagem mostrando que você pode conectar-se à pesquisa personalizada Bing através da API](media/BCS-Overview.png "Como funciona a Bing Custom Search.")


## <a name="next-steps"></a>Passos seguintes

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para obter detalhes sobre como personalizar a sua instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Certifique-se de que lê os Requisitos de [Utilização e Exibição](./use-and-display-requirements.md) de Bing para utilizar os resultados da pesquisa nos seus serviços e aplicações.

Visite a página do centro da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.

Familiarize-se com o conteúdo de referência para cada um dos pontos finais da pesquisa personalizada. A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir os resultados da pesquisa. Também inclui as definições dos objetos de resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API de Pesquisa Personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API de Imagem Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API de Vídeo Personalizado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API de Sugestão Automática Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

