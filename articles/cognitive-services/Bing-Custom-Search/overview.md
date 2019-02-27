---
title: O que é a API de Pesquisa Personalizada do Bing?
titlesuffix: Azure Cognitive Services
description: A API de pesquisa personalizada do Bing permite-lhe criar experiências de pesquisa personalizadas para tópicos que mais lhe interessa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: aahi
ms.openlocfilehash: 3a951f793ec946fc66d6a14acacd548a5f990be3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872867"
---
# <a name="what-is-the-bing-custom-search-api"></a>O que é a API de Pesquisa Personalizada do Bing?

A API de pesquisa personalizada do Bing permite-lhe criar experiências de pesquisa de publicidade sob medida para tópicos que mais lhe interessa. Pode especificar os domínios e páginas Web para o Bing, pesquisa, bem como pin, aumente ou despromover conteúdo específico para criar uma vista personalizada da web e ajudar os usuários a localizar rapidamente os resultados de pesquisa relevantes. 

## <a name="features"></a>Funcionalidades

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real personalizada](define-custom-suggestions.md)     | Fornece sugestões de pesquisa que podem ser apresentados como uma lista suspensa como tipo de utilizadores.       | 
|[Experiências de pesquisa de imagem personalizada](get-images-from-instance.md)     | Permita que os utilizadores a procurar imagens a partir de domínios e Web sites especificados na sua instância de pesquisa personalizada.        |        
|[Experiências de pesquisa de vídeo personalizada](get-videos-from-instance.md)     | Permita que os utilizadores a procurar vídeos dos domínios e sites especificado na sua instância de pesquisa personalizada.        |    
|[Partilhar a sua instância de pesquisa personalizada](share-your-custom-search.md)     | Em colaboração, editar e testar sua instância de pesquisa ao partilhá-lo com membros de sua equipe.        | 
|[Configurar uma interface do Usuário para seus aplicativos e Web sites](hosted-ui.md)     | Em colaboração, editar e testar sua instância de pesquisa ao partilhá-lo com membros de sua equipe.        | 
## <a name="workflow"></a>Fluxo de trabalho

Pode criar uma instância de pesquisa personalizadas com o [portal de pesquisa personalizada do Bing](https://customsearch.ai). O portal permite-lhe criar uma instância de pesquisa personalizada que especifica a domínios, sites e páginas da Web que pretende que o Bing para pesquisar, juntamente com as quais pretende-lo para pesquisar. Também pode utilizar o portal para: pré-visualizar a experiência de pesquisa, ajustar as classificações de pesquisa que fornece a API e, opcionalmente, configure uma interface do usuário pesquisável a ser processado nos seus Web sites e aplicações.

Depois de criar a sua instância de pesquisa, pode integrá-lo (e, opcionalmente, uma interface do usuário) no seu site ou aplicação ao chamar a API de pesquisa personalizada do Bing:

![Imagem que mostra que pode ligar a pesquisa personalizada do Bing através da API](media/BCS-Overview.png "funciona como pesquisa personalizada do Bing.")


## <a name="next-steps"></a>Passos Seguintes

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para obter detalhes sobre como personalizar a sua instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Certifique-se de que leia [requisitos de apresentação e utilização do Bing](./use-and-display-requirements.md) para utilizar os resultados da pesquisa nos seus serviços e aplicações.

Familiarize-se com o conteúdo de referência para cada um dos pontos finais da pesquisa personalizada. A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir os resultados da pesquisa. Também inclui as definições dos objetos de resposta.

- [API de Pesquisa Personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [API de Imagem Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [API de Vídeo Personalizado](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [API de Sugestão Automática Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)

