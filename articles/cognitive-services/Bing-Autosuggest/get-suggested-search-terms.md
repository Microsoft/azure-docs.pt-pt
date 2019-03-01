---
title: O que é a Sugestão Automática do Bing?
titlesuffix: Azure Cognitive Services
description: Saiba como utilizar a API de Sugestão Automática do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 669eaaf0eaf28c696a7ca368c02f5816054e5d10
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010111"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

Se a sua aplicação enviar consultas para qualquer uma das APIs de pesquisa do Bing, pode usar a API de sugestão automática do Bing para melhorar a experiência de pesquisa dos seus utilizadores. A API de sugestão automática do Bing devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa. Que carateres são inseridos na caixa de pesquisa, pode exibir sugestões numa lista pendente.

## <a name="bing-autosuggest-api-features"></a>Funcionalidades de API de sugestão automática do Bing

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/get-suggestions.md) | Melhore a sua experiência de aplicação com a API de sugestão automática para apresentar os termos de pesquisa sugerida, à medida que estão a ser digitados. |

## <a name="workflow"></a>Fluxo de trabalho

A API de sugestão automática do Bing é um serviço RESTful web, fáceis de chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. 

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuita.
2. Envie um pedido para esta API sempre que um usuário digita um caractere de nova na caixa de pesquisa da sua aplicação.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

Normalmente, chamaria esta API sempre que o utilizador escreve um caractere de nova na caixa de pesquisa da sua aplicação. Que forem inseridas mais carateres, a API irá devolver a consultas de pesquisa sugerida mais relevantes. Por exemplo, as sugestões a API podem devolver para uma única `s` são provavelmente será menos relevantes do que aqueles para `sail`.

O exemplo seguinte mostra uma caixa de pesquisa de lista suspensa com os termos de sugestões de consulta da API de sugestão automática do Bing.

![Lista da caixa de pesquisa pendente de sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando um usuário seleciona uma sugestão na lista pendente, pode utilizá-lo para a pesquisa com uma das APIs de pesquisa do Bing ou vá diretamente para a página de resultados de pesquisa do Bing.

## <a name="next-steps"></a>Passos Seguintes

Para começar a trabalhar rapidamente com o seu primeiro pedido, veja [Fazer a Primeira Consulta](quickstarts/csharp.md).

Familiarize-se com a referência da [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir termos de consulta sugeridos e as definições dos objetos de resposta.

Saiba como procurar na Web com a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md).

Não se esqueça de ler o artigo [Requisitos de Utilização e Apresentação do Bing](./useanddisplayrequirements.md) para que não infrinja nenhuma das regras relativas à utilização dos resultados da pesquisa.
