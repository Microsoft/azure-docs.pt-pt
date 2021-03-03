---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750080"
---
Utilize as respostas de execução do agente IoT Edge para resolver erros relacionados com a computação. Aqui está uma lista de possíveis respostas:

* 200 - OK
* 400 - A configuração de implantação é mal formada ou inválida.
* 417 - O dispositivo não tem um conjunto de configuração de implantação.
* 412 - A versão de esquema na configuração de implementação é inválida.
* 406 - O dispositivo IoT Edge está offline ou não está a enviar relatórios de estado.
* 500 - Ocorreu um erro no tempo de execução do IoT Edge.

Para mais informações, consulte [o Agente IoT Edge.](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)

O seguinte erro está relacionado com o serviço IoT Edge no seu Azure Stack Edge Pro<!--/ Data Box Gateway--> todos os dispositivos.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Os módulos computacional têm estatuto desconhecido e não podem ser usados

#### <a name="error-description"></a>Descrição do erro

Todos os módulos do dispositivo mostram o estado desconhecido e não podem ser utilizados. O estado desconhecido persiste através de um reboot.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Solução sugerida

Elimine o serviço IoT Edge e, em seguida, reposicione o módulo(s). Para mais informações, consulte [o serviço Remove IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).