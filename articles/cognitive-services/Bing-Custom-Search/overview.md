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
ms.openlocfilehash: e70d4d83fcd9641fa86f013688b5a3a6e3652919
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338406"
---
# <a name="what-is-the-bing-custom-search-api"></a>O que é a API de Pesquisa Personalizada do Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

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

Certifique-se de que lê [os requisitos de utilização e visualização de Bing](../bing-web-search/use-display-requirements.md) para utilizar os resultados de pesquisa nos seus serviços e aplicações.

Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/overview.md) para explorar as outras APIs disponíveis.

Familiarize-se com o conteúdo de referência para cada um dos pontos finais da pesquisa personalizada. A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir os resultados da pesquisa. Também inclui as definições dos objetos de resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API de Pesquisa Personalizada](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API de Imagem Personalizada](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API de Vídeo Personalizado](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API de Sugestão Automática Personalizada](/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)