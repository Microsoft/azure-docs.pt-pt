---
title: Pesquisar uma vista personalizada - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Depois de configurar a sua experiência de pesquisa personalizada, pode testá-la dentro do portal Bing Custom Search.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: 07b0dd68e39f555171e5606b71cd1eec92a4035c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353345"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Ligue para o seu caso de Pesquisa Personalizada Bing a partir do Portal

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Depois de configurar a sua experiência de pesquisa personalizada, pode testá-la dentro do [portal](https://customsearch.ai)Bing Custom Search . 

![uma imagem do portal de pesquisa personalizado Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Criar uma consulta de pesquisa 

Depois de ter assinado no [portal](https://customsearch.ai)Bing Custom Search, selecione o seu caso de pesquisa e clique no separador **'Produção'.** Em **Endpoints**, selecione um ponto final da API (por exemplo, Web API). A sua subscrição determina quais os pontos finais apresentados.

Para criar uma consulta de pesquisa, introduza os valores de parâmetro para o seu ponto final. Note que os parâmetros apresentados no portal podem ser alterados dependendo do ponto final que escolher. Consulte a [referência API de Pesquisa Personalizada](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) para obter mais informações. Para alterar a subscrição que a sua instância de pesquisa utiliza, adicione a chave de subscrição adequada e atualize os parâmetros de mercado e/ou idioma apropriados.

Alguns parâmetros importantes estão abaixo:


|Parâmetro  |Descrição  |
|---------|---------|
|Consulta     | O termo de pesquisa para procurar. Disponível apenas para pontos finais Web, Image, Video e Autosuggest |
|ID de configuração personalizada | O ID de configuração da instância de pesquisa personalizada selecionada. Este campo é lido apenas. |
|Mercado     | O mercado de onde os resultados terão origem. Disponível apenas para os pontos finais web, imagem, vídeo e ui hospedados.        |
|Chave de Subscrição | A chave de subscrição para testar. Pode selecionar uma chave da lista de dropdown ou introduzir uma manualmente.          |

Clicar **em Parâmetros Adicionais** revela os seguintes parâmetros:  

|Parâmetro  |Descrição  |
|---------|---------|
|Pesquisa segura     | Um filtro utilizado para filtrar páginas web para conteúdo adulto. Disponível apenas para os pontos finais web, imagem, vídeo e ui hospedados. Note que bing Custom Video Search suporta apenas dois valores: `moderate` e `strict` .        |
|Idioma da Interface de Utilizador    | O idioma utilizado para as cordas de interface do utilizador. Por exemplo, se ativar imagens e vídeos em UI hospedado, os separadores **de imagem** e **vídeo** utilizam o idioma especificado.        |
|de palavras     | O número de resultados de pesquisa para retorno na resposta. Disponível apenas para pontos finais Web, Image e Video.         |
|Desvio    | O número de resultados de pesquisa a saltar antes de devolver os resultados. Disponível apenas para pontos finais Web, Image e Video.        |
    
Depois de especificar todas as opções necessárias, clique em **Call** para ver a resposta JSON no painel certo. Se selecionar o ponto final do UI hospedado, pode testar a experiência de pesquisa no painel inferior.

## <a name="change-your-bing-custom-search-subscription"></a>Altere a sua subscrição de Pesquisa Personalizada Bing

Pode alterar a subscrição associada à sua instância de Pesquisa Personalizada Bing sem criar uma nova instância. Para que as chamadas API são enviadas e cobradas para uma nova subscrição, crie um novo recurso Bing Custom Search no portal Azure. Utilize a nova chave de subscrição nos seus pedidos de API, juntamente com o ID de configuração personalizado do seu caso.

## <a name="next-steps"></a>Passos seguintes

- [Ligue para a sua vista personalizada com C #](./call-endpoint-csharp.md)
- [Ligue para a sua vista personalizada com Java](./call-endpoint-java.md)
- [Ligue para a sua vista personalizada com NodeJs](./call-endpoint-nodejs.md)
- [Ligue para a sua vista personalizada com Python](./call-endpoint-python.md)

- [Ligue para a sua vista personalizada com o C# SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)