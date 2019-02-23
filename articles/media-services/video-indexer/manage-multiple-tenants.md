---
title: Gerir vários inquilinos com o indexador de vídeo - Azure
description: Este artigo sugere opções diferentes de integração para o gerenciamento de vários inquilinos com o indexador de vídeo.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/10/2019
ms.author: ikbarmen
ms.openlocfilehash: e98cf44db01de1f99836e247066ba01006eac97b
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733304"
---
# <a name="manage-multiple-tenants"></a>Gerir vários inquilinos

Este artigo discute as diferentes opções para gerir vários inquilinos com o indexador de vídeo. Escolha um método que é mais adequado para o seu cenário:

* Conta de indexador de vídeo por inquilino
* Conta do Video Indexer única para todos os inquilinos
* Subscrição do Azure por inquilino

## <a name="video-indexer-account-per-tenant"></a>Conta de indexador de vídeo por inquilino

Quando utilizar esta arquitetura, é criada uma conta do Video Indexer para cada inquilino. Os inquilinos têm isolamento completo no persistente e camada de computação.  

![Conta de indexador de vídeo por inquilino](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Considerações

* Os clientes não partilhe contas de armazenamento (a não ser configuradas manualmente pelo cliente).
* Os clientes não partilham computação (unidades reservadas) e não afetam os tempos de tarefas de processamento de um do outro.
* Pode facilmente remover um inquilino do sistema ao eliminar a conta do Video Indexer.
* Não existe nenhuma capacidade de compartilhar modelos personalizados entre inquilinos.

    Certifique-se de que não existe nenhum requisito de negócio para compartilhar modelos personalizados.
* Mais difícil de gerir devido a vários Video Indexer (e os serviços de suporte de dados associados) contas por inquilino.

> [!TIP]
> Criar um utilizador de administrador para o seu sistema na [Portal do Programador de indexador de vídeo](https://api-portal.videoindexer.ai/) e utilizar a API de autorização para fornecer aos seus inquilinos a relevantes [token de acesso de conta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Conta do Video Indexer única para todos os utilizadores

Quando utilizar esta arquitetura, o cliente é responsável por isolamento de inquilinos. Todos os inquilinos têm de utilizar uma única conta do Video Indexer com uma única conta de serviço de multimédia do Azure. Ao carregar, procurar ou eliminar o conteúdo, o cliente precisará filtrar os resultados de adequada para esse inquilino.

![Conta do Video Indexer única para todos os utilizadores](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Com esta opção, os modelos de personalização (pessoa, idioma e marcas) podem ser partilhados ou isolados entre inquilinos ao filtrar os modelos por inquilino.

Quando [carregar vídeos](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), pode especificar um atributo de partição diferente por inquilino. Isso permitirá que o isolamento no [API de pesquisa](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Especificando o atributo de partição na API de pesquisa obterá apenas os resultados da partição especificada. 

### <a name="considerations"></a>Considerações

* Capacidade de compartilhar conteúdo e personalização de modelos entre inquilinos.
* Um inquilino impacta o desempenho de outros inquilinos.
* Cliente tem de criar uma camada de gerenciamento complexas sobre o Video Indexer.

> [!TIP]
> Pode utilizar o [prioridade](upload-index-videos.md) atributo para dar prioridade às tarefas de inquilinos.

## <a name="azure-subscription-per-tenant"></a>Subscrição do Azure por inquilino 

Quando utilizar esta arquitetura, cada inquilino terá sua própria subscrição do Azure. Para cada utilizador, irá criar uma nova conta do Video Indexer na subscrição do inquilino.

![Subscrição do Azure por inquilino](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Considerações

* Esta é a única opção que permite a separação de faturação.
* Esta integração tem mais sobrecarga de gerenciamento do que a conta do Video Indexer por inquilino. Se a faturação não é um requisito, recomenda-se para utilizar uma das outras opções descritas neste artigo.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral](video-indexer-overview.md)
