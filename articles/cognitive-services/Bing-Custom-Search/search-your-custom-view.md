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
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: f00a8a02ac7f9468516506b6e6a376209d24c270
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448890"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Chamar a sua instância de pesquisa personalizada do Bing do Portal

Após configurar a sua experiência de pesquisa personalizada, pode testá-lo de dentro da pesquisa personalizada do Bing [portal](https://customsearch.ai). 

![uma captura de ecrã do portal de pesquisa personalizada do Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Criar uma consulta de pesquisa 

Depois de se para a pesquisa personalizada do Bing [portal](https://customsearch.ai), selecione a sua instância de pesquisa e clique nas **produção** separador. Sob **pontos de extremidade**, selecione um ponto final de API (por exemplo, API Web). A subscrição determina quais pontos de extremidade são exibidos.

Para criar uma consulta de pesquisa, introduza os valores de parâmetro para o ponto final. Tenha em atenção que os parâmetros apresentados no portal do podem ser alterados dependendo do ponto final que escolher. Consulte a[referência da API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) para obter mais informações. 

Alguns parâmetros importantes estão abaixo:


|Parâmetro  |Descrição  |
|---------|---------|
|Consulta     | O termo de pesquisa para procurar. Disponível apenas para pontos finais de Web, imagem, vídeo e de sugestão automática |
|ID de configuração personalizada | O ID de configuração da instância de pesquisa personalizada selecionada. Este campo é só de leitura. |
|Comercializar     | O mercado resultados serão provenientes de mensagens em fila. Apenas disponível para os pontos de extremidade da Web, imagem, vídeo e hospedado da interface do Usuário.        |
|Chave de Subscrição | A chave de subscrição para testar com. Pode selecionar uma chave na lista pendente, ou introduza um manualmente.          |

Clicar **parâmetros adicionais** revela os seguintes parâmetros:  

|Parâmetro  |Descrição  |
|---------|---------|
|Pesquisa segura     | Um filtro utilizado para filtrar as páginas Web para o conteúdo para adultos. Apenas disponível para os pontos de extremidade da Web, imagem, vídeo e hospedado da interface do Usuário.        |
|Idioma da Interface do usuário    | O idioma usado para cadeias de caracteres de interface de utilizador. Por exemplo, se ativar imagens e vídeos na interface do Usuário do hospedado, o **imagem** e **vídeo** separadores utilizam o idioma especificado.        |
|Contagem     | O número de resultados da pesquisa para devolver na resposta. Disponível apenas para pontos finais de Web, imagem e vídeo.         |
|Desvio    | O número de resultados de pesquisa a ignorar antes de retornar resultados. Disponível apenas para pontos finais de Web, imagem e vídeo.        |
    
Depois de especificar todas as opções necessárias, clique em **chamar** para ver a resposta JSON no painel da direita. Se selecionar o ponto de extremidade hospedado da interface do Usuário, pode testar a experiência de pesquisa no painel inferior.

## <a name="next-steps"></a>Passos Seguintes

- [Chamar a sua vista personalizada com c#](./call-endpoint-csharp.md)
- [Chamar a sua vista personalizada com Java](./call-endpoint-java.md)
- [Chamar a sua vista personalizada com NodeJs](./call-endpoint-nodejs.md)
- [Chamar a sua vista personalizada com Python](./call-endpoint-python.md)

- [Chamar a sua vista personalizada com o SDK do c#](./sdk-csharp-quick-start.md)
