---
title: Gerir o acesso à rede pública para o hub Azure IoT
description: Documentação sobre como desativar e permitir o acesso à rede pública para o hub IoT
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937563"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gerir o acesso à rede pública para o seu hub IoT

Para restringir o acesso apenas ao [seu hub IoT no seu VNet,](virtual-network-support.md)desative o acesso à rede pública. Para tal, utilize o portal Azure ou a `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Desligue o acesso à rede pública através do portal Azure

1. Visite [o portal Azure](https://portal.azure.com)
2. Navegue até ao seu hub IoT.
3. Selecione **Networking** a partir do menu do lado esquerdo.
4. Em "Permitir o acesso à rede pública", **selecione Disabled**
5. Selecione **Guardar**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Imagem mostrando portal Azure onde desligar o acesso à rede pública" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Para ativar o acesso à rede pública, selecionado **Ativado,** em seguida, **Guardar**.

## <a name="ip-filter"></a>Filtro IP 

Se o acesso à rede pública for desativado, todas as regras [do filtro IP](iot-hub-ip-filtering.md) são ignoradas. Isto porque todos os IPs da internet pública estão bloqueados. Para utilizar o filtro IP, utilize a opção **de gamas IP selecionada.**

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Correção de bug com ponto final compatível com o Event Hub incorporado

Existe um bug com o IoT Hub onde o [ponto final compatível com](iot-hub-devguide-messages-read-builtin.md) o Event Hub continua a ser acessível através da internet pública quando o acesso da rede pública ao IoT Hub é desativado. Para saber mais e contacte-nos sobre este bug, consulte [o acesso desativando a rede pública para ioT Hub desativar o acesso ao ponto final do Event Hub incorporado.](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)