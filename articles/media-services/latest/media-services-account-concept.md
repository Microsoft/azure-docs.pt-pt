---
title: Gerir contas de v3 de serviços de multimédia do Azure | Documentos da Microsoft
description: Para começar a gerir, encriptar, codificar, analisar e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services. Este artigo explica como gerir contas de v3 de serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670689"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gerir contas de v3 de serviços de multimédia do Azure

Para começar a gerir, encriptar, codificar, analisar e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Para obter mais informações, consulte [contas de armazenamento](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Mover uma conta dos serviços de suporte de dados entre subscrições 

Se precisar de mover uma conta de serviços de multimédia para uma nova subscrição, terá de mover primeiro o grupo de recursos inteiro que contém a conta de serviços de multimédia para a nova subscrição. Tem de mover todos os recursos anexados: Contas de armazenamento do Azure, os perfis de CDN do Azure, etc. Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../../azure-resource-manager/resource-group-move-resources.md). Tal como acontece com todos os recursos no Azure, são movidos do grupo de recursos podem demorar algum tempo a concluir.

> [!NOTE]
> Serviços de multimédia v3 suporta o modelo de vários inquilinos.

### <a name="considerations"></a>Considerações

* Crie cópias de segurança de todos os dados na sua conta antes de migrar para uma subscrição diferente.
* Terá de parar todos os de transmissão em fluxo pontos finais e em direto de recursos de transmissão em fluxo. Os utilizadores não será capazes de aceder ao conteúdo durante a mudança do grupo de recursos. 

> [!IMPORTANT]
> Não é inicie o ponto final de transmissão em fluxo até que a migração for concluída com êxito.

### <a name="troubleshoot"></a>Resolução de problemas 

Se uma conta de Media Services ou uma conta de armazenamento do Azure associada "desconectada" seguindo a mudança do grupo de recursos, tente alternar as chaves de conta de armazenamento. Um novo pedido de suporte a partir de ficheiros se rodar as chaves de conta de armazenamento não resolver o estado "desconectado" da conta de Media Services, o "Suporte + resolução de problemas" menu na conta de Media Services.  

## <a name="next-steps"></a>Passos Seguintes

[Criar uma conta](create-account-cli-quickstart.md)
