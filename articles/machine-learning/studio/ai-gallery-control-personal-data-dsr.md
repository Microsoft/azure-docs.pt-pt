---
title: Gerenciar dados de Galeria de IA do Azure
titleSuffix: Azure Machine Learning Studio (classic)
description: Você pode exportar e excluir seus dados de usuário no produto de Galeria de IA do Azure usando a interface ou a API de catálogo da Galeria de ia. Este artigo mostra como.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: f0b0bd4b01056769d38179597e477ecb164fa9ab
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493550"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Exibir e excluir dados de usuário no produto do Galeria de IA do Azure

Você pode exibir e excluir seus dados de usuário no produto de Galeria de IA do Azure usando a interface ou a API de catálogo da Galeria de ia. Este artigo explica como.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Exibir seus dados na Galeria de ia com a interface do usuário

Você pode exibir os itens publicados por meio da interface do usuário do site Galeria de IA do Azure. Os usuários podem exibir soluções públicas e não listadas, projetos, experimentos e outros itens publicados:

1.  Entre no [Galeria de ia do Azure](https://gallery.azure.ai/).
2.  Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar sua página de perfil.
3.  A página Perfil exibe todos os itens publicados na Galeria, incluindo entradas não listadas.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Use a API de catálogo da Galeria de ia para exibir seus dados

Você pode exibir de forma programática os dados coletados por meio da API de catálogo da Galeria de ia, que é acessível em https://catalog.cortanaanalytics.com/entities. Para exibir dados, você precisa da ID do autor. Para exibir entidades não listadas por meio da API de catálogo, você precisa de um token de acesso.

As respostas do catálogo são retornadas no formato JSON.

### <a name="get-an-author-id"></a>Obter uma ID do autor
A ID do autor se baseia no endereço de email usado ao publicar no Galeria de IA do Azure. Ele não é alterado:

1.  Entre no [Galeria de ia do Azure](https://gallery.azure.ai/).
2.  Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar sua página de perfil.
3.  A URL na barra de endereços exibe a ID alfanumérica seguinte `authorId=`. Por exemplo, para a URL: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    ID do autor: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Obter seu token de acesso

Você precisa de um token de acesso para exibir entidades não listadas por meio da API do catálogo. Sem um token de acesso, os usuários ainda podem exibir entidades públicas e outras informações de usuário.

Para obter um token de acesso, você precisa inspecionar o cabeçalho de `DataLabAccessToken` de uma solicitação HTTP que o navegador faz para a API de catálogo enquanto estiver conectado:

1.  Entre no [Galeria de ia do Azure](https://gallery.azure.ai/).
2.  Clique na imagem do perfil no canto superior direito e, em seguida, o nome da conta para carregar sua página de perfil.
3.  Abra o painel Ferramentas para Desenvolvedores do navegador pressionando F12, selecione a guia rede e atualize a página. 
4. Filtre as solicitações no *Catálogo* de cadeias de caracteres digitando na caixa de texto de filtro.
5.  Em solicitações para a URL `https://catalog.cortanaanalytics.com/entities`, localize uma solicitação GET e selecione a guia *cabeçalhos* . Role para baixo até a seção *cabeçalhos de solicitação* .
6.  Sob o cabeçalho `DataLabAccessToken` é o token alfanumérico. Para ajudar a manter seus dados seguros, não compartilhe esse token.

### <a name="view-user-information"></a>Exibir informações do usuário
Usando a ID do autor que você obteve nas etapas anteriores, exiba as informações em um perfil de usuário, substituindo `[AuthorId]` na seguinte URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Por exemplo, esta solicitação de URL:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Retorna uma resposta como:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Exibir entidades públicas

A API de catálogo armazena informações sobre entidades publicadas no Galeria de IA do Azure que você também pode exibir diretamente no [site da Galeria de ia](https://gallery.azure.ai/). 

Para exibir as entidades publicadas, visite a URL a seguir, substituindo `[AuthorId]` pela ID de autor obtida em [obter uma ID de autor](#get-an-author-id) acima.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Por exemplo:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Exibir entidades não listadas e públicas

Essa consulta exibe somente entidades públicas. Para exibir todas as suas entidades, incluindo aquelas não listadas, forneça o token de acesso obtido na seção anterior.

1.  Usando uma ferramenta como o [postmaster](https://www.getpostman.com), crie uma solicitação HTTP Get para a URL do catálogo, conforme descrito em [obter seu token de acesso](#get-your-access-token).
2.  Crie um cabeçalho de solicitação HTTP chamado `DataLabAccessToken`, com o valor definido para o token de acesso.
3.  Envie a solicitação HTTP.

> [!TIP]
> Se as entidades não listadas não estiverem aparecendo em respostas da API do catálogo, o usuário poderá ter um token de acesso inválido ou expirado. Saia do Galeria de IA do Azure e repita as etapas em [obter seu token de acesso](#get-your-access-token) para renovar o token. 
