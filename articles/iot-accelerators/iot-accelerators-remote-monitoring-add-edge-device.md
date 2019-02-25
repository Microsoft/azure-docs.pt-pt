---
title: Solução de monitorização remota adicionar dispositivo do Edge - Azure | Documentos da Microsoft
description: Este artigo descreve como adicionar um dispositivo IoT Edge para um acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749550"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Adicionar um dispositivo IoT Edge para seu acelerador de solução de monitorização remota

Para adicionar um [IoT Edge](../iot-edge/about-iot-edge.md) dispositivo do solution accelerator, conclua os seguintes dois passos:

1. Adicionar o dispositivo de limite de **Device Explorer** página na monitorização remota solution accelerator da IU da web.
1. Instale o runtime do IoT Edge no seu dispositivo do Edge.

## <a name="add-the-iot-edge-device"></a>Adicionar dispositivo IoT Edge

Para adicionar um dispositivo IoT Edge para o acelerador de solução de monitorização remota, navegue para o **Device Explorer** página na IU da web e clique em **+ novo dispositivo**.

Na **novo dispositivo** painel, escolha **dispositivo IoT Edge**. Pode deixar os valores predefinidos para as outras definições. Em seguida, clique em **Aplicar**:

![Adicionar dispositivo IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Maneiras alternativas de adicionar um dispositivo IoT Edge

Também é possível registar um dispositivo IoT Edge diretamente com a instância do IoT Hub do solution accelerator. Precisa saber o nome do hub IoT do solution accelerator, antes de seguir qualquer um destes guias de procedimentos:

- [Registar um novo dispositivo Azure IoT Edge do portal do Azure](../iot-edge/how-to-register-device-portal.md)
- [Registar um novo dispositivo Azure IoT Edge com o CLI do Azure](../iot-edge/how-to-register-device-cli.md)
- [Registar um novo dispositivo Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-register-device-vscode.md)

Quando registar um dispositivo diretamente com o hub IoT no acelerador de solução de monitorização remota, é apresentada no **Device Explorer** página na IU da web.

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

Antes de poder implementar módulos para o seu dispositivo do Edge, tem de instalar o runtime do IoT Edge no dispositivo real. Os guias de procedimentos seguintes mostram-lhe como instalar o tempo de execução em plataformas de dispositivos comuns:

- [Instalar o runtime do Azure IoT Edge no Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Instalar o runtime do Azure IoT Edge no Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Instalar o runtime do Azure IoT Edge no Windows para utilização com contentores do Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Instalar o runtime do Azure IoT Edge no Windows para utilização com contentores do Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Instalar o runtime do IoT Edge no Windows IoT Core](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Passos Seguintes

Agora que preparou o seu dispositivo IoT Edge, a próxima etapa é implementar módulos nele. Consulte [importar um pacote do IoT Edge para o seu acelerador de solução de monitorização remota](iot-accelerators-remote-monitoring-import-edge-package.md)
