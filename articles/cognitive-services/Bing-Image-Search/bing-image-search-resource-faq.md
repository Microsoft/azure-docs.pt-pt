---
title: Perguntas frequentes (FAQ) - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Encontre respostas para perguntas comumente feitas sobre conceitos, código e cenários relacionados com a API de Pesquisa de Imagem Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e8502b1d01e0f3fbf5d42e924511cc978690bce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342197"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Perguntas frequentes (FAQ) sobre a API de Pesquisa de Imagem de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Encontre respostas para perguntas comumente feitas sobre conceitos, código e cenários relacionados com a API de pesquisa de imagem Bing para serviços cognitivos da Microsoft em Azure.

## <a name="response-headers-in-javascript"></a>Cabeçalhos de resposta em JavaScript

Podem ocorrer os seguintes cabeçalhos em respostas da API de Pesquisa de Imagem de Bing.

| Atributo           | Descrição   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |O ID único que Bing atribuiu ao utilizador |
| `BingAPIs-Market`   |O mercado que foi usado para satisfazer o pedido |
| `BingAPIs-TraceId`  |A entrada de registo no servidor Bing API para este pedido (para suporte) |

É particularmente importante persistir na identificação do cliente e devolvê-la com pedidos subsequentes. Ao fazê-lo, a pesquisa utilizará o contexto passado nos resultados de pesquisa do ranking e também proporcionará uma experiência consistente do utilizador.

No entanto, quando liga para a API de Pesquisa de Imagem Bing do JavaScript, as funcionalidades de segurança incorporadas do seu navegador (CORS) podem impedir-lhe de aceder aos valores destes cabeçalhos.

Para obter acesso aos cabeçalhos, pode escorrê-lo através de um representante da CORS. A resposta de tal proxy tem um `Access-Control-Expose-Headers` cabeçalho que filtra cabeçalhos de resposta e os coloca disponíveis para JavaScript.

É fácil instalar um proxy CORS para permitir que a nossa [aplicação tutorial](tutorial-bing-image-search-single-page-app.md) aceda aos cabeçalhos opcionais do cliente. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, insira o seguinte comando num pedido de comando.

```console
npm install -g cors-proxy-server
```

Em seguida, altere o ponto de terminação de pesquisa de imagem Bing no ficheiro HTML para:\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Por fim, inicie o proxy do CORS com o comando seguinte:

```console
cors-proxy-server
```

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta na produção

A abordagem de procuração CORS descrita na resposta anterior é adequada para o desenvolvimento, teste e aprendizagem.

Num ambiente de produção, no entanto, deve hospedar um script do lado do servidor no mesmo domínio que a página Web que utiliza a API de Pesquisa web Bing. Este script deve realmente fazer as chamadas API a pedido da página Web JavaScript e passar todos os resultados, incluindo cabeçalhos, de volta para o cliente. Uma vez que os dois recursos (página e script) partilham uma origem, o CORS não entra em jogo e os cabeçalhos especiais estão acessíveis ao JavaScript na página Web.

Esta abordagem também protege a sua chave API da exposição ao público, uma vez que apenas o script do lado do servidor precisa dela. O script pode usar outro método (como o remetente HTTP) para se certificar de que o pedido é autorizado.

## <a name="next-steps"></a>Passos seguintes

A sua pergunta é sobre uma funcionalidade ou funcionalidade em falta? Considere solicitar ou votar no nosso [site user Voice](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Ver também

 [Stack Overflow: Serviços Cognitivos](https://stackoverflow.com/questions/tagged/bing-api)