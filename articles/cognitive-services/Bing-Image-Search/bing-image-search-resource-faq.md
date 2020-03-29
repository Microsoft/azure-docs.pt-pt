---
title: Perguntas frequentes (FAQ) - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Encontre respostas a perguntas comumente feitas sobre conceitos, códigoe cenários relacionados com a API de Pesquisa de Imagem bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881703"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Perguntas frequentes (FAQ) sobre a API de Pesquisa de Imagem bing

Encontre respostas a perguntas comumente feitas sobre conceitos, códigos e cenários relacionados com a API de Pesquisa de Imagem Bing para serviços cognitivos da Microsoft em Azure.

## <a name="response-headers-in-javascript"></a>Cabeçalhos de resposta no JavaScript

Os seguintes cabeçalhos podem ocorrer em respostas da API de Pesquisa de Imagem Bing.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |O ID único que Bing atribuiu ao utilizador |
| `BingAPIs-Market`   |O mercado que foi usado para cumprir o pedido |
| `BingAPIs-TraceId`  |A entrada de log no servidor Bing API para este pedido (para suporte) |

É particularmente importante persistir na identificação do cliente e devolvê-la com pedidos subsequentes. Quando o fizer, a pesquisa utilizará o contexto passado nos resultados de pesquisa de ranking e também proporcionará uma experiência consistente do utilizador.

No entanto, quando liga para a API de Pesquisa de Imagem Bing do JavaScript, as funcionalidades de segurança incorporadas do seu navegador (CORS) podem impedir que aceda aos valores destes cabeçalhos.

Para ter acesso aos cabeçalhos, pode fazer o pedido de API de Pesquisa de Imagem Bing através de um proxy CORS. A resposta de um proxy deste tipo inclui um cabeçalho `Access-Control-Expose-Headers`, que adiciona os cabeçalhos das respostas à lista de permissões e os disponibiliza para o JavaScript.

É fácil instalar um proxy CORS para permitir que a nossa [aplicação tutorial](tutorial-bing-image-search-single-page-app.md) aceda aos cabeçalhos opcionais do cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, introduza o seguinte comando num pedido de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto final da API de Pesquisa de Imagem Bing no ficheiro HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta na produção

A abordagem de procuração cors descrita na resposta anterior é adequada para o desenvolvimento, teste e aprendizagem.

No entanto, num ambiente de produção, deve alojar um script do lado do servidor no mesmo domínio que a página Web que utiliza a API de Pesquisa Web bing. Este script deve realmente fazer as chamadas da API a pedido da página Web JavaScript e passar todos os resultados, incluindo cabeçalhos, de volta ao cliente. Uma vez que os dois recursos (página e script) partilham uma origem, o CORS não entra em jogo e os cabeçalhos especiais estão acessíveis ao JavaScript na página Web.

Esta abordagem também protege a sua chave API da exposição ao público, uma vez que apenas o script do lado do servidor precisa dela. O script pode usar outro método (como o remetente HTTP) para se certificar de que o pedido é autorizado.

## <a name="next-steps"></a>Passos seguintes

A sua pergunta sobre uma funcionalidade ou funcionalidade em falta? Considere solicitar ou votar no nosso [site user Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Consulte também

 [Transbordo de pilhas: Serviços Cognitivos](https://stackoverflow.com/questions/tagged/bing-api)
