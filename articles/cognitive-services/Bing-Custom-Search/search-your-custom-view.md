---
title: Procurar uma exibição personalizada - pesquisa personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Descreve como pesquisar uma vista personalizada da web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 9c0c4fc21f6f4b23b14c8020ecd7e95c63dedcbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388555"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Chamar a sua instância de pesquisa personalizada do Bing do Portal

Após configurar a sua experiência de pesquisa personalizada, pode testá-lo de dentro da pesquisa personalizada do Bing [portal](https://customsearch.ai). 

![uma captura de ecrã do portal de pesquisa personalizada do Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Criar uma consulta de pesquisa 

Depois de se para a pesquisa personalizada do Bing [portal](https://customsearch.ai), selecione a sua instância de pesquisa e clique nas **produção** separador. Sob **pontos de extremidade**, selecione um ponto final de API (por exemplo, API Web). A subscrição determina quais pontos de extremidade são exibidos.

Para criar uma consulta de pesquisa, introduza os valores de parâmetro para o ponto final. Tenha em atenção que os parâmetros apresentados no portal do podem ser alterados dependendo do ponto final que escolher. Consulte a [referência da API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) para obter mais informações. Para alterar a subscrição que utiliza a sua instância de pesquisa, adicione a chave de subscrição adequada e atualize os parâmetros de mercado e/ou o idioma apropriados.

Alguns parâmetros importantes estão abaixo:


|Parâmetro  |Descrição  |
|---------|---------|
|Consulta     | O termo de pesquisa para procurar. Disponível apenas para pontos finais de Web, imagem, vídeo e de sugestão automática |
|ID de configuração personalizada | O ID de configuração da instância de pesquisa personalizada selecionada. Este campo é só de leitura. |
|Market     | O mercado resultados serão provenientes de mensagens em fila. Apenas disponível para os pontos de extremidade da Web, imagem, vídeo e hospedado da interface do Usuário.        |
|Chave de subscrição | A chave de subscrição para testar com. Pode selecionar uma chave na lista pendente, ou introduza um manualmente.          |

Clicar **parâmetros adicionais** revela os seguintes parâmetros:  

|Parâmetro  |Descrição  |
|---------|---------|
|Safe Search     | Um filtro utilizado para filtrar as páginas Web para o conteúdo para adultos. Apenas disponível para os pontos de extremidade da Web, imagem, vídeo e hospedado da interface do Usuário.        |
|Idioma da Interface do usuário    | O idioma usado para cadeias de caracteres de interface de utilizador. Por exemplo, se ativar imagens e vídeos na interface do Usuário do hospedado, o **imagem** e **vídeo** separadores utilizam o idioma especificado.        |
|Count     | O número de resultados da pesquisa para devolver na resposta. Disponível apenas para pontos finais de Web, imagem e vídeo.         |
|Offset    | O número de resultados de pesquisa a ignorar antes de retornar resultados. Disponível apenas para pontos finais de Web, imagem e vídeo.        |
    
Depois de especificar todas as opções necessárias, clique em **chamar** para ver a resposta JSON no painel da direita. Se selecionar o ponto de extremidade hospedado da interface do Usuário, pode testar a experiência de pesquisa no painel inferior.

## <a name="change-your-bing-custom-search-subscription"></a>Alterar a sua subscrição de pesquisa personalizada do Bing

Pode alterar a subscrição associada à sua instância de pesquisa personalizada do Bing sem criar uma nova instância. Para que as chamadas de API enviados sendo cobrado uma nova subscrição, crie um novo recurso de pesquisa personalizada do Bing no portal do Azure. Utilizar a nova chave de subscrição nos seus pedidos de API, juntamente com o ID de configuração personalizada. da sua instância

## <a name="next-steps"></a>Passos Seguintes

- [Chamar a sua vista personalizada com c#](./call-endpoint-csharp.md)
- [Chamar a sua vista personalizada com Java](./call-endpoint-java.md)
- [Chamar a sua vista personalizada com NodeJs](./call-endpoint-nodejs.md)
- [Chamar a sua vista personalizada com Python](./call-endpoint-python.md)

- [Chamar a sua vista personalizada com o SDK do c#](./sdk-csharp-quick-start.md)
