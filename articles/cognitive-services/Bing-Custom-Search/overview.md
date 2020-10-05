---
title: O que é a API de Pesquisa Personalizada do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa personalizadas para tópicos que lhe interessam.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 010f2960693a06a50cd15ac3bac2fe35b6c0985f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "84434571"
---
# <a name="what-is-the-bing-custom-search-api"></a>O que é a API de Pesquisa Personalizada do Bing?

A API de Pesquisa Personalizada Bing permite-lhe criar experiências de pesquisa sem anúncios personalizadas para tópicos que lhe interessam. Pode especificar os domínios e páginas web para o Bing procurar, bem como pin, boost ou despromusar conteúdo específico para criar uma visão personalizada da web e ajudar os seus utilizadores a encontrar rapidamente resultados de pesquisa relevantes. 

## <a name="features"></a>Funcionalidades

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões personalizadas de pesquisa em tempo real](define-custom-suggestions.md)     | Forneça sugestões de pesquisa que possam ser apresentadas como uma lista de dropdown como o seu tipo de utilizadores.       | 
|[Experiências de pesquisa de imagem personalizadas](get-images-from-instance.md)     | Permita que os seus utilizadores procurem imagens dos domínios e websites especificados no seu caso de pesquisa personalizado.        |        
|[Experiências personalizadas de pesquisa de vídeo](get-videos-from-instance.md)     | Permita que os seus utilizadores procurem vídeos a partir dos domínios e sites especificados no seu caso de pesquisa personalizado.        |    
|[Partilhar a sua instância de pesquisa personalizada](share-your-custom-search.md)     | Editar e testar colaborativamente o seu caso de pesquisa partilhando-o com membros da sua equipa.        | 
|[Configure um UI para as suas aplicações e websites](hosted-ui.md)     | Fornece um UI hospedado que pode facilmente integrar nas suas páginas web e aplicações web como um snippet de código JavaScript.        | 
## <a name="workflow"></a>Fluxo de trabalho

Pode criar um caso de pesquisa personalizado utilizando o [portal Bing Custom Search](https://customsearch.ai). O portal permite-lhe criar uma instância de pesquisa personalizada que especifica os domínios, websites e páginas web que deseja que o Bing procure, juntamente com as que não quer que procure. Também pode utilizar o portal para: visualizar a experiência de pesquisa, ajustar os rankings de pesquisa que a API fornece, e configurar opcionalmente uma interface de utilizador pes pes pes que possa ser prestada nos seus websites e aplicações.

Depois de criar o seu caso de pesquisa, pode integrá-lo (e opcionalmente, uma interface de utilizador) no seu website ou aplicação, ligando para a API de Pesquisa Personalizada Bing:

![Imagem mostrando que pode ligar-se à pesquisa personalizada de Bing através da API](media/BCS-Overview.png "Como funciona a Pesquisa Personalizada Bing.")


## <a name="next-steps"></a>Passos seguintes

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para obter detalhes sobre como personalizar a sua instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Certifique-se de que lê [os requisitos de utilização e visualização de Bing](./use-and-display-requirements.md) para utilizar os resultados de pesquisa nos seus serviços e aplicações.

Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.

Familiarize-se com o conteúdo de referência para cada um dos pontos finais da pesquisa personalizada. A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir os resultados da pesquisa. Também inclui as definições dos objetos de resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API de Pesquisa Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API de Imagem Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API de Vídeo Personalizado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API de Sugestão Automática Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

