---
title: Gerir dados da Galeria Azure AI
titleSuffix: ML Studio (classic) - Azure
description: Pode exportar e eliminar os dados dos utilizadores no produto da Galeria de IA do Azure utilizando a interface ou a API do catálogo de Galeria de IA. Este artigo mostra-lhe como.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 03341b9e663398f2c42266dead0d2dd01e97c3f3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204550"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Ver e eliminar dados de utilizador no produto da Galeria de IA do Azure

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Pode ver e eliminar os dados dos utilizadores no produto da Galeria de IA do Azure utilizando a interface ou a API do catálogo de Galeria de IA. Este artigo mostra-lhe como.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Ver os seus dados na Galeria de IA com a interface do Usuário

Pode visualizar os itens publicados através do site de Galeria de IA do Azure da interface do Usuário. Os utilizadores podem ver soluções públicas e não listadas, projetos, experimentações e outros itens publicados:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    A página de perfil apresenta todos os itens publicados na galeria, incluindo as entradas não listadas.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Utilizar a API de catálogo de Galeria de ia para ver os seus dados

Pode visualizar programáticamente os dados recolhidos através da AI Gallery Catalog API, acessível a https://catalog.cortanaanalytics.com/entities. Para ver os dados, terá de sua ID do autor. Para ver as entidades não listadas por meio da API do catálogo, terá de um token de acesso.

Respostas de catálogo são devolvidas no formato JSON.

### <a name="get-an-author-id"></a>Obter uma ID do autor
O ID do autor baseia-se no endereço de e-mail utilizado quando publicar a Galeria de IA do Azure. Ele não é alterado:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    O URL na barra de endereços apresenta o ID alfanumérico após `authorId=`. Por exemplo, para o URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Id do autor: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Obter o token de acesso

Precisa de um token de acesso para ver as entidades não listadas por meio da API do catálogo. Sem um Token de acesso, os utilizadores ainda podem ver as entidades públicas e outras informações de utilizador.

Para obter um sinal de acesso, você precisa inspecionar o cabeçalho `DataLabAccessToken` de um pedido HTTP que o navegador faz para a API do Catálogo enquanto faz login:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    Abra o painel de ferramentas de programação do browser, premindo F12, selecione o separador rede e atualize a página. 
4. Filtrar pedidos no *catálogo* de cordas digitando na caixa de texto Filter.
5.    Nos pedidos para o URL `https://catalog.cortanaanalytics.com/entities`, encontre um pedido GET e selecione o separador *Cabeçalhos.*
6.    Sob o cabeceamento `DataLabAccessToken` está o símbolo alfanumérico. Para ajudar a manter os seus dados seguros, não partilhe este token.

### <a name="view-user-information"></a>Ver informações de utilizador
Utilizando o ID do autor que obteve nos passos anteriores, veja a informação no perfil de um utilizador substituindo `[AuthorId]` no seguinte URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Por exemplo, este pedido de URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Devolve uma resposta, tais como:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Vista de entidades públicas

A API do Catálogo armazena informações sobre entidades publicadas na Galeria Azure AI que também pode ver diretamente no site da [Galeria AI.](https://gallery.azure.ai/) 

Para visualizar as entidades publicadas, visite o seguinte URL, substituindo `[AuthorId]` com o ID do autor obtido em [Obter um ID autor](#get-an-author-id) acima.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Por exemplo:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Ver as entidades públicas e não listadas

Esta consulta apresenta apenas as entidades públicas. Para ver todas as suas entidades, incluindo os não listados, fornecer o acesso ao token obtido a partir da secção anterior.

1.    Utilizando uma ferramenta como [o Carteiro,](https://www.getpostman.com)crie um pedido HTTP GET para o URL do catálogo, conforme descrito no [token](#get-your-access-token)de acesso .
2.    Crie um cabeçalho de pedido HTTP chamado `DataLabAccessToken`, com o valor definido para o token de acesso.
3.    Submeta o pedido HTTP.

> [!TIP]
> Se a entidades não listadas não são exibidas nas respostas da API do catálogo, o utilizador pode ter um inválido ou expirado o token de acesso. Assine a Galeria Azure AI e repita os passos em Obter o [seu sinal](#get-your-access-token) de acesso para renovar o símbolo. 
