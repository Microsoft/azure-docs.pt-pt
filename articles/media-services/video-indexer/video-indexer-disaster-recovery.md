---
title: Falha no Indexer de Vídeo e recuperação de desastres
titleSuffix: Azure Media Services
description: Aprenda a falhar numa conta secundária do Indexer de Vídeo se ocorrer uma falha regional do datacenter ou desastre.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499615"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Falha no Indexer de Vídeo e recuperação de desastres

O Azure Media Services Video Indexer não fornece falhas instantâneas do serviço se houver uma falha ou falha no datacenter regional. Este artigo explica como configurar o seu ambiente para uma falha para garantir a disponibilidade ideal para apps e tempo de recuperação minimizado se ocorrer um desastre.

Recomendamos que configure a recuperação de desastres de continuidade do negócio (BCDR) em pares regionais para beneficiar das políticas de isolamento e disponibilidade de Azure. Para mais informações, consulte [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição do Azure. Se ainda não tiver uma assinatura Azure, inscreva-se no [teste gratuito do Azure.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Falha numa conta secundária

Para implementar o BCDR, é necessário ter duas contas de Indexer de Vídeo para lidar com a redundância.

1. Crie duas contas de Indexer de Vídeo ligadas ao Azure (ver [Criar uma conta de Indexer](connect-to-azure.md)de Vídeo). Crie uma conta para a sua região primária e outra para a região azul emparelhada.
1. Se houver uma falha na sua região primária, mude para a indexação usando a conta secundária.

> [!TIP]
> Pode automatizar o BCDR através da criação de alertas de registo de atividade para notificações de saúde de serviço, de acordo com alertas de registo de [atividade sonantes da Create em notificações](../../service-health/alerts-activity-log-service-notifications.md)de serviço .

Para obter informações sobre o uso de vários inquilinos, consulte [Gerir vários inquilinos.](manage-multiple-tenants.md) Para implementar o BCDR, escolha uma destas duas opções: [conta Indexer](manage-multiple-tenants.md#video-indexer-account-per-tenant) de vídeo por inquilino ou [subscrição Azure por inquilino.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Passos seguintes

[Gerir uma conta de Indexer](manage-account-connected-to-azure.md)de Vídeo ligada ao Azure .
