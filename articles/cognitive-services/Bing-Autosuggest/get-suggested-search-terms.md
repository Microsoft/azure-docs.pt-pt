---
title: O que é a Sugestão Automática do Bing?
titleSuffix: Azure Cognitive Services
description: O API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de caracteres de consulta parcial na caixa de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: a90fa0a66fb32b2a885599f09458964188353880
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448838"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

Se seu aplicativo enviar consultas para qualquer um dos APIs de Pesquisa do Bing, você poderá usar o API de Sugestão Automática do Bing para melhorar a experiência de pesquisa de seus usuários. O API de Sugestão Automática do Bing retorna uma lista de consultas sugeridas com base na cadeia de caracteres de consulta parcial na caixa de pesquisa. À medida que os caracteres são inseridos na caixa de pesquisa, você pode exibir sugestões em uma lista suspensa.

## <a name="bing-autosuggest-api-features"></a>Recursos de API de Sugestão Automática do Bing

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/get-suggestions.md) | Aprimore sua experiência de aplicativo usando a API de sugestão automática para exibir os termos de pesquisa sugeridos conforme eles são digitados. |

## <a name="workflow"></a>Fluxo de Trabalho

O API de Sugestão Automática do Bing é um serviço Web RESTful, fácil de chamar de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON. 

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie uma solicitação para essa API cada vez que um usuário digitar um novo caractere na caixa de pesquisa do seu aplicativo.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

Normalmente, você chamaria essa API toda vez que o usuário digitar um novo caractere na caixa de pesquisa do seu aplicativo. À medida que mais caracteres forem inseridos, a API retornará consultas de pesquisa sugeridas mais relevantes. Por exemplo, as sugestões que a API pode retornar para um único `s` provavelmente serão menos relevantes do que aquelas para `sail`.

O exemplo a seguir mostra uma caixa de pesquisa suspensa com os termos de consulta sugeridos do API de Sugestão Automática do Bing.

![Lista da caixa de pesquisa pendente de sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando um usuário seleciona uma sugestão na lista suspensa, você pode usá-la para começar a Pesquisar com uma das APIs de Pesquisa do Bing ou ir diretamente para a página resultados da pesquisa do Bing.

## <a name="next-steps"></a>Passos seguintes

Para começar a trabalhar rapidamente com o seu primeiro pedido, veja [Fazer a Primeira Consulta](quickstarts/csharp.md).

Familiarize-se com a referência da [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir termos de consulta sugeridos e as definições dos objetos de resposta.

Visite a [página do hub de API pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.


Saiba como Pesquisar na Web usando o [API de pesquisa na Web do Bing](../bing-web-search/search-the-web.md)e explorar os outros[APIs de pesquisa do Bing](../bing-web-search/index.yml).

Não se esqueça de ler o artigo [Requisitos de Utilização e Apresentação do Bing](./useanddisplayrequirements.md) para que não infrinja nenhuma das regras relativas à utilização dos resultados da pesquisa.
