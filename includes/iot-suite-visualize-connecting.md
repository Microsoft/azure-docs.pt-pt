---
title: incluir ficheiro
description: incluir ficheiro
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9b9e28f18208674609d0842b0e3a54e3fc661c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184693"
---
## <a name="view-device-telemetry"></a>Ver a telemetria do dispositivo

Pode ver a telemetria enviada do seu dispositivo na página **do Device Explorer** na solução.

1. Selecione o dispositivo que aprovisionou na lista de dispositivos na página do Explorer do **Dispositivo.** Um painel exibe informações sobre o seu dispositivo, incluindo um enredo da telemetria do dispositivo:

    ![Ver detalhes do dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Escolha **a Pressão** para alterar o visor de telemetria:

    ![Ver telemetria de pressão](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para ver informações de diagnóstico sobre o seu dispositivo, desloque-se para **Diagnósticos:**

    ![Ver diagnósticos do dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Aja no seu dispositivo

Para invocar métodos nos seus dispositivos, utilize a página do Explorer do **Dispositivo** na solução de Monitorização Remota. Por exemplo, na solução de monitorização remota os dispositivos **Chiller** implementam um método **De reboot.**

1. Escolha **dispositivos** para navegar na página **do Explorer** do Dispositivo na solução.

1. Selecione o dispositivo que aprovisionou na lista de dispositivos na página do Explorer do **Dispositivo:**

    ![Selecione o seu verdadeiro dispositivo](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para apresentar uma lista dos métodos que pode recorrer ao seu dispositivo, escolha **Jobs**, em seguida **Métodos**. Para agendar uma função para executar em vários dispositivos, pode selecionar vários dispositivos da lista. O painel **Jobs** mostra os tipos de método sumido a todos os dispositivos selecionados.

1. Escolha **reiniciar,** detete o nome de trabalho para **RebootPhysicalChiller** e, em seguida, escolha **Aplicar:**

    ![Agende a atualização do firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Uma sequência de mensagens mostra na consola que executa o código do dispositivo enquanto o dispositivo simulado manuseia o método.

> [!NOTE]
> Para acompanhar o estado do trabalho na solução, escolha **Ver Estatuto de Emprego**.

## <a name="next-steps"></a>Passos seguintes

O artigo [Personalize o acelerador de soluções de monitorização remota](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) descreve algumas formas de personalizar o acelerador de solução.