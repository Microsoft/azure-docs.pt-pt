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
ms.openlocfilehash: 69fdc6cf678107ef64ea1fe7b819738fd4a4ff4f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56740845"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e estender o dispositivo ações de gestão

Suas soluções de IoT podem expandir o conjunto definido de padrões de gestão de dispositivos ou ativar padrões personalizados com o dispositivo duplo e primitivos de método de cloud-para-dispositivo. Outros exemplos de ações de gestão do dispositivo incluem a reposição de fábrica, atualização de firmware, atualização de software, gerenciamento de energia, gestão de rede e a conectividade e encriptação de dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo

Normalmente, pode configurar dispositivos para realizar ações ao mesmo tempo que minimiza as interrupções e tempo de inatividade. Janelas de manutenção do dispositivo são um padrão usado para definir a hora quando um dispositivo deve atualizar a respetiva configuração. Suas soluções de back-end podem utilizar as propriedades pretendidas do dispositivo duplo para definir e ativar uma política no seu dispositivo, que permite uma janela de manutenção. Quando um dispositivo recebe a política de janela de manutenção, pode utilizar a propriedade comunicada do dispositivo duplo para comunicar o estado da política. A aplicação de back-end, em seguida, pode utilizar consultas twin do dispositivo para confirmar a conformidade de dispositivos e de cada política.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, utilizou um método direto para acionar a reinicialização remota num dispositivo. Utilizou as propriedades reportadas para reportar a última vez que o reinício do dispositivo e consultado o dispositivo duplo para detetar a última vez que o reinício do dispositivo a partir da cloud.

Para continuar a introdução ao IoT Hub e padrões de gestão de dispositivos como remota sobre a atualização de firmware do ar, veja [como fazer uma atualização de firmware](../articles/iot-hub/tutorial-firmware-update.md)

Para saber como expandir o seu IoT chama o método de solução e a agenda em vários dispositivos, veja [agendar e transmitir tarefas](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).