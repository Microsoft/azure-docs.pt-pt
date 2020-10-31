---
title: O que é a Sugestão Automática do Bing?
titleSuffix: Azure Cognitive Services
description: A Bing Autosuggest API devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d55ff4ed8717a449628e94a940c0896d31818da2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101940"
---
# <a name="what-is-bing-autosuggest"></a>O que é a Sugestão Automática do Bing?

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Se a sua aplicação enviar consultas para qualquer uma das APIs de Pesquisa de Bing, pode utilizar a API Bing Autosuggest para melhorar a experiência de pesquisa dos seus utilizadores. A Bing Autosuggest API devolve uma lista de consultas sugeridas com base na cadeia de consulta parcial na caixa de pesquisa. À medida que os caracteres são introduzidos na caixa de pesquisa, pode apresentar sugestões numa lista de suspensos.

## <a name="bing-autosuggest-api-features"></a>Bing Autosuggest API

| Funcionalidade                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir termos de pesquisa em tempo real](concepts/get-suggestions.md) | Melhore a sua experiência de aplicação utilizando a API Autosuggest para exibir termos de pesquisa sugeridos à medida que são dactilografados. |

## <a name="workflow"></a>Fluxo de trabalho

A Bing Autosuggest API é um serviço web RESTful, fácil de ligar a partir de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar jSON.

1. Crie uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Se não tiver uma subscrição do Azure, pode [criar uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuita.
2. Envie um pedido a esta API sempre que um utilizador escreve um novo carácter na caixa de pesquisa da sua aplicação.
3. Processe a resposta da API ao analisar a mensagem JSON devolvida.

Normalmente, chamaria a isto API cada vez que o utilizador escreve um novo personagem na caixa de pesquisa da sua aplicação. À medida que mais caracteres são introduzidos, a API devolverá consultas de pesquisa mais relevantes sugeridas. Por exemplo, as sugestões que a API pode devolver para um único `s` são provavelmente menos relevantes do que as de `sail` .

O exemplo a seguir mostra uma caixa de pesquisa suspensa com termos de consulta sugeridos da API Bing Autosuggest.

![Lista da caixa de pesquisa pendente de sugestão automática](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Quando um utilizador seleciona uma sugestão da lista de drop-down, pode usá-la para começar a pesquisar com uma das APIs de Pesquisa de Bing ou ir diretamente para a página de resultados de pesquisa de Bing.

## <a name="next-steps"></a>Passos seguintes

Para começar a trabalhar rapidamente com o seu primeiro pedido, veja [Fazer a Primeira Consulta](quickstarts/csharp.md).

Familiarize-se com a referência da [API de Sugestão Automática do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference). A referência contém a lista de pontos finais, cabeçalhos e parâmetros de consulta que teria de utilizar para pedir termos de consulta sugeridos e as definições dos objetos de resposta.

Visite a página do hub da [API de Pesquisa de Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.


Aprenda a pesquisar na web utilizando a [API de Pesquisa web Bing](../bing-web-search/search-the-web.md)e explore as outras[APIs de pesquisa de Bing](../bing-web-search/index.yml).

Não se esqueça de ler o artigo [Requisitos de Utilização e Apresentação do Bing](./useanddisplayrequirements.md) para que não infrinja nenhuma das regras relativas à utilização dos resultados da pesquisa.
