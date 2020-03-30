---
title: Métodos diretos edgeAgent incorporados - Borda Azure IoT
description: Monitorize e gerencie uma implementação IoT Edge utilizando métodos diretos incorporados no módulo de tempo de execução do agente IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240351"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicar com edgeAgent utilizando métodos diretos incorporados

Monitorize e gerencie as implementações do IoT Edge utilizando os métodos diretos incluídos no módulo de agente IoT Edge. Os métodos diretos são implementados no dispositivo e, em seguida, podem ser invocados a partir da nuvem. O agente IoT Edge inclui métodos diretos que o ajudam a monitorizar e gerir remotamente os seus dispositivos IoT Edge.

Para obter mais informações sobre métodos diretos, como usá-los e como implementá-los nos seus próprios módulos, consulte [Compreender e invocar métodos diretos a partir do IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Os nomes destes métodos diretos são tratados de forma insensível.

## <a name="ping"></a>Ping

O método **de ping** é útil para verificar se o IoT Edge está a funcionar num dispositivo ou se o dispositivo tem uma ligação aberta ao IoT Hub. Utilize este método direto para pingar o agente IoT Edge e obter o seu estado. Um ping bem sucedido devolve uma carga útil vazia e **um "status": 200**.

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

No portal Azure, invoque o `ping` método com o nome `{}`do método e uma carga útil JSON vazia.

![Invocar método direto 'ping' no portal Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Reiniciar módulo

O método **RestartModule** permite a gestão remota de módulos em execução num dispositivo IoT Edge. Se um módulo estiver a reportar um estado falhado ou outro comportamento pouco saudável, pode ativar o agente IoT Edge para o reiniciar. Um comando de reinício bem sucedido devolve uma carga útil vazia e **um "status": 200**.

O método RestartModule está disponível na versão 1.0.9 do IoT Edge e posteriormente. 

Pode utilizar o método direto RestartModule em qualquer módulo que esteja em execução num dispositivo IoT Edge, incluindo o próprio módulo EdgeAgent. No entanto, se utilizar este método direto para desligar o edgeAgent, não receberá um resultado de sucesso uma vez que a ligação é interrompida enquanto o módulo reinicia.

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

No portal Azure, invoque o `RestartModule` método com o nome do método e a seguinte carga útil da JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Invoque o método direto 'RestartModule' no portal Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Métodos experimentais

Estão disponíveis novas opções de métodos diretos como características experimentais para testar, incluindo:

* [UploadLogs:](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md)Recuperar os registos dos módulos e carregá-los para o Armazenamento De Blob Azure.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Verifique o estado de um pedido de registo de upload.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Recuperar os registos dos módulos inline na resposta do método direto.

## <a name="next-steps"></a>Passos seguintes

[Propriedades do agente IoT Edge e dos gémeos do módulo IoT Edge](module-edgeagent-edgehub.md)
