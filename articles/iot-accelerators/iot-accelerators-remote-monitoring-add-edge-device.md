---
title: Solução de Monitorização Remota adicionar dispositivo Edge - Azure [ Microsoft Docs
description: Este artigo descreve como adicionar um dispositivo IoT Edge a um acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 0a42763ff47cccfa506acbbbd95d20d41eb0827f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72965377"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Adicione um dispositivo IoT Edge ao seu acelerador de solução de monitorização remota

Para adicionar um dispositivo [IoT Edge](../iot-edge/about-iot-edge.md) ao seu acelerador de solução, complete os seguintes dois passos:

1. Adicione o dispositivo Edge na página **do Device Explorer** no uI do acelerador de solução de monitorização remota.
1. Instale o tempo de execução do IoT Edge no seu dispositivo Edge.

## <a name="add-the-iot-edge-device"></a>Adicione o dispositivo IoT Edge

Para adicionar um dispositivo IoT Edge ao acelerador de solução de monitorização remota, navegue na página do **Device Explorer** na Web UI e clique + **novo dispositivo**.

No painel **do novo dispositivo,** escolha o **dispositivo IoT Edge**. Pode deixar os valores predefinidos para as outras definições. Em seguida, clique em **Aplicar**:

![Adicione o dispositivo IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Formas alternativas de adicionar um dispositivo IoT Edge

Também é possível registar um dispositivo IoT Edge diretamente com a instância IoT Hub no seu acelerador de soluções. Precisa de saber o nome do hub IoT no seu acelerador de soluções antes de seguir qualquer um destes guias:

- [Registe um novo dispositivo Azure IoT Edge do portal Azure](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [Registe um novo dispositivo Azure IoT Edge com o Azure CLI](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [Registe um novo dispositivo Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

Quando regista um dispositivo diretamente com o hub IoT no acelerador de soluções de monitorização remota, está listado na página do **Device Explorer** na UI web.

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução do IoT Edge

Antes de poder implantar módulos no seu dispositivo Edge, tem de instalar o tempo de funcionar do IoT Edge no dispositivo real. Os seguintes guias mostram-lhe como instalar o tempo de funcionação em plataformas comuns do dispositivo:

- [Instale o tempo de execução do Azure IoT Edge em Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instale o tempo de execução do Edge Azure IoT em Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Instale o tempo de execução do Azure IoT Edge no Windows para utilizar com contentores windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Instale o tempo de execução do Azure IoT Edge no Windows para utilizar com recipientes Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Instale o tempo de execução do IoT Edge no Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Passos seguintes

Agora que preparou o seu dispositivo IoT Edge, o próximo passo é implementar módulos para o mesmo. Consulte [importar um pacote IoT Edge no seu acelerador](iot-accelerators-remote-monitoring-import-edge-package.md) de solução de monitorização remota
