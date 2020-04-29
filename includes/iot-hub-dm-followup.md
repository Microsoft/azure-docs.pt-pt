---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "69558746"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personalize e estenda as ações de gestão do dispositivo

As suas soluções IoT podem expandir o conjunto definido de padrões de gestão de dispositivos ou ativar padrões personalizados utilizando os primitivos do método twin e cloud-to-device. Outros exemplos de ações de gestão de dispositivos incluem reset de fábrica, atualização de firmware, atualização de software, gestão de energia, gestão de rede e conectividade, e encriptação de dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo

Normalmente, configura os dispositivos para realizar ações de cada vez que minimiza as interrupções e o tempo de inatividade. As janelas de manutenção do dispositivo são um padrão comumente utilizado para definir o tempo em que um dispositivo deve atualizar a sua configuração. As suas soluções traseiras podem utilizar as propriedades desejadas do dispositivo twin para definir e ativar uma política no seu dispositivo que permite uma janela de manutenção. Quando um dispositivo recebe a política da janela de manutenção, pode utilizar a propriedade reportada do dispositivo twin para reportar o estado da apólice. A aplicação back-end pode então usar consultas gémeas do dispositivo para atestar a conformidade dos dispositivos e cada política.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, usou um método direto para desencadear um reboot remoto num dispositivo. Usou as propriedades reportadas para relatar o último tempo de reinício do dispositivo, e questionou o dispositivo twin para descobrir o último tempo de reinício do dispositivo a partir da nuvem.

Para continuar a começar com o IoT Hub e padrões de gestão de dispositivos, como remotamente sobre a atualização do firmware de ar, consulte [como fazer uma atualização](../articles/iot-hub/tutorial-firmware-update.md)de firmware .

Para aprender a alargar a sua solução IoT e agendar o método de apela a vários dispositivos, consulte [agendar e transmitir trabalhos.](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)