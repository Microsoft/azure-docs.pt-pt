---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 0b478775aa572ce89f3dcdc6f28ee0418e818d99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377390"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Gestão automática de dispositivos no Azure IoT Hub
A Gestão Automática de Dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos ao longo de todo o seu ciclo de vida. Com a Gestão Automática de Dispositivos, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e deixar que os dispositivos de atualização do IoT Hub sempre que eles entram no âmbito.  Consiste em [configurações automáticas](../articles/iot-hub/iot-hub-auto-device-config.md) de dispositivos e [implementações automáticas IoT Edge](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
O Azure IoT Edge permite a implantação em nuvem de serviços Azure e código específico de solução para dispositivos no local. Os dispositivos IoT Edge podem agregar dados de outros dispositivos para realizar computação e análise antes de os dados serem enviados para a nuvem. Para mais informações, consulte [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente IoT Edge
A parte do tempo de funcionamento do IoT Edge responsável pela implantação e monitorização de módulos.

## <a name="iot-edge-device"></a>Dispositivo do IoT Edge
Os dispositivos IoT Edge têm o tempo de execução IoT Edge instalado e são sinalizados como **dispositivo IoT Edge** nos detalhes do dispositivo. Saiba como [implantar o Azure IoT Edge num dispositivo simulado em Linux - pré-visualização](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Implantação automática IoT Edge
Uma implementação automática IoT Edge configura um conjunto de dispositivos IoT Edge para executar um conjunto de módulos IoT Edge. Cada implementação garante continuamente que todos os dispositivos que correspondam à sua condição alvo estão a executar o conjunto especificado de módulos, mesmo quando novos dispositivos são criados ou modificados para corresponder à condição alvo. Cada dispositivo IoT Edge só recebe a maior prioridade de implementação cuja condição de alvo cumpre. Saiba mais sobre [a implementação automática IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifesto de implantação IoT Edge
Um documento Json que contém as informações a copiar num ou mais módulos de dispositivos IoT Edge twin para implantar um conjunto de módulos, rotas e propriedades de módulos associados desejados.

## <a name="iot-edge-gateway-device"></a>Dispositivo de gateway IoT Edge
Um dispositivo IoT Edge com dispositivo a jusante. O dispositivo a jusante pode ser o dispositivo IoT Edge ou não IoT Edge.

## <a name="iot-edge-hub"></a>Hub IoT Edge
A parte do tempo de funcionação do IoT Edge responsável pelas comunicações do módulo para módulos, a montante (em direção ao IoT Hub) e a jusante (longe do IoT Hub). 

## <a name="iot-edge-leaf-device"></a>Dispositivo de folha de borda IoT
Um dispositivo IoT Edge sem dispositivo a jusante. 

## <a name="iot-edge-module"></a>Módulo IoT Edge
Um módulo IoT Edge é um recipiente Docker que pode implantar em dispositivos IoT Edge. Executa uma tarefa específica, como ingerir uma mensagem a partir de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um hub IoT. Comunica com outros módulos e envia dados para o tempo de funcionação do IoT Edge. [Compreenda os requisitos e ferramentas para o desenvolvimento de módulos IoT Edge.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="iot-edge-module-identity"></a>Identidade do módulo IoT Edge
Um registo no registo de identidade do módulo IoT Hub que detalha a existência e as credenciais de segurança a serem usadas por um módulo para autenticar com um hub de borda ou IoT Hub.

## <a name="iot-edge-module-image"></a>Imagem do módulo IoT Edge
A imagem do estivador que é usada pelo tempo de execução IoT Edge para instantaneaizar as instâncias do módulo.

## <a name="iot-edge-module-twin"></a>Módulo IoT Edge gémeo
Um documento Json persistiu no IoT Hub que armazena a informação do Estado para uma instância de módulo.

## <a name="iot-edge-priority"></a>Prioridade IoT Edge
Quando duas implementações IoT Edge visam o mesmo dispositivo, a implementação com maior prioridade é aplicada. Se duas implementações tiverem a mesma prioridade, a implantação com a data de criação posterior é aplicada. Saiba mais sobre [a prioridade.](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority)

## <a name="iot-edge-runtime"></a>Runtime do IoT Edge
O tempo de execução do IoT Edge inclui tudo o que a Microsoft distribui para ser instalado num dispositivo IoT Edge. Inclui o agente Edge, o edge hub e o daemon de segurança IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge definir módulos para um único dispositivo
Uma operação que copia o conteúdo de um manifesto IoT Edge no módulo gémeo de um dispositivo. A API subjacente é uma "configuração de aplicação" genérica, que simplesmente toma um manifesto IoT Edge como uma entrada.

## <a name="iot-edge-target-condition"></a>Condição alvo IoT Edge
Numa implementação IoT Edge, a condição do alvo é qualquer condição booleana nas etiquetas dos gémeos do dispositivo para selecionar os dispositivos-alvo da implementação, por exemplo **tag.environment = prod**. A condição alvo é continuamente avaliada para incluir quaisquer novos dispositivos que satisfaçam os requisitos ou removam dispositivos que já não o façam. Saiba mais sobre [a condição do alvo](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)