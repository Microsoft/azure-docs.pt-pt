---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445078"
---
## <a name="automatic-device-management"></a>Gestão Automática de Dispositivos
Gestão automática de dispositivos no IoT Hub do Azure automatiza muitas das tarefas repetitivas e complexas de gerenciamento de frotas de dispositivo grandes sobre a totalidade dos respetivos ciclos de vida. Gestão de dispositivos automático, pode um conjunto de dispositivos com base nas respetivas propriedades de destino, definir uma configuração desejada e permitir que o IoT Hub, atualize os dispositivos sempre que eles vão para o âmbito.  Consiste [configurações de dispositivos automático](../articles/iot-hub/iot-hub-auto-device-config.md) e [implementações automáticas do IoT Edge](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
O Azure IoT Edge permite orientado pela cloud a implementação de serviços do Azure e o código específico da solução para dispositivos no local. Dispositivos IoT Edge podem agregar dados de outros dispositivos para realizar a computação e análise de antes dos dados são enviados para a cloud. Para obter mais informações, consulte [do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agente do IoT Edge
A parte do runtime do IoT Edge responsável por implantar e módulos de monitorização.

## <a name="iot-edge-device"></a>Dispositivo IoT Edge
Dispositivos IoT Edge têm o runtime do IoT Edge instalado e são sinalizados como **dispositivo IoT Edge** nos detalhes do dispositivo. Saiba como [implementar o Azure IoT Edge num dispositivo simulado no Linux - pré-visualização](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Implementação automática do IoT Edge
Uma implementação automática do IoT Edge configura um conjunto de destino de dispositivos do IoT Edge para executar um conjunto do IoT Edge módulos. Cada implementação continuamente garante que todos os dispositivos que correspondam à sua condição de destino estiver a executar o conjunto especificado de módulos, mesmo quando novos dispositivos são criados ou modificados de acordo com a condição de destino. Cada dispositivo IoT Edge apenas recebe a implementação de prioridade mais elevada cuja condição de destino cumpre. Saiba mais sobre [implementação automática do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifesto de implantação do IoT Edge
Um documento Json que contém as informações a serem copiadas twin(s) de módulo de um ou mais dispositivos de IoT Edge para implementar um conjunto de módulos, rotas e o módulo propriedades pretendidas.

## <a name="iot-edge-gateway-device"></a>Dispositivo de gateway do IoT Edge
Um dispositivo IoT Edge com o dispositivo de downstream. O dispositivo de downstream pode ser o IoT Edge, ou não o dispositivo IoT Edge.

## <a name="iot-edge-hub"></a>Hub do IoT Edge
A parte do runtime do IoT Edge responsável pelo módulo de módulo de comunicações, a montante (para o IoT Hub) e a jusante (para fora do IoT Hub) de comunicações. 

## <a name="iot-edge-leaf-device"></a>Dispositivo de folha de IoT Edge
Um dispositivo IoT Edge com nenhum dispositivo de downstream. 

## <a name="iot-edge-module"></a>Módulo IoT Edge
Módulo do IoT Edge é um contentor de Docker que pode implementar em dispositivos IoT Edge. Ele executa uma tarefa específica, como a ingestão de uma mensagem a partir de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um hub IoT. Ele se comunica com outros módulos e envia dados para o runtime do IoT Edge. [Compreender os requisitos e as ferramentas para desenvolver módulos do IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identidade de módulo do IoT Edge
Um registo no registo de identidade do módulo do IoT Hub com detalhes sobre as credenciais de segurança e de existência para ser utilizado por um módulo para autenticar com um hub do edge ou o IoT Hub.

## <a name="iot-edge-module-image"></a>Imagem de módulo do IoT Edge
A imagem do docker que é utilizada pelo runtime do IoT Edge para instanciar instâncias de módulo.

## <a name="iot-edge-module-twin"></a>Duplo do módulo de IoT Edge
Um documento Json que permanecem no IoT Hub que armazena as informações de estado para uma instância de módulo.

## <a name="iot-edge-priority"></a>Prioridade do IoT Edge
Quando duas implementações do IoT Edge no mesmo dispositivo de destino, a implementação com prioridade mais alta é aplicada. Se duas implementações têm a mesma prioridade, é aplicada a implementação com a data de criação posterior. Saiba mais sobre [prioridade](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Runtime do IoT Edge
Runtime do IoT Edge inclui tudo o que a Microsoft distribui para ser instalado num dispositivo IoT Edge. Ele inclui o agente do Edge hub do Edge e o daemon de segurança de IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge definir módulos a um único dispositivo
Uma operação que copia o conteúdo de um manifesto do IoT Edge no dispositivo de um "duplo do módulo. A API básica é um genérico "aplicar a configuração", que simplesmente pega um manifesto do IoT Edge como entrada.

## <a name="iot-edge-target-condition"></a>Condição de destino do IoT Edge
Numa implementação do IoT Edge, a condição de destino é qualquer condição booleana em etiquetas dos dispositivos duplos para selecionar os dispositivos de destino da implementação, por exemplo **tag.environment = prod**. A condição de destino é avaliada continuamente para incluir novos dispositivos que cumprem os requisitos ou remover dispositivos que deixaram de exigi. Saiba mais sobre [condição de destino](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)