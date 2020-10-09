---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184693"
---
## <a name="view-device-telemetry"></a>Ver a telemetria do dispositivo

Pode ver a telemetria enviada do seu dispositivo na página **Device Explorer** na solução.

1. Selecione o dispositivo que forvisionou na lista de dispositivos na página **Device Explorer.** Um painel apresenta informações sobre o seu dispositivo, incluindo um enredo da telemetria do dispositivo:

    ![Ver detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha a **pressão** para alterar o visor da telemetria:

    ![Ver telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para visualizar informações de diagnóstico sobre o seu dispositivo, desloque-se até **ao Diagnóstico:**

    ![Ver diagnósticos do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agir no seu dispositivo

Para invocar métodos nos seus dispositivos, utilize a página Explorer do **Dispositivo** na solução de Monitorização Remota. Por exemplo, na solução de monitorização remota, os dispositivos **Chiller** implementam um método **reboot.**

1. Escolha **dispositivos** para navegar na página **device Explorer** na solução.

1. Selecione o dispositivo que alistou na lista de dispositivos na página **Device Explorer:**

    ![Selecione o seu verdadeiro dispositivo](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para apresentar uma lista dos métodos que pode recorrer no seu dispositivo, escolha **Jobs,** em seguida, **Métodos**. Para agendar um trabalho para executar em vários dispositivos, pode selecionar vários dispositivos na lista. O painel **Jobs** mostra os tipos de método comum a todos os dispositivos selecionados.

1. Escolha **o Reboot,** despe um pouco o nome de trabalho para **RebootPhysicalChiller** e, em seguida, escolha **Aplicar**:

    ![Agende a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens aparece na consola a executar o código do dispositivo enquanto o dispositivo simulado trata do método.

> [!NOTE]
> Para acompanhar o estado do trabalho na solução, escolha **Ver Situação do Trabalho**.

## <a name="next-steps"></a>Passos seguintes

O artigo [Personalize o acelerador de solução de monitorização remota](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descreve algumas formas de personalizar o acelerador de solução.