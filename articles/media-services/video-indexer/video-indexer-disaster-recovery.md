---
title: Video Indexer a continuidade dos negócios e a recuperação de desastres (BCDR) – Azure | Microsoft Docs
description: Saiba como fazer failover para uma conta de Video Indexer secundária se ocorrer uma interrupção ou falha de um datacenter regional.
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
ms.openlocfilehash: ce788b4640f0a6c6f25b3280ce4f52fd018d1699
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668307"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Manipule Video Indexer continuidade dos negócios e recuperação de desastres

Os serviços de mídia do Azure Video Indexer não fornecem failover instantâneo do serviço se houver uma interrupção ou falha de datacenter regional. Este artigo explica como configurar seu ambiente para um failover a fim de garantir a disponibilidade ideal para aplicativos e o tempo de recuperação minimizado se ocorrer um desastre.

Recomendamos que você configure a BCDR (recuperação de desastre de continuidade de negócios) em pares regionais para se beneficiar das políticas de isolamento e disponibilidade do Azure. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Pré-requisitos 

Uma subscrição do Azure. Se você ainda não tiver uma assinatura do Azure, Inscreva-se para a [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="failover-to-a-secondary-account"></a>Failover para uma conta secundária

Para implementar o BCDR, você precisa ter duas contas de Video Indexer para lidar com a redundância.

1. Crie duas contas de Video Indexer conectadas ao Azure (consulte [criar contas](connect-to-azure.md)). Uma para sua região primária e a outra para a região emparelhada do Azure. 
1. Se houver uma falha em sua região primária, alterne para a indexação usando a conta secundária.

> [!TIP]
> Você pode automatizar o BCDR Configurando alertas do log de atividades para notificações de integridade do serviço de acordo com a [criação de alertas do log de atividades em notificações de serviço](../../service-health/alerts-activity-log-service-notifications.md).

Para obter informações sobre como usar vários locatários, consulte [gerenciar vários locatários](manage-multiple-tenants.md). Para implementar o BCDR, escolha uma destas duas opções: [Video indexer conta por locatário](manage-multiple-tenants.md#video-indexer-account-per-tenant) ou [assinatura do Azure por locatário](manage-multiple-tenants.md#azure-subscription-per-tenant).

## <a name="next-steps"></a>Passos Seguintes

[Gerenciar uma conta de video indexer conectada ao Azure](manage-account-connected-to-azure.md).
