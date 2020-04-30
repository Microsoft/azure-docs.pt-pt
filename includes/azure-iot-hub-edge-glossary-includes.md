---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 30b8facfef6d90a444bd61d0ce041ed7dfef324e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131628"
---
## <a name="automatic-device-management"></a>Gestão automática de Dispositivos
A Gestão Automática de Dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gerir grandes frotas de dispositivos ao longo de toda a sua vida. Com a Gestão Automática de Dispositivos, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e deixar os dispositivos de atualização do IoT Hub sempre que entram em alcance.  Consiste em [configurações automáticas](../articles/iot-hub/iot-hub-auto-device-config.md) de dispositivos e [implementações automáticas IoT Edge](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
O Azure IoT Edge permite a implantação de serviços Azure e código específico de soluções para dispositivos no local. Os dispositivos IoT Edge podem agregar dados de outros dispositivos para realizar computação e análise antes de os dados forem enviados para a nuvem. Para mais informações, consulte [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente IoT Edge
A parte do tempo de funcionamento do IoT Edge responsável pela implementação e monitorização dos módulos.

## <a name="iot-edge-device"></a>Dispositivo IoT Edge
Os dispositivos IoT Edge têm o tempo de execução do IoT Edge instalado e são sinalizados como **dispositivo IoT Edge** nos detalhes do dispositivo. Aprenda a [implementar o Azure IoT Edge num dispositivo simulado em Linux - pré-visualização](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Implantação automática IoT Edge
Uma implementação automática IoT Edge configura um conjunto de alvos de dispositivos IoT Edge para executar um conjunto de módulos IoT Edge. Cada implementação garante continuamente que todos os dispositivos que correspondam ao seu estado-alvo estão a executar o conjunto especificado de módulos, mesmo quando novos dispositivos são criados ou são modificados para corresponder à condição-alvo. Cada dispositivo IoT Edge recebe apenas a maior implantação prioritária cuja condição de destino se encontra. Saiba mais sobre a [implementação automática IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifesto de implantação de IoT Edge
Um documento Json contendo as informações a copiar num ou mais módulos de módulos IoT Edge twin(s) para implantar um conjunto de módulos, rotas e propriedades desejadas por módulos associados.

## <a name="iot-edge-gateway-device"></a>Dispositivo de gateway IoT Edge
Um dispositivo IoT Edge com dispositivo a jusante. O dispositivo a jusante pode ser ioT Edge ou não ioT Edge.

## <a name="iot-edge-hub"></a>Hub IoT Edge
A parte do tempo de funcionamento do IoT Edge responsável pelo módulo de comunicações de módulos, a montante (em direção ao Hub IoT) e a jusante (longe do IoT Hub). 

## <a name="iot-edge-leaf-device"></a>Dispositivo de folha de borda iot
Um dispositivo IoT Edge sem dispositivo a jusante. 

## <a name="iot-edge-module"></a>Módulo IoT Edge
Um módulo IoT Edge é um recipiente Docker que pode implantar para dispositivos IoT Edge. Executa uma tarefa específica, como ingerir uma mensagem a partir de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um hub IoT. Comunica com outros módulos e envia dados para o tempo de funcionar do IoT Edge. [Compreenda os requisitos e ferramentas para o desenvolvimento de módulos IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identidade do módulo IoT Edge
Um registo no registo de identidade do módulo IoT Hub detalhando a existência e credenciais de segurança a utilizar por um módulo para autenticar com um hub de borda ou hub IoT.

## <a name="iot-edge-module-image"></a>Imagem do módulo IoT Edge
A imagem do estivador que é usada pelo tempo de funcionação do IoT Edge para instantaneamente casos de módulos.

## <a name="iot-edge-module-twin"></a>Módulo IoT Edge twin
Um documento Json persistiu no IoT Hub que armazena a informação do Estado para uma instância de módulo.

## <a name="iot-edge-priority"></a>Prioridade do IoT Edge
Quando duas implementações ioT Edge visam o mesmo dispositivo, a implementação com maior prioridade é aplicada. Se duas implementações tiverem a mesma prioridade, a implantação com a data de criação posterior é aplicada. Saiba mais sobre [prioridade.](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority)

## <a name="iot-edge-runtime"></a>Runtime do IoT Edge
O tempo de execução do IoT Edge inclui tudo o que a Microsoft distribui para ser instalado num dispositivo IoT Edge. Inclui o agente Edge, o edge hub, e o daemon de segurança IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Módulos conjuntos IoT Edge para um único dispositivo
Uma operação que copia o conteúdo de um manifesto IoT Edge num módulo gémeo de um dispositivo. A API subjacente é uma configuração genérica de "aplicar", que simplesmente toma um manifesto IoT Edge como entrada.

## <a name="iot-edge-target-condition"></a>Condição de alvo de Borda IoT
Numa implementação ioT Edge, a condição de destino é qualquer condição Boolean nas etiquetas dos gémeos do dispositivo para selecionar os dispositivos-alvo da implementação, por exemplo **tag.environment = prod**. A condição-alvo é continuamente avaliada para incluir quaisquer novos dispositivos que satisfaçam os requisitos ou removam dispositivos que já não o façam. Saiba mais sobre a [condição do alvo](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)