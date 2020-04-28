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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184694"
---
### <a name="code-walkthrough"></a>Instruções de código

Esta secção descreve algumas das partes-chave do código da amostra e explica como se relacionam com o acelerador de soluções de monitorização remota.

O seguinte corte mostra como as propriedades relatadas que descrevem as capacidades do dispositivo são definidas. Estas propriedades incluem:

- A localização do dispositivo para permitir que o acelerador de solução adicione o dispositivo ao mapa.
- A versão atual do firmware.
- A lista de métodos que o dispositivo suporta.
- O esquema das mensagens de telemetria enviadas pelo dispositivo.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

A amostra inclui uma função **serializeToJson** que serializa esta estrutura de dados usando a biblioteca Parson.

A amostra inclui várias funções de callback que imprimem informações para a consola à medida que o cliente interage com o acelerador de solução:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

O seguinte corte mostra a função **device_method_callback.** Esta função determina a ação a tomar quando uma chamada de método é recebida do acelerador de solução. A função recebe uma referência à estrutura de dados **chiller** no parâmetro contextBack do **utilizador.** O valor do **utilizadorContextCallback** é definido quando a função de chamada está configurada na função **principal:**

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Quando o acelerador de solução chama o método de atualização do firmware, a amostra desserializa a carga útil JSON e inicia um fio de fundo para completar o processo de atualização. O seguinte corte mostra o **do_firmware_update** que corre na linha:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

O seguinte corte mostra como o cliente envia uma mensagem de telemetria para o acelerador de soluções. As propriedades da mensagem incluem o esquema da mensagem para ajudar o acelerador de solução a exibir a telemetria no painel de instrumentos:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

A **função principal** na amostra:

- Inicializa e desliga o subsistema SDK.
- Inicializa a estrutura de dados **chiller.**
- Envia as propriedades reportadas para o acelerador de solução.
- Refigura a função de chamada do método do dispositivo.
- Envia valores de telemetria simulados para o acelerador de solução.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
