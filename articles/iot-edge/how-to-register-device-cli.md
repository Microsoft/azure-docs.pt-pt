---
title: Registar um dispositivo novo na linha de comando - Azure IoT Edge | Documentos da Microsoft
description: Utilizar a extensão de IoT para a CLI do Azure para registar um novo dispositivo IoT Edge e obter a cadeia de ligação
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: de00c317483da9bcd93bb2e2505350d787385925
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495383"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli"></a>Registar um novo dispositivo Azure IoT Edge com o CLI do Azure

Antes de poder utilizar os seus dispositivos IoT com o Azure IoT Edge, tem de registá-los com o seu hub IoT. Depois de se registar um dispositivo, receberá uma cadeia de ligação que pode ser utilizada para configurar o dispositivo para cargas de trabalho de IoT Edge.

[CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma código-fonte aberto para várias plataforma ferramenta da linha de comandos para gerir recursos do Azure, como o IoT Edge. Permite-lhe gerir recursos, instâncias de serviço aprovisionamento de dispositivos e hubs ligados a caixa do IoT Hub do Azure. A nova extensão de IoT otimiza a CLI do Azure com funcionalidades como a gestão de dispositivos e a capacidade total do IoT Edge.

Este artigo mostra como registar um novo dispositivo IoT Edge com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [IoT hub](../iot-hub/iot-hub-create-using-cli.md) na sua subscrição do Azure.
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –-version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack.
* O [extensão de IoT para a CLI do Azure](https://github.com/Azure/azure-iot-cli-extension).

## <a name="create-a-device"></a>Criar um dispositivo

Utilize o [az iot hub-identidade de dispositivo criar](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) comando para criar uma nova identidade de dispositivo do seu hub IoT. Por exemplo:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Este comando inclui três parâmetros:

* **device-id**: Forneça um nome descritivo exclusivo ao seu hub IoT.

* **hub-name**: Forneça o nome do IoT hub.

* **edge-enabled**: Este parâmetro declara que o dispositivo é para utilização com o IoT Edge.

   ![saída de criar AZ iot hub-identidade de dispositivo](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>Ver todos os dispositivos

Utilize o [lista de identidade de dispositivo do az iot hub](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) comando para ver todos os dispositivos no IoT hub. Por exemplo:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Qualquer dispositivo que está registado como um dispositivo IoT Edge irá ter a propriedade **capabilities.iotEdge** definida como **verdadeiro**.

## <a name="retrieve-the-connection-string"></a>Obter a cadeia de ligação

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub. Utilize o [identidade de dispositivo de hub iot com az-show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) comando para devolver a cadeia de ligação para um único dispositivo:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

O valor para o `device-id` parâmetro diferencia maiúsculas de minúsculas. Não copia as aspas à volta a cadeia de ligação.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [implementar módulos num dispositivo com a CLI do Azure](how-to-deploy-modules-cli.md).
