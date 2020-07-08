---
title: Falha no Indexer de Vídeo e recuperação de desastres
titleSuffix: Azure Media Services
description: Saiba como falhar com uma conta de Indexer de Vídeo secundária se ocorrer uma falha ou desastre regional do datacenter.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79499615"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Falha no Indexer de Vídeo e recuperação de desastres

O Azure Media Services Video Indexer não fornece falha imediata do serviço se houver uma falha ou falha no datacenter regional. Este artigo explica como configurar o seu ambiente para uma falha para garantir a melhor disponibilidade para apps e tempo de recuperação minimizado em caso de desastre.

Recomendamos que configuure a recuperação de desastres de continuidade de negócios (BCDR) em pares regionais para beneficiar das políticas de isolamento e disponibilidade da Azure. Para mais informações, consulte [as regiões emparelhadas Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, inscreva-se para [o teste gratuito do Azure.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Falha numa conta secundária

Para implementar o BCDR, precisa de ter duas contas de Indexer de Vídeo para lidar com a redundância.

1. Criar duas contas de Indexer de Vídeo ligadas ao Azure (ver [Criar uma conta de Indexer de Vídeo).](connect-to-azure.md) Crie uma conta para a sua região primária e a outra para a região azul emparelhada.
1. Se houver uma falha na sua região primária, mude para a indexação usando a conta secundária.

> [!TIP]
> Pode automatizar o BCDR configurando alertas de registo de atividade para notificações de saúde de serviço de acordo com os [alertas de registo de atividades da Create em notificações de serviço](../../service-health/alerts-activity-log-service-notifications.md).

Para obter informações sobre a utilização de vários inquilinos, consulte [Gerir vários inquilinos.](manage-multiple-tenants.md) Para implementar o BCDR, escolha uma destas duas opções: [conta indexante](manage-multiple-tenants.md#video-indexer-account-per-tenant) de vídeo por inquilino ou [subscrição Azure por inquilino.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Próximos passos

[Gerir uma conta de Indexer de Vídeo ligada ao Azure](manage-account-connected-to-azure.md).
