---
title: O que é a API de Pesquisa Personalizada do Bing?
titleSuffix: Azure Cognitive Services
description: O API de Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa personalizadas para tópicos sobre os quais você se preocupa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 4b0b0d91af15912e1c64761351ba33acfd3e2725
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405149"
---
# <a name="what-is-the-bing-custom-search-api"></a>O que é a API de Pesquisa Personalizada do Bing?

O API de Pesquisa Personalizada do Bing permite que você crie experiências de pesquisa sem anúncios personalizadas para tópicos sobre os quais você se preocupa. Você pode especificar os domínios e as páginas da Web para o Bing para pesquisa, bem como fixar, aumentar ou rebaixar conteúdo específico para criar uma exibição personalizada da Web e ajudar os usuários a localizar rapidamente os resultados relevantes da pesquisa. 

## <a name="features"></a>Funcionalidades

|Funcionalidade  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real personalizadas](define-custom-suggestions.md)     | Forneça sugestões de pesquisa que podem ser exibidas como uma lista suspensa como seu tipo de usuário.       | 
|[Experiências de pesquisa de imagem personalizada](get-images-from-instance.md)     | Permita que os usuários pesquisem imagens de domínios e sites especificados em sua instância de pesquisa personalizada.        |        
|[Experiências de pesquisa de vídeo personalizadas](get-videos-from-instance.md)     | Permita que os usuários pesquisem vídeos de domínios e sites especificados em sua instância de pesquisa personalizada.        |    
|[Compartilhar sua instância de pesquisa personalizada](share-your-custom-search.md)     | Edite e teste colaborativamente sua instância de pesquisa compartilhando-a com membros de sua equipe.        | 
|[Configurar uma interface do usuário para seus aplicativos e sites](hosted-ui.md)     | Edite e teste colaborativamente sua instância de pesquisa compartilhando-a com membros de sua equipe.        | 
## <a name="workflow"></a>Fluxo de trabalho

Você pode criar uma instância de pesquisa personalizada usando o [portal de pesquisa personalizada do Bing](https://customsearch.ai). O portal permite que você crie uma instância de pesquisa personalizada que especifica os domínios, sites e páginas da Web que você deseja que o Bing pesquise, junto com aqueles que você não deseja que ele pesquise. Você também pode usar o portal para: Visualizar a experiência de pesquisa, ajustar as classificações de pesquisa que a API fornece e, opcionalmente, configurar uma interface de usuário pesquisável para ser renderizada em seus sites e aplicativos.

Depois de criar sua instância de pesquisa, você pode integrá-la (e, opcionalmente, uma interface do usuário) em seu site ou aplicativo chamando o API de Pesquisa Personalizada do Bing:

![Imagem mostrando que você pode se conectar à pesquisa personalizada do Bing por meio da API](media/BCS-Overview.png "Como pesquisa personalizada do Bing funciona.")


## <a name="next-steps"></a>Passos Seguintes

Para começar rapidamente, consulte [Criar a primeira instância de Pesquisa Personalizada do Bing](quick-start.md).

Para obter detalhes sobre como personalizar a sua instância de pesquisa, consulte [Definir uma instância de pesquisa personalizada](define-your-custom-view.md).

Certifique-se de ler [os requisitos de uso e exibição do Bing](./use-and-display-requirements.md) para usar os resultados da pesquisa em seus serviços e aplicativos.

Familiarize-se com o conteúdo de referência para cada um dos pontos finais da pesquisa personalizada. A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir os resultados da pesquisa. Também inclui as definições dos objetos de resposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API de Pesquisa Personalizada do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API de Imagem Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API de Vídeo Personalizado](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API de Sugestão Automática Personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

