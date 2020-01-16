---
title: Gerenciar contas dos serviços de mídia do Azure v3 | Microsoft Docs
description: Para começar a gerenciar, criptografar, codificar, analisar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de serviços de mídia. Este artigo explica como gerenciar contas dos serviços de mídia do Azure v3.
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
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980369"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gerenciar contas dos serviços de mídia do Azure v3

Para começar a gerenciar, criptografar, codificar, analisar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de serviços de mídia. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Para obter mais informações, consulte [contas de armazenamento](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Movendo uma conta dos serviços de mídia entre assinaturas 

Se precisar mover uma conta dos serviços de mídia para uma nova assinatura, primeiro você precisará mover todo o grupo de recursos que contém a conta dos serviços de mídia para a nova assinatura. Você deve mover todos os recursos anexados: contas de armazenamento do Azure, perfis da CDN do Azure, etc. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Assim como ocorre com qualquer recurso no Azure, as movimentações do grupo de recursos podem levar algum tempo para serem concluídas.

> [!NOTE]
> Os serviços de mídia v3 oferecem suporte ao modelo de multilocação.

### <a name="considerations"></a>Considerações

* Crie backups de todos os dados em sua conta antes de migrar para uma assinatura diferente.
* Você precisa interromper todos os pontos de extremidade de streaming e recursos de transmissão ao vivo. Os usuários não poderão acessar o conteúdo pela duração da movimentação do grupo de recursos. 

> [!IMPORTANT]
> Não inicie o ponto de extremidade de streaming até que a movimentação seja concluída com êxito.

### <a name="troubleshoot"></a>Resolução de problemas 

Se uma conta dos serviços de mídia ou uma conta de armazenamento do Azure associada se tornar "desconectada" após a movimentação do grupo de recursos, tente girar as chaves da conta de armazenamento. Se a rotação das chaves da conta de armazenamento não resolver o status "desconectado" da conta dos serviços de mídia, faça uma nova solicitação de suporte no menu "suporte + solução de problemas" na conta dos serviços de mídia.  

## <a name="next-steps"></a>Passos seguintes

[Criar uma conta](create-account-cli-quickstart.md)
