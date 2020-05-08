---
title: Monitorize o seu dispositivo Azure Stack Edge [ Microsoft Docs
description: Descreve como usar o portal Azure e a Web UI local para monitorizar o seu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569436"
---
# <a name="monitor-your-azure-stack-edge"></a>Monitorize o seu Edge Azure Stack

Este artigo descreve como monitorizar o seu Azure Stack Edge. Para monitorizar o seu dispositivo, pode utilizar o portal Azure ou a UI web local. Utilize o portal Azure para visualizar eventos de dispositivos, configurar e gerir alertas e ver métricas. Utilize a UI web local no seu dispositivo físico para visualizar o estado do hardware dos vários componentes do dispositivo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Ver eventos de dispositivos e os alertas correspondentes
> * Ver o estado do hardware dos componentes do dispositivo
> * Ver métricas de capacidade e transação para o seu dispositivo
> * Configurar e gerir alertas

## <a name="view-device-events"></a>Ver eventos de dispositivos

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Ver estado de hardware

Tome os seguintes passos na UI web local para ver o estado do hardware dos componentes do seu dispositivo.

1. Ligue-se à Web UI local do seu dispositivo.
2. Vá para o **estado de hardware de manutenção >**. Pode ver a saúde dos vários componentes do dispositivo. 

    ![Ver estado de hardware](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Ver métricas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Gerir alertas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Passos seguintes 

Saiba como [Gerir a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).