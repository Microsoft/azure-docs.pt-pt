---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761541"
---
Utilize as respostas de execução do agente IoT Edge para resolver erros relacionados com a computação. Aqui está uma lista de possíveis respostas:

* 200 - OK
* 400 - A configuração de implantação é mal formada ou inválida.
* 417 - O dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão de esquema na configuração de implementação é inválida.
* 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
* 500 - Ocorreu um erro no tempo de execução do IoT Edge.

Para mais informações, consulte [o Agente IoT Edge.](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent)