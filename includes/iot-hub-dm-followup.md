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
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558746"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e estender as ações de gerenciamento de dispositivo

Suas soluções de IoT podem expandir o conjunto definido de padrões de gerenciamento de dispositivos ou habilitar padrões personalizados usando os primitivos do método "e da nuvem para o dispositivo". Outros exemplos de ações de gerenciamento de dispositivos incluem redefinição de fábrica, atualização de firmware, atualização de software, gerenciamento de energia, gerenciamento de rede e conectividade e criptografia de dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo

Normalmente, você configura dispositivos para executar ações de cada vez, o que minimiza as interrupções e o tempo de inatividade. As janelas de manutenção do dispositivo são um padrão comumente usado para definir a hora em que um dispositivo deve atualizar sua configuração. Suas soluções de back-end podem usar as propriedades desejadas do dispositivo de cópia para definir e ativar uma política no seu dispositivo que habilita uma janela de manutenção. Quando um dispositivo recebe a política de janela de manutenção, ele pode usar a propriedade relatada do dispositivo "r" para relatar o status da política. O aplicativo de back-end pode usar consultas de dispositivo de backup para atestar a conformidade dos dispositivos e de cada política.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você usou um método direto para disparar uma reinicialização remota em um dispositivo. Você usou as propriedades relatadas para reportar a hora da última reinicialização do dispositivo e consultou o dispositivo "to" para descobrir a hora da última reinicialização do dispositivo da nuvem.

Para continuar a introdução aos padrões do Hub IoT e do gerenciamento de dispositivos, como remoto pela atualização do firmware do ar, consulte [como fazer uma atualização de firmware](../articles/iot-hub/tutorial-firmware-update.md).

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, consulte [agendar e difundir trabalhos](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).