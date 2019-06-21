---
title: incluir ficheiro
description: incluir ficheiro
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184694"
---
### <a name="code-walkthrough"></a>Código passo a passo

Esta secção descreve algumas das principais partes do código de exemplo e explica como se relacionam o acelerador de solução de monitorização remota.

O fragmento seguinte mostra como as propriedades reportadas que descrevem as capacidades do dispositivo são definidas. Essas propriedades incluem:

- A localização do dispositivo para ativar o solution accelerator adicionar o dispositivo ao mapa.
- A versão de firmware atual.
- A lista de métodos que o dispositivo suporta.
- O esquema das mensagens de telemetria enviada pelo dispositivo.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

O exemplo inclui um **serializeToJson** função que serializa essa estrutura de dados usando a biblioteca de Parson.

O exemplo inclui várias funções de retorno de chamada que imprimem informações para a consola à medida que o cliente interage com o solution accelerator:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

O fragmento seguinte mostra os **device_method_callback** função. Esta função determina a ação a tomar quando uma chamada de método é recebida a partir do solution accelerator. A função recebe uma referência para o **Chiller** estrutura de dados no **userContextCallback** parâmetro. O valor de **userContextCallback** é definida quando a função de retorno de chamada é configurada no **principal** função:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Quando o solution accelerator chama o método de atualização de firmware, o exemplo desserializa o payload JSON e inicia um thread em segundo plano para concluir o processo de atualização. O fragmento seguinte mostra os **do_firmware_update** que é executado no thread:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

O fragmento seguinte mostra como o cliente envia uma mensagem de telemetria para o solution accelerator. As propriedades da mensagem incluem o esquema de mensagem para ajudar o solution accelerator apresentar a telemetria no dashboard:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

O **principal** função no exemplo:

- Inicializa e encerra o subsistema SDK.
- Inicializa a **Chiller** estrutura de dados.
- Envia as propriedades reportadas para o solution accelerator.
- Configura a função de retorno de chamada de método de dispositivo.
- Envia simulado valores de telemetria para o solution accelerator.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
