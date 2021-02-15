---
title: Gerir o acesso à rede pública para o hub Azure IoT
description: Documentação sobre como desativar e permitir o acesso à rede pública para o hub IoT
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: 27552b2c39f2d1e5d9cc1719a9cc2944e088773a
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517132"
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

Para ligar o acesso à rede pública, selecionou **todas as redes,** em seguida, **Guardar**.

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Ponto final do IoT Hub, endereço IP e portas após desativar o acesso à rede pública

O IoT Hub é um Multi-inquilino Plataforma-as-A-Service (PaaS), pelo que diferentes clientes partilham o mesmo conjunto de recursos de hardware de computação, networking e armazenamento. Os nomes de anfitriões do IoT Hub mapeiam para um ponto final público com um endereço IP publicamente roteado através da internet. Diferentes clientes partilham este ponto final público do IoT Hub, e os dispositivos IoT em redes de ampla área e redes no local podem aceder-lhe. 

A desativação do acesso à rede pública é aplicada num recurso específico do hub IoT, garantindo o isolamento. Para manter o serviço ativo para outros recursos do cliente utilizando o caminho público, o seu ponto final público permanece resolúvel, endereços IP detetáveis e portas permanecem abertas. Isto não é motivo de preocupação, uma vez que a Microsoft integra várias camadas de segurança para garantir um isolamento total entre os inquilinos. Para saber mais, consulte [o Isolamento na Nuvem Pública do Azure.](../security/fundamentals/isolation-choices.md#tenant-level-isolation)

## <a name="ip-filter"></a>Filtro IP 

Se o acesso à rede pública for desativado, todas as regras [do filtro IP](iot-hub-ip-filtering.md) são ignoradas. Isto porque todos os IPs da internet pública estão bloqueados. Para utilizar o filtro IP, utilize a opção **de gamas IP selecionada.**

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Correção de bug com ponto final compatível com o Event Hub incorporado

Existe um bug com o IoT Hub onde o [ponto final compatível com](iot-hub-devguide-messages-read-builtin.md) o Event Hub continua a ser acessível através da internet pública quando o acesso da rede pública ao IoT Hub é desativado. Para saber mais e contacte-nos sobre este bug, consulte [o acesso desativando a rede pública para ioT Hub desativar o acesso ao ponto final do Event Hub incorporado.](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)
