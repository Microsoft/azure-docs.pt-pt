---
title: Pesquisar um modo de exibição personalizado-Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Descreve como pesquisar uma exibição personalizada da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 814f57d4011823da80e53cce41ffcb523fc0bf1b
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405000"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Chamar sua instância do Pesquisa Personalizada do Bing no portal

Depois de configurar sua experiência de pesquisa personalizada, você pode testá-la de dentro do [portal](https://customsearch.ai)de pesquisa personalizada do Bing. 

![uma captura de tela do portal de pesquisa personalizada do Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Criar uma consulta de pesquisa 

Depois de entrar no [portal](https://customsearch.ai)de pesquisa personalizada do Bing, selecione sua instância de pesquisa e clique na guia **produção** . Em **ponto**de extremidade, selecione um ponto de extremidade de API (por exemplo, API Web). Sua assinatura determina quais pontos de extremidade são mostrados.

Para criar uma consulta de pesquisa, insira os valores de parâmetro para seu ponto de extremidade. Observe que os parâmetros exibidos no portal podem ser alterados dependendo do ponto de extremidade que você escolher. Consulte a [referência de API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) para obter mais informações. Para alterar a assinatura que sua instância de pesquisa usa, adicione a chave de assinatura apropriada e atualize os parâmetros de mercado e/ou de idioma apropriados.

Alguns parâmetros importantes estão abaixo:


|Parâmetro  |Descrição  |
|---------|---------|
|Consulta     | O termo de pesquisa a ser pesquisado. Disponível somente para pontos de extremidade de Web, imagem, vídeo e sugestão automática |
|ID de configuração personalizada | A ID de configuração da instância de pesquisa personalizada selecionada. Este campo é somente leitura. |
|Market     | O mercado do qual os resultados serão originados. Disponível somente para os pontos de extremidade da Web, imagem, vídeo e interface do usuário hospedada.        |
|Chave de Subscrição | A chave de assinatura para teste. Você pode selecionar uma chave na lista suspensa ou inseri-la manualmente.          |

Clicar em **parâmetros adicionais** revela os seguintes parâmetros:  

|Parâmetro  |Descrição  |
|---------|---------|
|Safe Search     | Um filtro usado para filtrar páginas da Web para conteúdo adulto. Disponível somente para os pontos de extremidade da Web, imagem, vídeo e interface do usuário hospedada.        |
|Idioma da interface do usuário    | O idioma usado para cadeias de caracteres da interface do usuário. Por exemplo, se você habilitar imagens e vídeos na interface do usuário hospedada, as guias **imagem** e **vídeo** usarão o idioma especificado.        |
|Count     | O número de resultados da pesquisa a serem retornados na resposta. Disponível somente para pontos de extremidade da Web, imagem e vídeo.         |
|Offset    | O número de resultados da pesquisa a serem ignorados antes de retornar os resultados. Disponível somente para pontos de extremidade da Web, imagem e vídeo.        |
    
Depois de especificar todas as opções necessárias, clique em **chamar** para exibir a resposta JSON no painel direito. Se você selecionar o ponto de extremidade da interface do usuário hospedada, poderá testar a experiência de pesquisa no painel inferior.

## <a name="change-your-bing-custom-search-subscription"></a>Alterar sua assinatura do Pesquisa Personalizada do Bing

Você pode alterar a assinatura associada à sua instância de Pesquisa Personalizada do Bing sem criar uma nova instância. Para que as chamadas à API sejam enviadas e cobradas para uma nova assinatura, crie um novo recurso de Pesquisa Personalizada do Bing no portal do Azure. Use a nova chave de assinatura em suas solicitações de API, juntamente com a ID de configuração personalizada da sua instância.

## <a name="next-steps"></a>Passos Seguintes

- [Chame sua exibição personalizada comC#](./call-endpoint-csharp.md)
- [Chame seu modo de exibição personalizado com Java](./call-endpoint-java.md)
- [Chame sua exibição personalizada com NodeJs](./call-endpoint-nodejs.md)
- [Chame sua exibição personalizada com o Python](./call-endpoint-python.md)

- [Chamar sua exibição personalizada com o C# SDK](./sdk-csharp-quick-start.md)
