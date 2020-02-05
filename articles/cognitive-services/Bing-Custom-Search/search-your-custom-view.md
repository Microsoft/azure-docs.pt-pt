---
title: Procure uma vista personalizada - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Depois de configurar a sua experiência de pesquisa personalizada, pode testá-la dentro do portal bing Custom Search.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983117"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Ligue para a sua instância de pesquisa personalizada Bing a partir do Portal

Depois de configurar a sua experiência de pesquisa personalizada, pode testá-la dentro do [portal](https://customsearch.ai)bing Custom Search . 

![uma imagem do portal de pesquisa personalizado Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Criar uma consulta de pesquisa 

Depois de ter assinado o [portal](https://customsearch.ai)bing Custom Search, selecione a sua instância de pesquisa e clique no separador **Produção.** Em **Pontos Finais,** selecione um ponto final da API (por exemplo, Web API). A sua subscrição determina quais os pontos finais mostrados.

Para criar uma consulta de pesquisa, introduza os valores do parâmetro para o seu ponto final. Note que os parâmetros apresentados no portal podem mudar dependendo do ponto final que escolher. Consulte a referência da [API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) de Pesquisa Personalizada para mais informações. Para alterar a subscrição que a sua instância de pesquisa utiliza, adicione a chave de subscrição apropriada e atualize os parâmetros de mercado e/ou idiomas apropriados.

Alguns parâmetros importantes estão abaixo:


|Parâmetro  |Descrição  |
|---------|---------|
|Consulta     | O termo de pesquisa para procurar. Disponível apenas para Web, Imagem, Vídeo e Pontos finais De Sugestão Automática |
|ID de configuração personalizado | O ID de configuração da instância de pesquisa personalizada selecionada. Este campo é lido apenas. |
|Comercializar     | O mercado de onde os resultados se originarão. Apenas disponível para os pontos finais da Web, Imagem, Vídeo e UI hospedados.        |
|Chave de assinatura | A chave de subscrição para testar com. Pode selecionar uma chave da lista de dropdown ou introduzir uma manualmente.          |

Clicar em **parâmetros adicionais** revela os seguintes parâmetros:  

|Parâmetro  |Descrição  |
|---------|---------|
|Safe Search     | Um filtro usado para filtrar páginas web para conteúdo adulto. Apenas disponível para os pontos finais da Web, Imagem, Vídeo e UI hospedados. Note que bing Custom Video Search apenas suporta dois valores: `moderate` e `strict`.        |
|Idioma da interface do utilizador    | O idioma utilizado para as cordas da interface do utilizador. Por exemplo, se ativar imagens e vídeos em UI hospedado, os separadores **De Imagem** e **Vídeo** utilizam o idioma especificado.        |
|Contagem     | O número de resultados de pesquisa para devolver na resposta. Disponível apenas para pontos finais Web, Image e Vídeo.         |
|Desvio    | O número de resultados de pesquisa a saltar antes de devolver os resultados. Disponível apenas para pontos finais Web, Image e Vídeo.        |
    
Depois de especificar todas as opções necessárias, clique em **Call** para ver a resposta JSON no painel direito. Se selecionar o ponto final do UI hospedado, pode testar a experiência de pesquisa no painel inferior.

## <a name="change-your-bing-custom-search-subscription"></a>Altere a subscrição de Bing Custom Search

Pode alterar a subscrição associada à sua instância de Pesquisa Personalizada Bing sem criar uma nova instância. Para que as chamadas API enviem e colhedas para uma nova subscrição, crie um novo recurso bing Custom Search no portal Azure. Utilize a nova chave de subscrição nos seus pedidos DePI, juntamente com o ID de configuração personalizado da sua instância.

## <a name="next-steps"></a>Passos seguintes

- [Ligue para a sua vista personalizada comC#](./call-endpoint-csharp.md)
- [Ligue para a sua vista personalizada com Java](./call-endpoint-java.md)
- [Ligue para a sua vista personalizada com NodeJs](./call-endpoint-nodejs.md)
- [Ligue para a sua vista personalizada com Python](./call-endpoint-python.md)

- [Ligue para a C# sua vista personalizada com o SDK](./sdk-csharp-quick-start.md)
