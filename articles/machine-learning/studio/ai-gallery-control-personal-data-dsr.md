---
title: Gerir dados da Galeria Azure AI
titleSuffix: ML Studio (classic) - Azure
description: Pode exportar e eliminar os seus dados de utilizador no produto da Azure AI Gallery utilizando a interface ou a AI Gallery Catalog API. Este artigo mostra-lhe como.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204550"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Ver e eliminar dados de utilizadores no produto da Galeria Azure AI

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Pode ver e eliminar os seus dados de utilizador no produto da Galeria Azure AI utilizando a interface ou a AI Gallery Catalog API. Este artigo diz-lhe como.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Veja os seus dados na Galeria de IA com a UI

Pode ver itens publicados através do site da Azure AI Gallery UI. Os utilizadores podem ver soluções públicas e não listadas, projetos, experiências e outros itens publicados:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem de perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    A página de perfil exibe todos os itens publicados na galeria, incluindo entradas não listadas.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Utilize a API do Catálogo de Galerias AI para ver os seus dados

Pode visualizar programáticamente os dados recolhidos através da AI Gallery Catalog API, acessível a https://catalog.cortanaanalytics.com/entities. Para visualizar os dados, precisa da sua identificação de autor. Para visualizar entidades não cotadas através da API do Catálogo, precisa de um sinal de acesso.

As respostas do catálogo são devolvidas em formato JSON.

### <a name="get-an-author-id"></a>Obtenha uma identificação de autor
O ID do autor baseia-se no endereço de e-mail utilizado na publicação na Galeria Azure AI. Não muda:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem de perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    O URL na barra de endereços `authorId=`apresenta o ID alfanumérico a seguir . Por exemplo, para o URL:`https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Id do autor:`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Obtenha o seu sinal de acesso

Precisa de um sinal de acesso para visualizar entidades não cotadas através da API do catálogo. Sem um Access Token, os utilizadores ainda podem ver entidades públicas e outras informações do utilizador.

Para obter um sinal de acesso, `DataLabAccessToken` você precisa inspecionar o cabeçalho de um pedido HTTP que o navegador faz para o Catálogo API enquanto faz login:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem de perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    Abra o painel de ferramentas do navegador Developer Tools premindo F12, selecione o separador Rede e refresque a página. 
4. Filtrar pedidos no *catálogo* de cordas digitando na caixa de texto Filter.
5.    Nos pedidos ao `https://catalog.cortanaanalytics.com/entities`URL, encontre um pedido GET e selecione *Request Headers* o separador *Cabeçalhos.*
6.    Debaixo do `DataLabAccessToken` cabeceamento está o símbolo alfanumérico. Para ajudar a manter os seus dados seguros, não partilhe este símbolo.

### <a name="view-user-information"></a>Ver informações do utilizador
Utilizando o ID do autor que obteve nos passos anteriores, `[AuthorId]` veja a informação no perfil de um utilizador substituindo no seguinte URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Por exemplo, este pedido de URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Devolve uma resposta como:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Ver entidades públicas

A API do Catálogo armazena informações sobre entidades publicadas na Galeria Azure AI que também pode ver diretamente no site da [Galeria AI.](https://gallery.azure.ai/) 

Para visualizar as entidades publicadas, `[AuthorId]` visite o seguinte URL, substituindo o ID do autor obtido em [Obter um ID autor](#get-an-author-id) acima.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Por exemplo:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Ver entidades não cotadas e públicas

Esta consulta exibe apenas entidades públicas. Para ver todas as suas entidades, incluindo as não cotadas, fornecer o sinal de acesso obtido na secção anterior.

1.    Utilizando uma ferramenta como [o Carteiro,](https://www.getpostman.com)crie um pedido HTTP GET para o URL do catálogo, conforme descrito no [token](#get-your-access-token)de acesso .
2.    Crie um cabeçalho de pedido HTTP chamado `DataLabAccessToken`, com o valor definido para o token de acesso.
3.    Envie o pedido HTTP.

> [!TIP]
> Se as entidades não cotadas não aparecerem em respostas da API do catálogo, o utilizador pode ter um sinal de acesso inválido ou expirado. Assine a Galeria Azure AI e repita os passos em Obter o [seu sinal](#get-your-access-token) de acesso para renovar o símbolo. 
