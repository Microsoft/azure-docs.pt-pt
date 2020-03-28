---
title: O que é a Sugestão Automática do Bing?
titleSuffix: Azure Cognitive Services
description: A API Bing Autosuggest devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: a90fa0a66fb32b2a885599f09458964188353880
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448838"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

Se a sua aplicação enviar consultas a qualquer uma das APIs de Pesquisa de Bing, pode utilizar a API Bing Autosuggest para melhorar a experiência de pesquisa dos seus utilizadores. A API Bing Autosuggest devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa. À medida que os caracteres são introduzidos na caixa de pesquisa, pode apresentar sugestões numa lista de drop-down.

## <a name="bing-autosuggest-api-features"></a>Funcionalidades bing autosuggest API

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/get-suggestions.md) | Melhore a sua experiência de aplicação utilizando a API autosuggest para mostrar os termos de pesquisa sugeridos à medida que são dactilografados. |

## <a name="workflow"></a>Fluxo de trabalho

O Bing Autosuggest API é um serviço web RESTful, fácil de ligar de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar jSON. 

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie um pedido para esta API sempre que um utilizador escreve um novo personagem na caixa de pesquisa da sua aplicação.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

Normalmente, chamaria a isto API cada vez que o utilizador escreve um novo personagem na caixa de pesquisa da sua aplicação. À medida que mais caracteres forem introduzidos, a API devolverá consultas de pesquisa mais relevantes. Por exemplo, as sugestões que a `s` API pode devolver para um `sail`único são suscetíveis de ser menos relevantes do que as para .

O exemplo seguinte mostra uma caixa de pesquisa com termos de consulta sugeridos da API Bing Autosuggest.

![Lista da caixa de pesquisa pendente de sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando um utilizador seleciona uma sugestão da lista de drop-down, pode usá-la para começar a pesquisar com uma das APIs de Pesquisa bing, ou ir diretamente para a página de resultados de pesquisa bing.

## <a name="next-steps"></a>Passos seguintes

Para começar a trabalhar rapidamente com o seu primeiro pedido, veja [Fazer a Primeira Consulta](quickstarts/csharp.md).

Familiarize-se com a referência da [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir termos de consulta sugeridos e as definições dos objetos de resposta.

Visite a página do centro da [Bing Search API](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.


Saiba como pesquisar a web utilizando a API de [Pesquisa web bing,](../bing-web-search/search-the-web.md)e explore as outras[APIs](../bing-web-search/index.yml)de Pesquisa de Bing .

Não se esqueça de ler o artigo [Requisitos de Utilização e Apresentação do Bing](./useanddisplayrequirements.md) para que não infrinja nenhuma das regras relativas à utilização dos resultados da pesquisa.
