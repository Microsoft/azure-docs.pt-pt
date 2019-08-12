---
title: Perguntas frequentes (FAQ)-API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados à API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881703"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Perguntas frequentes sobre o API de Pesquisa de Imagem do Bing

Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao API de Pesquisa de Imagem do Bing para serviços cognitivas da Microsoft no Azure.

## <a name="response-headers-in-javascript"></a>Cabeçalhos de resposta em JavaScript

Os cabeçalhos a seguir podem ocorrer em respostas do API de Pesquisa de Imagem do Bing.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |A ID exclusiva que o Bing atribuiu ao usuário |
| `BingAPIs-Market`   |O mercado que foi usado para atender à solicitação |
| `BingAPIs-TraceId`  |A entrada de log no servidor de API do Bing para esta solicitação (para suporte) |

É particularmente importante manter a ID do cliente e retorná-la com solicitações subsequentes. Quando você fizer isso, a pesquisa usará o contexto anterior na classificação dos resultados da pesquisa e também fornecerá uma experiência de usuário consistente.

No entanto, quando você chama o API de Pesquisa de Imagem do Bing do JavaScript, os recursos de segurança internos (CORS) do seu navegador podem impedi-lo de acessar os valores desses cabeçalhos.

Para obter acesso aos cabeçalhos, você pode fazer a solicitação de API de Pesquisa de Imagem do Bing por meio de um proxy CORS. A resposta de um proxy deste tipo inclui um cabeçalho `Access-Control-Expose-Headers`, que adiciona os cabeçalhos das respostas à lista de permissões e os disponibiliza para o JavaScript.

É fácil instalar um proxy CORS para permitir que nosso [aplicativo tutorial](tutorial-bing-image-search-single-page-app.md) acesse os cabeçalhos de cliente opcionais. Em primeiro lugar, se ainda não o tiver, [instale Node.js](https://nodejs.org/en/download/). Em seguida, digite o comando a seguir em um prompt de comando.

    npm install -g cors-proxy-server

Em seguida, altere o ponto de extremidade API de Pesquisa de Imagem do Bing no arquivo HTML para:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Por fim, inicie o proxy do CORS com o comando seguinte:

    cors-proxy-server

Deixe a janela de comando aberta enquanto utiliza a aplicação de tutorial. Se a janela for fechada, o proxy é interrompido. Na secção Cabeçalhos HTTP expansíveis, abaixo dos resultados da pesquisa, pode agora ver o cabeçalho `X-MSEdge-ClientID` (entre outros) e confirmar se é o mesmo em todos os pedidos.

## <a name="response-headers-in-production"></a>Cabeçalhos de resposta em produção

A abordagem de proxy CORS descrita na resposta anterior é apropriada para desenvolvimento, teste e aprendizado.

Em um ambiente de produção, no entanto, você deve hospedar um script do lado do servidor no mesmo domínio que a página da Web que usa o API de Pesquisa na Web do Bing. Esse script deve realmente fazer as chamadas à API na solicitação da página da Web JavaScript e passar todos os resultados, incluindo os cabeçalhos, de volta ao cliente. Como os dois recursos (página e script) compartilham uma origem, o CORS não é produzido e os cabeçalhos especiais são acessíveis ao JavaScript na página da Web.

Essa abordagem também protege sua chave de API contra exposição ao público, pois apenas o script do lado do servidor precisa dela. O script pode usar outro método (como o referenciador HTTP) para verificar se a solicitação está autorizada.

## <a name="next-steps"></a>Passos Seguintes

Sua pergunta sobre um recurso ou uma funcionalidade ausente? Considere solicitar ou votar em nosso site de [voz do usuário](https://cognitive.uservoice.com/forums/555907-bing-search).

## <a name="see-also"></a>Consulte também

 [Stack Overflow: Serviços cognitivas](https://stackoverflow.com/questions/tagged/bing-api)
