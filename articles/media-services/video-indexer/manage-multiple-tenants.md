---
title: Gerir vários inquilinos com Índice de Vídeo - Azure
description: Este artigo sugere diferentes opções de integração para gerir vários inquilinos com Índice de Vídeo.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76990509"
---
# <a name="manage-multiple-tenants"></a>Gerir vários inquilinos

Este artigo discute diferentes opções para gerir vários inquilinos com Índice de Vídeo. Escolha um método mais adequado para o seu cenário:

* Conta indexante de vídeo por inquilino
* Indexante de Vídeo Único conta para todos os inquilinos
* Assinatura Azure por inquilino

## <a name="video-indexer-account-per-tenant"></a>Conta indexante de vídeo por inquilino

Ao utilizar esta arquitetura, é criada uma conta de Indexer de Vídeo para cada inquilino. Os inquilinos têm isolamento total na camada persistente e computacional.  

![Conta indexante de vídeo por inquilino](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Considerações

* Os clientes não partilham contas de armazenamento (a menos que configuram manualmente pelo cliente).
* Os clientes não partilham o cálculo (unidades reservadas) e não têm impacto nos tempos de processamento dos tempos uns dos outros.
* Pode facilmente remover um inquilino do sistema eliminando a conta de Indexer de Vídeo.
* Não há capacidade de partilhar modelos personalizados entre inquilinos.

    Certifique-se de que não existe necessidade de partilhar modelos personalizados.
* Mais difícil de gerir devido a várias contas de Video Indexer (e serviços de mídia associados) por inquilino.

> [!TIP]
> Crie um utilizador administrativo para o seu sistema no [Portal do Desenvolvedor de Indexados de Vídeo](https://api-portal.videoindexer.ai/) e utilize a API de Autorização para fornecer aos seus inquilinos o [token](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)de acesso à conta relevante .

## <a name="single-video-indexer-account-for-all-users"></a>Conta Indexer de vídeo único para todos os utilizadores

Ao utilizar esta arquitetura, o cliente é responsável pelo isolamento dos inquilinos. Todos os inquilinos têm de usar uma única conta de Indexer de Vídeo com uma única conta do Azure Media Service. Ao carregar, pesquisar ou eliminar conteúdo, o cliente terá de filtrar os resultados adequados para esse inquilino.

![Conta Indexer de vídeo único para todos os utilizadores](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Com esta opção, os modelos de personalização (Pessoa, Língua e Marcas) podem ser partilhados ou isolados entre inquilinos filtrando os modelos por inquilino.

Ao [carregar vídeos,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)pode especificar um atributo de partição diferente por inquilino. Isto permitirá o isolamento na [pesquisa API.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?) Ao especificar o atributo de partição na API de pesquisa, só obterá resultados da partição especificada. 

### <a name="considerations"></a>Considerações

* Capacidade de partilhar conteúdos e modelos de personalização entre inquilinos.
* Um inquilino tem impacto no desempenho de outros inquilinos.
* O cliente precisa de construir uma camada de gestão complexa em cima do Video Indexer.

> [!TIP]
> Pode usar o atributo [prioritário](upload-index-videos.md) para priorizar os empregos dos inquilinos.

## <a name="azure-subscription-per-tenant"></a>Assinatura Azure por inquilino 

Ao utilizar esta arquitetura, cada inquilino terá a sua própria assinatura Azure. Para cada utilizador, irá criar uma nova conta de Indexer de Vídeo na subscrição do inquilino.

![Assinatura Azure por inquilino](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Considerações

* Esta é a única opção que permite a separação da faturação.
* Esta integração tem mais despesas de gestão do que a conta de Indexer de Vídeo por inquilino. Se a faturação não for um requisito, recomenda-se a utilização de uma das outras opções descritas neste artigo.

## <a name="next-steps"></a>Próximos passos

[Descrição geral](video-indexer-overview.md)
