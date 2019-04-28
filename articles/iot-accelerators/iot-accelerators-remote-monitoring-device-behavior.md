---
title: Simulated o comportamento do dispositivo na solução de monitorização remota - Azure | Documentos da Microsoft
description: Este artigo descreve como utilizar JavaScript para definir o comportamento de um dispositivo simulado na solução de monitorização remota.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c2151a4b1eb2a853ed343f6720b4f53af5e5e449
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446362"
---
# <a name="implement-the-device-model-behavior"></a>Implementar o comportamento de modelo do dispositivo

O artigo [compreender o esquema do modelo de dispositivo](iot-accelerators-remote-monitoring-device-schema.md) descreve o esquema que define um modelo de dispositivo simulado. Esse artigo chamado de dois tipos de arquivo JavaScript que implementam o comportamento de um dispositivo simulado:

- **Estado** JavaScript ficheiros que são executados em intervalos fixos para atualizar o estado interno do dispositivo.
- **Método** JavaScript ficheiros que são executados quando a solução invoca um método no dispositivo.

> [!NOTE]
> Comportamentos de modelo do dispositivo são apenas para dispositivos simulados alojados no serviço de simulação do dispositivo. Se quiser criar um dispositivo real, veja [ligar o seu dispositivo para o acelerador de solução de monitorização remota](iot-accelerators-connecting-devices.md).

Neste artigo, vai aprender a:

>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de monitorização remota
> * Os scripts de depuração

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>Passos Seguintes

Este artigo descreveu como definir o comportamento do seu próprio modelo personalizado de dispositivo simulado. Este artigo mostrou como para:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Controlar o estado de um dispositivo simulado
> * Definir como um dispositivo simulado responde a uma chamada de método da solução de monitorização remota
> * Os scripts de depuração

Agora que aprendeu como especificar o comportamento de um dispositivo simulado, o passo seguinte sugerido é saber como [criar um dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

Para obter mais informações para desenvolvedores sobre a solução de monitorização remota, consulte:

* [Guia de Referência para Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Resolução de Problemas de Programadores](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
