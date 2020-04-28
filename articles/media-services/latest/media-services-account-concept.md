---
title: Gerir contas Azure Media Services v3 [ Microsoft Docs
description: Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, precisa de criar uma conta de Media Services. Este artigo explica como gerir as contas V3 da Azure Media Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75980369"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gerir contas V3 da Azure Media Services

Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, precisa de criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Para mais informações, consulte [as contas de Armazenamento.](storage-account-concept.md)

## <a name="moving-a-media-services-account-between-subscriptions"></a>Mover uma conta de Serviços de Media entre assinaturas 

Se precisa de mover uma conta de Media Services para uma nova subscrição, precisa de mover primeiro todo o grupo de recursos que contém a conta media Services para a nova subscrição. Deve mover todos os recursos anexados: Contas de Armazenamento Azure, perfis De CDN Azure, etc. Para mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) Como em quaisquer recursos em Azure, os movimentos do grupo de recursos podem levar algum tempo a ser concluídos.

> [!NOTE]
> A Media Services v3 suporta o modelo multi-arrendamento.

### <a name="considerations"></a>Considerações

* Crie cópias de segurança de todos os dados da sua conta antes de migrar para uma subscrição diferente.
* Tens de parar todos os Streaming Endpoints e recursos de streaming ao vivo. Os seus utilizadores não poderão aceder ao seu conteúdo durante a duração do movimento do grupo de recursos. 

> [!IMPORTANT]
> Não inicie o Ponto Final de Streaming até que o movimento termine com sucesso.

### <a name="troubleshoot"></a>Resolução de problemas 

Se uma conta de Serviços de Media ou uma conta de Armazenamento Azure associada ficarem "desligadas" na sequência do movimento do grupo de recursos, tente rodar as teclas da Conta de Armazenamento. Se rodar as chaves da Conta de Armazenamento não resolver o estado "desligado" da conta de Serviços de Media, apresente um novo pedido de suporte do menu "Suporte + resolução de problemas" na conta de Serviços de Media.  

## <a name="next-steps"></a>Passos seguintes

[Criar uma conta](create-account-cli-quickstart.md)
