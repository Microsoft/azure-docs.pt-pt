---
title: Utilizar o portal do Azure para configurar o carregamento de ficheiros | Documentos da Microsoft
description: Como utilizar o portal do Azure para configurar o hub IoT para permitir carregamentos de ficheiros a partir dos dispositivos ligados. Inclui informações sobre como configurar o conta de armazenamento do Azure de destino.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robin.shahan
ms.openlocfilehash: 04f3f05c8c18a3356c282144cda62d1f980362fe
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008446"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar o IoT Hub, carregamentos de ficheiros com o portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Carregamento de ficheiros

Para utilizar o [funcionalidade de carregamento de ficheiros no IoT Hub](iot-hub-devguide-file-upload.md), primeiro tem de associar uma conta de armazenamento do Azure com o seu hub. Selecione **carregamento de ficheiros** para apresentar uma lista de propriedades de carregamento de ficheiro para o hub de IoT que está a ser modificado.

![Definições no portal de carregamento de ficheiros do IoT Hub de vista](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Contentor de armazenamento**: Utilize o portal do Azure para selecionar um contentor de BLOBs numa conta de armazenamento do Azure na sua subscrição do Azure atual para associar o seu IoT Hub. Se necessário, pode criar uma conta de armazenamento do Azure no **contas de armazenamento** contentor de BLOBs e do painel no **contentores** painel. IoT Hub gera automaticamente os URIs de SAS com permissões de escrita para este contentor de BLOBs para os dispositivos a utilizar quando eles carregam ficheiros.

   ![Ver os contentores de armazenamento para o carregamento de ficheiros no portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receber notificações sobre os ficheiros carregados**: Ativar ou desativar notificações de carregamento do ficheiro através do botão de alternar.

* **SAS TTL**: Esta definição é o tempo de vida dos URIs de SAS retornado para o dispositivo ao IoT Hub. Definido como uma hora por padrão, mas pode ser personalizado para outros valores no controlo de deslize.

* **TTL de padrão de definições de notificação de ficheiros**: O tempo de vida de um ficheiro a carregar notificação antes de expirar. Definido como um dia por padrão, mas pode ser personalizado para outros valores no controlo de deslize.

* **Contagem máxima de entrega de notificação de ficheiros**: O número de vezes que o IoT Hub tentará entregar um arquivo carregar notificação. Definido como 10, por predefinição, mas pode ser personalizado para outros valores no controlo de deslize.

   ![Configurar o carregamento de ficheiros do IoT Hub no portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as capacidades de carregamento de arquivo do IoT Hub, veja [carregar ficheiros a partir de um dispositivo](iot-hub-devguide-file-upload.md) no Guia do programador do IoT Hub.

Siga estas ligações para saber mais sobre como gerir o IoT Hub do Azure:

* [Gerir dispositivos IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteger a sua solução de IoT desde o backup](../iot-fundamentals/iot-security-ground-up.md)
