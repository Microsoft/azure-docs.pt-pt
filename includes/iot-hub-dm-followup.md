---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "69558746"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e alargar as ações de gestão de dispositivos

As suas soluções IoT podem expandir o conjunto definido de padrões de gestão do dispositivo ou permitir padrões personalizados utilizando os primitivos do método twin e nuvem-dispositivo do dispositivo. Outros exemplos de ações de gestão de dispositivos incluem reset de fábrica, atualização de firmware, atualização de software, gestão de energia, gestão de rede e conectividade, e encriptação de dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção de dispositivos

Normalmente, configura dispositivos para executar ações de uma vez que minimiza interrupções e tempo de inatividade. As janelas de manutenção do dispositivo são um padrão comumente utilizado para definir o momento em que um dispositivo deve atualizar a sua configuração. As suas soluções back-end podem utilizar as propriedades desejadas do twin do dispositivo para definir e ativar uma política no seu dispositivo que permita uma janela de manutenção. Quando um dispositivo recebe a política da janela de manutenção, pode utilizar a propriedade reportada do dispositivo twin para reportar o estado da apólice. A aplicação back-end pode então usar consultas gémeas do dispositivo para atestar a conformidade dos dispositivos e de cada política.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, utilizou um método direto para desencadear um reboot remoto num dispositivo. Utilizou as propriedades relatadas para relatar a última vez de reinicialização do dispositivo, e questionou o dispositivo twin para descobrir a última hora de reinicialização do dispositivo a partir da nuvem.

Para continuar a trabalhar com o IoT Hub e padrões de gestão de dispositivos como o controlo remoto sobre a atualização do firmware aéreo, consulte [Como fazer uma atualização de firmware](../articles/iot-hub/tutorial-firmware-update.md).

Para aprender a estender a sua solução IoT e programar chamadas em vários dispositivos, consulte [Agenda e trabalhos de transmissão](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).