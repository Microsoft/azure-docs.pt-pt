---
title: Gerar uma cadeia de ligação do dispositivo para o Azure IoT Central | Documentos da Microsoft
description: Como desenvolvedor de dispositivo, como posso gerar uma cadeia de ligação para o dispositivo que necessita para ligar a uma aplicação IoT Central?
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885647"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Gerar uma cadeia de ligação do dispositivo para ligar a uma aplicação do Azure IoT Central

Este artigo descreve como, como um desenvolvedor de dispositivo, para gerar uma cadeia de ligação para o dispositivo que necessita para ligar a uma aplicação IoT Central. O procedimento descrito neste artigo mostra-lhe como ligar-se rapidamente um único dispositivo com uma assinatura de acesso partilhado (SAS). Essa abordagem é útil quando está experimentando o Centro de IoT ou dispositivos de teste. Para obter abordagens alternativas para usar num ambiente de produção, consulte [conectividade do dispositivo no Azure IoT Central](concepts-connectivity.md).

## <a name="prerequisites"></a>Pré-requisitos

Para executar os passos descritos neste artigo é necessário o seguinte:

- Uma aplicação do Azure IoT Central. Para obter mais informações, veja [criar um início rápido da aplicação](quick-deploy-iot-central.md).
- Uma máquina de desenvolvimento com [node. js](https://nodejs.org/) versão 8.0.0 ou posterior instalado. Pode executar `node --version` na linha de comandos para verificar a sua versão. O Node.js está disponível para uma grande variedade de sistemas operativos.

## <a name="get-connection-information"></a>Obter informações da ligação

Os passos seguintes descrevem como obter as informações necessárias gerar uma cadeia de ligação SAS para um dispositivo:

1. Na **Device Explorer**, encontrar o dispositivo real que pretende ligar à sua aplicação:

    ![Selecione um dispositivo real](media/howto-generate-connection-string/real-devices.png)

1. Sobre o **dispositivo** página, selecione **Connect**:

    ![Selecione ligar](media/howto-generate-connection-string/connect.png)

1. Tome nota dos detalhes da ligação, **ID de âmbito**, **ID do dispositivo**, e **chave primária do dispositivo**, para utilizar nos passos seguintes:

    ![Detalhes da ligação](media/howto-generate-connection-string/device-connect.png)

    Pode copiar os valores a partir desta página para guardar.

## <a name="generate-the-connection-string"></a>Gerar a cadeia de ligação

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que tiver gerado uma cadeia de ligação para um dispositivo real ligar à sua aplicação do Azure IoT Central, aqui estão os passos sugeridos seguintes:

* [Preparar e ligar um dispositivo de DevKit (C)](howto-connect-devkit.md)
* [Preparar e ligue-se um Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Preparar e ligue-se um Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Preparar e ligar um dispositivo do Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs.md)