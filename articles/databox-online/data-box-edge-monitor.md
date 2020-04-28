---
title: Monitorize o seu dispositivo Azure Data Box Edge [ Microsoft Docs
description: Descreve como usar o portal Azure e a UI web local para monitorizar o seu Edge de Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756755"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorize a sua borda de caixa de dados Azure

Este artigo descreve como monitorizar o seu Limite de Caixa de Dados Azure. Para monitorizar o seu dispositivo, pode utilizar o portal Azure ou a UI web local. Utilize o portal Azure para visualizar eventos de dispositivos, configurar e gerir alertas e ver métricas. Utilize a UI web local no seu dispositivo físico para visualizar o estado do hardware dos vários componentes do dispositivo.

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

    ![Ver estado de hardware](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Ver métricas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Gerir alertas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Passos seguintes 

Saiba como [Gerir a largura de banda](data-box-edge-manage-bandwidth-schedules.md).