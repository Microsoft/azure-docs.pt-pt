---
title: 'Estúdio ML (clássico): Gerir os dados da Galeria Azure AI - Azure'
description: Pode exportar e eliminar os seus dados de utilizador no produto da Galeria Azure AI utilizando a interface ou a AI Gallery Catalog API. Este artigo mostra-lhe como.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 1c549cd4a4d76e96f16eb04f3cf5aa3368725b8c
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070546"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Ver e apagar dados de utilizadores no produto da Galeria Azure AI



Pode ver e eliminar os dados do utilizador no produto da Galeria Azure AI utilizando a interface ou a AI Gallery Catalog API. Este artigo diz-lhe como.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Ver os seus dados na Galeria AI com a UI

Pode ver os itens que publicou através do site da Galeria Azure AI UI. Os utilizadores podem ver soluções públicas e não cotadas, projetos, experiências e outros itens publicados:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    A página de perfil exibe todos os itens publicados na galeria, incluindo entradas não cotadas.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Utilize a AI Gallery Catalog API para ver os seus dados

Pode visualizar programáticamente os dados recolhidos através do Catálogo API da Galeria AI, que é acessível em https://catalog.cortanaanalytics.com/entities . Para visualizar dados, precisa do seu ID de autor. Para visualizar entidades não cotadas através do Catálogo API, precisa de um token de acesso.

As respostas do catálogo são devolvidas no formato JSON.

### <a name="get-an-author-id"></a>Obter uma iD de autor
O ID do autor baseia-se no endereço de e-mail utilizado na publicação na Galeria Azure AI. Não muda.

1. Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2. Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3. O URL na barra de endereços apresenta o ID alfanumérico que se segue `authorId=` . Por exemplo, para o URL:\
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    ID do autor:\
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Obtenha o seu token de acesso

Precisa de um token de acesso para visualizar entidades não cotadas através do Catálogo API. Sem um Token de Acesso, os utilizadores ainda podem ver entidades públicas e outras informações do utilizador.

Para obter um token de acesso, você precisa inspecionar o `DataLabAccessToken` cabeçalho de um pedido HTTP que o navegador faz para a API catálogo enquanto inicia sessão:

1.    Inscreva-se na [Galeria Azure AI.](https://gallery.azure.ai/)
2.    Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar a sua página de perfil.
3.    Abra o painel de ferramentas do programador do navegador pressionando F12, selecione o separador 'Rede' e refresque a página. 
4. Filtrar os pedidos no *catálogo* de cordas digitando a caixa de texto do filtro.
5.    Nos pedidos para o `https://catalog.cortanaanalytics.com/entities` URL, encontre um pedido GET e  selecione o *separador Cabeçalhos.*
6.    Debaixo do cabeçalho está o símbolo `DataLabAccessToken` alfanumérico. Para ajudar a manter os seus dados seguros, não partilhe este símbolo.

### <a name="view-user-information"></a>Ver informações do utilizador
Utilizando o ID do autor que obteve nos passos anteriores, veja as informações no perfil de um utilizador substituindo `[AuthorId]` no seguinte URL:

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Por exemplo, este pedido de URL:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Devolve uma resposta como:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Ver entidades públicas

O Catálogo API armazena informações sobre entidades publicadas na Galeria Azure AI que também pode ver diretamente no site da [Galeria AI.](https://gallery.azure.ai/) 

Para ver as entidades publicadas, visite o seguinte URL, substituindo `[AuthorId]` pelo ID de autor obtido em [Obter um ID de autor](#get-an-author-id) acima.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Por exemplo:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Ver entidades não cotadas e públicas

Esta consulta apresenta apenas entidades públicas. Para visualizar todas as suas entidades, incluindo as não cotadas, forneça o token de acesso obtido a partir da secção anterior.

1.    Utilizando uma ferramenta como [o Carteiro,](https://www.getpostman.com)crie um pedido HTTP GET para o URL do catálogo, conforme descrito no [Token de acesso](#get-your-access-token).
2.    Crie um cabeçalho de pedido HTTP chamado `DataLabAccessToken` , com o valor definido para o token de acesso.
3.    Envie o pedido HTTP.

> [!TIP]
> Se as entidades não cotadas não aparecerem em respostas da API do Catálogo, o utilizador poderá ter um token de acesso inválido ou caducado. Assine fora da Galeria Azure AI e, em seguida, repita os passos em [Obter o seu token de acesso](#get-your-access-token) para renovar o token. 
