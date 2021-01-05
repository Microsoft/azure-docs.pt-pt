---
title: Faça upload de ficheiros de dispositivos para armazenamento Azure / Microsoft Docs
description: Como configurar os uploads de ficheiros dos seus dispositivos para a nuvem. Depois de configurar os uploads de ficheiros, implemente uploads de ficheiros nos seus dispositivos.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: ed2eff4d6ccbb9f85dfaf7049fa3cc18711bae0f
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796912"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>Faça upload de ficheiros dos seus dispositivos para a nuvem

*Este tópico aplica-se a administradores e desenvolvedores de dispositivos.*

O IoT Central permite-lhe fazer o upload de meios de comunicação e outros ficheiros de dispositivos conectados para armazenamento em nuvem. Configura a capacidade de upload de ficheiros na sua aplicação IoT Central e, em seguida, implementa uploads de ficheiros no código do seu dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Deve ser administrador na sua aplicação IoT Central para configurar uploads de ficheiros.

Precisa de uma conta de armazenamento Escaure e de um contentor para armazenar os ficheiros carregados. Se não tiver uma conta de armazenamento e um recipiente existentes para utilizar, crie uma [nova conta de armazenamento no portal Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="configure-device-file-uploads"></a>Configurar uploads de ficheiros de dispositivos

Para configurar os uploads de ficheiros do dispositivo:

1. Navegue para a secção **Administração** na sua aplicação.

1. Selecione **o upload do ficheiro do dispositivo**.

1. Selecione a conta de armazenamento e o recipiente para utilizar. Se a conta de armazenamento estiver numa subscrição Azure diferente da sua aplicação, introduza uma cadeia de ligação de conta de armazenamento.

1. Se necessário, ajuste o tempo limite de carregamento que define quanto tempo um pedido de upload permanece válido. Os valores válidos são de 1 a 24 horas.

1. Selecione **Guardar**. Quando o estado aparecer **Configurado,** está pronto para carregar ficheiros a partir de dispositivos.

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="Configurar o upload de ficheiros na aplicação":::

## <a name="disable-device-file-uploads"></a>Desativar os uploads de ficheiros de dispositivos

Se pretender desativar os uploads de ficheiros de dispositivos para a sua aplicação IoT Central:

1. Navegue para a secção **Administração** na sua aplicação.

1. Selecione **o upload do ficheiro do dispositivo**.

1. Selecione **Eliminar**.

## <a name="upload-a-file-from-a-device"></a>Faça upload de um ficheiro a partir de um dispositivo

O IoT Central utiliza a capacidade de upload de ficheiros do IoT Hub para permitir que os dispositivos carreguem ficheiros. Para obter um código de amostra que lhe mostre como fazer o upload de ficheiros a partir de um dispositivo, consulte a amostra do [dispositivo de upload de ficheiros IoT Central](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/).

## <a name="next-steps"></a>Passos seguintes

Agora que sabe configurar e implementar uploads de ficheiros de dispositivos na IoT Central, um próximo passo sugerido é aprender mais uploads de ficheiros de dispositivos:

- [Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [Faça upload de ficheiros do seu dispositivo para a nuvem com IoT Hub (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)