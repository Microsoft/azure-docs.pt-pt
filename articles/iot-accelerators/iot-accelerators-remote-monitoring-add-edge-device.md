---
title: Solução de monitorização remota adicionar dispositivo Edge - Azure / Microsoft Docs
description: Este artigo descreve como adicionar um dispositivo IoT Edge a um acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: 88d732cc3feaa2c9a528443d4a783227f3d8e641
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446794"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Adicione um dispositivo IoT Edge ao seu acelerador de solução de monitorização remota

Para adicionar um dispositivo [IoT Edge](../iot-edge/about-iot-edge.md) ao seu acelerador de solução, complete os seguintes dois passos:

1. Adicione o dispositivo Edge na página **Device Explorer** na UI do acelerador de solução de monitorização remota.
1. Instale o tempo de funcionaamento do IoT Edge no seu dispositivo Edge.

## <a name="add-the-iot-edge-device"></a>Adicione o dispositivo IoT Edge

Para adicionar um dispositivo IoT Edge ao acelerador de solução de monitorização remota, navegue na página **Device Explorer** na UI web e clique em + **novo dispositivo**.

No painel do **novo dispositivo,** escolha o **dispositivo IoT Edge**. Pode deixar os valores predefinidos para as outras definições. Em seguida, clique em **Aplicar**:

![Adicionar dispositivo IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Formas alternativas de adicionar um dispositivo IoT Edge

Também é possível registar um dispositivo IoT Edge diretamente com a instância IoT Hub no seu acelerador de solução. Precisa de saber o nome do hub IoT no seu acelerador de solução antes de seguir qualquer um destes guias:

- [Registar um novo dispositivo Azure IoT Edge a partir do portal Azure](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-portal%2Cwindows)
- [Registar um novo dispositivo Azure IoT Edge com Azure CLI](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-cli%2Cwindows)
- [Registar um novo dispositivo Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=visual-studio-code%2Cwindows)

Quando regista um dispositivo diretamente com o hub IoT no acelerador de solução de monitorização remota, está listado na página **device Explorer** na UI web.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

Antes de poder implantar módulos no seu dispositivo Edge, tem de instalar o tempo de funcionaamento do IoT Edge no dispositivo real. Os seguintes guias de como instalar o tempo de funcionaamento nas plataformas comuns do dispositivo:

- [Instale o tempo de execução Azure IoT Edge no Linux (x64)](../iot-edge/how-to-install-iot-edge.md)
- [Instale o tempo de execução da borda Azure IoT em Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge.md)
- [Instale o tempo de execução do Azure IoT Edge no Windows para utilizar com recipientes Windows](../iot-edge/how-to-install-iot-edge.md)
- [Instale o tempo de execução do Azure IoT Edge no Windows para utilizar com recipientes Linux](../iot-edge/how-to-install-iot-edge.md)
- [Instale o tempo de execução IoT Edge no Windows IoT Core](../iot-edge/how-to-install-iot-edge.md)

## <a name="next-steps"></a>Passos seguintes

Agora que preparou o seu dispositivo IoT Edge, o próximo passo é implantar módulos nele. Consulte [a Importação de um pacote IoT Edge no seu acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-import-edge-package.md)