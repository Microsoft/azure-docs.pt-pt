---
title: Borda incorporada Métodos diretos Agent - Azure IoT Edge
description: Monitorize e gere uma implementação IoT Edge utilizando métodos diretos incorporados no módulo de execução do agente IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240351"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicar com o edgeAgent usando métodos diretos incorporados

Monitorize e gere as implementações do IoT Edge utilizando os métodos diretos incluídos no módulo de agente IoT Edge. Os métodos diretos são implementados no dispositivo e, em seguida, podem ser invocados a partir da nuvem. O agente IoT Edge inclui métodos diretos que o ajudam a monitorizar e gerir remotamente os seus dispositivos IoT Edge.

Para obter mais informações sobre métodos diretos, como usá-los e como implementá-los nos seus próprios módulos, consulte [Compreender e invocar métodos diretos do IoT Hub.](../iot-hub/iot-hub-devguide-direct-methods.md)

Os nomes destes métodos diretos são tratados caso-insensível.

## <a name="ping"></a>Ping

O método **do ping** é útil para verificar se o IoT Edge está a funcionar num dispositivo ou se o dispositivo tem uma ligação aberta ao IoT Hub. Utilize este método direto para pingar o agente IoT Edge e obter o seu estado. Um ping bem sucedido devolve uma carga vazia e **"status": 200**.

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

No portal Azure, invoque o método com o nome do método `ping` e uma carga JSON vazia `{}` .

![Invocar método direto 'ping' no portal Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Módulo de reinicialização

O método **RestartModule** permite uma gestão remota de módulos em execução num dispositivo IoT Edge. Se um módulo estiver a reportar um estado falhado ou outro comportamento pouco saudável, pode acionar o agente IoT Edge para reiniciá-lo. Um comando de reinício bem sucedido devolve uma carga e **um "estado" vazios: 200**.

O método RestartModule está disponível na versão IoT Edge 1.0.9 e posterior. 

Pode utilizar o método direto RestartModule em qualquer módulo que esteja a funcionar num dispositivo IoT Edge, incluindo o próprio módulo edgeAgent. No entanto, se utilizar este método direto para desligar o edgeAgent, não receberá um resultado de sucesso uma vez que a ligação é interrompida enquanto o módulo reinicia.

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

No portal Azure, invoque o método com o nome do método `RestartModule` e a seguinte carga útil JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Invocar o método direto 'RestartModule' no portal Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Métodos experimentais

Novas opções de método direto estão disponíveis como características experimentais a testar, incluindo:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Recupere os registos dos módulos e faça o upload para o Armazenamento Azure Blob.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Verifique o estado de um pedido de registos de upload.
* [ObterLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Recuperar os registos dos módulos inline na resposta do método direto.

## <a name="next-steps"></a>Passos seguintes

[Propriedades do agente IoT Edge e gémeos módulos de hub IoT Edge](module-edgeagent-edgehub.md)
