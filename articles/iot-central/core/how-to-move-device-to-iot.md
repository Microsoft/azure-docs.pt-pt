---
title: Como mover um dispositivo para a Azure IoT Central do IoT Hub
description: Como mover o dispositivo para a Azure IoT Central a partir do IoT Hub
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872736"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Como transferir um dispositivo para a Azure IoT Central do IoT Hub

*Este artigo aplica-se aos operadores e desenvolvedores de dispositivos.*  

Este artigo descreve como transferir um dispositivo para uma aplicação Azure IoT Central a partir de um Hub IoT. 

Um dispositivo liga-se primeiro a um ponto final DPS para recuperar a informação de que necessita para se ligar à sua aplicação. Internamente, a sua aplicação IoT Central utiliza um hub IoT para lidar com a conectividade do dispositivo.  

Um dispositivo pode ser ligado a um hub IoT diretamente utilizando uma cadeia de ligação ou utilizando DPS. [O serviço de provisionamento de dispositivos Azure IoT Hub (DPS)](../../iot-dps/about-iot-dps.md) é a rota para a IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Para mover o dispositivo para a Azure IoT Central

Para ligar um dispositivo à IoT Central a partir do IoT Hub, um dispositivo tem de ser atualizado com:

* O [ID de âmbito](../../iot-dps/concepts-service.md) da aplicação IoT Central.
* Uma chave derivada da chave [DO grupo SAS](concepts-get-connected.md) ou [do cert X.509](../../iot-hub/iot-hub-x509ca-overview.md)

Para interagir com a IoT Central, deve existir um modelo de dispositivo que modele as propriedades/telemetria/comandos que o dispositivo implementa. Para obter mais informações, consulte [Obter ligação à IoT Central](concepts-get-connected.md) e [quais são os modelos do dispositivo?](concepts-device-templates.md)

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Reveja algum código de amostra que mostre como usar fichas SAS em [Tutorial: Criar e ligar uma aplicação de cliente à sua aplicação Azure IoT Central](tutorial-connect-device.md)
- Saiba como [conectar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT](how-to-connect-devices-x509.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
- Saiba como definir um novo tipo de [dispositivo IoT na sua aplicação Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md)