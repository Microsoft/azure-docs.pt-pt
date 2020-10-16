---
title: Ativação pós-falha e recuperação após desastre do Video Indexer
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
ms.openlocfilehash: eab376c44065979de86e5c70b796be952fccffaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065416"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Ativação pós-falha e recuperação após desastre do Video Indexer

O Video Indexer dos Serviços de Multimédia do Azure não disponibilizará a ativação pós-falha instantânea do serviço se houver uma indisponibilidade ou falha no datacenter regional. Este artigo explica como configurar o seu ambiente para uma falha para garantir a melhor disponibilidade para apps e tempo de recuperação minimizado em caso de desastre.

Recomendamos que configure a continuidade de negócio e recuperação após desastre (BCDR) em pares regionais para beneficiar das políticas de isolamento e disponibilidade do Azure. Para mais informações, consulte [as regiões emparelhadas Azure.](../../best-practices-availability-paired-regions.md)

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, inscreva-se para [o teste gratuito do Azure.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Falha numa conta secundária

Para implementar o BCDR, precisa de ter duas contas de Indexer de Vídeo para lidar com a redundância.

1. Criar duas contas de Indexer de Vídeo ligadas ao Azure (ver [Criar uma conta de Indexer de Vídeo).](connect-to-azure.md) Crie uma conta para a sua região primária e a outra para a região azul emparelhada.
1. Se houver uma falha na sua região primária, mude para a indexação usando a conta secundária.

> [!TIP]
> Pode automatizar o BCDR configurando alertas de registo de atividade para notificações de saúde de serviço de acordo com os [alertas de registo de atividades da Create em notificações de serviço](../../service-health/alerts-activity-log-service-notifications-portal.md).

Para obter informações sobre a utilização de vários inquilinos, consulte [Gerir vários inquilinos.](manage-multiple-tenants.md) Para implementar o BCDR, escolha uma destas duas opções: [conta indexante](manage-multiple-tenants.md#video-indexer-account-per-tenant) de vídeo por inquilino ou [subscrição Azure por inquilino.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Passos seguintes

[Gerir uma conta de Indexer de Vídeo ligada ao Azure](manage-account-connected-to-azure.md).
