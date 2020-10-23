---
title: Utilize o portal Azure para configurar o upload de ficheiros Microsoft Docs
description: Como utilizar o portal Azure para configurar o seu hub IoT para permitir uploads de ficheiros a partir de dispositivos conectados. Inclui informações sobre a configuração da conta de armazenamento destino Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: 8d374ff372e0bc3fb183d9210f663987e4804114
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142520"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar os carregamentos de ficheiros do Hub IoT com o portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Upload de arquivo

Para utilizar a [funcionalidade de upload de ficheiros no IoT Hub,](iot-hub-devguide-file-upload.md)tem primeiro de associar uma conta de Armazenamento Azure ao seu hub. Selecione **o upload de ficheiros** para exibir uma lista de propriedades de upload de ficheiros para o hub IoT que está a ser modificado.

![Ver definições de upload de ficheiros IoT Hub no portal](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Recipiente de armazenamento**: Utilize o portal Azure para selecionar um recipiente de bolhas numa conta de Armazenamento Azure na sua subscrição atual do Azure para associar ao seu IoT Hub. Se necessário, pode criar uma conta de Armazenamento Azure na lâmina das contas de armazenamento e no recipiente de **bolhas** na lâmina do **Recipiente.** O IoT Hub gera automaticamente URIs SAS com permissões de escrita para este recipiente blob para os dispositivos utilizarem quando carregam ficheiros.

   ![Ver recipientes de armazenamento para upload de ficheiros no portal](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Receber notificações para ficheiros carregados**: Ative ou desative notificações de upload de ficheiros através do toggle.

* **SAS TTL**: Esta definição é o tempo de vida dos SAS URIs devolvidos ao dispositivo pelo IoT Hub. Configurar para uma hora por padrão, mas pode ser personalizado para outros valores usando o slider.

* **Definições de notificação de ficheiros predefinidoS TTL**: O tempo de vida de uma notificação de upload de ficheiros antes de expirar. Configurar para um dia por padrão, mas pode ser personalizado para outros valores usando o slider.

* **Contagem máxima de entrega**da notificação de ficheiros : O número de vezes que o IoT Hub tenta entregar uma notificação de upload de ficheiros. Configurar para 10 por padrão, mas pode ser personalizado para outros valores usando o slider.

   ![Configurar o upload de ficheiros IoT Hub no portal](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as capacidades de upload de ficheiros do IoT Hub, consulte [ficheiros upload de um dispositivo](iot-hub-devguide-file-upload.md) no guia de desenvolvedores do IoT Hub.

Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir dispositivos IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas IoT Hub](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/quickstart-linux.md)
* [Proteja a sua solução IoT do zero](../iot-fundamentals/iot-security-ground-up.md)