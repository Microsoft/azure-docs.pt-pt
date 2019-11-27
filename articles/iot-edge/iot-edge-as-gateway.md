---
title: Gateways para jusante dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Utilize o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou o proxy que envia dados de vários dispositivos de downstream para a cloud ou processa-os localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ec8b6cf61f9fb92f888642d1de7d4d1b9b7ac3df
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456655"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>De que forma um dispositivo IoT Edge pode ser utilizado como gateway

Os gateways em soluções IoT Edge fornecem conectividade de dispositivo e análise de borda para dispositivos IoT que, caso contrário, não teriam esses recursos. O Azure IoT Edge pode ser utilizado para satisfazer as necessidades de todos os para um gateway de IoT, independentemente se estão relacionadas com a conectividade, a identidade ou análises avançadas. Padrões de gateway neste artigo só fazer referência às características de conectividade do dispositivo downstream e identidade de dispositivo, não como dados de dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões

Existem três padrões para a utilização de um dispositivo IoT Edge como gateway: protocolo transparente, tradução e tradução de identidade:
* **Transparente** – dispositivos que teoricamente podiam se conectar ao Hub IOT podem se conectar a um dispositivo de gateway em vez disso. Os dispositivos de downstream têm suas próprias identidades do IoT Hub e estiver a utilizar qualquer um dos protocolos MQTT, AMQP ou HTTP. O gateway de limite a passar comunicações entre os dispositivos e IoT Hub. Os dispositivos não sabem que estão se comunicando com a nuvem por meio de um gateway, e um usuário que interage com os dispositivos no Hub IoT não está ciente do dispositivo de gateway intermediário. Portanto, o gateway é transparente. Consulte [criar um gateway transparente](how-to-create-transparent-gateway.md) para obter informações específicas sobre como usar um dispositivo IOT Edge como um gateway transparente.
* **Conversão de protocolo** – também conhecido como padrão de gateway opaco, os dispositivos que não dão suporte a MQTT, AMQP ou http podem usar um dispositivo de gateway para enviar dados ao Hub IOT em seu nome. O gateway compreende o protocolo usado pelos dispositivos downstream e é o único dispositivo que tem uma identidade no Hub IoT. Parece todas as informações que ele é proveniente de um dispositivo, o gateway. Dispositivos Downstream devem incorporá-informações de identificação adicionais nas suas mensagens se quiser que aplicações na cloud analisar os dados numa base por dispositivo. Além disso, primitivos de IoT Hub como gémeos e métodos só estão disponíveis para o dispositivo de gateway, os dispositivos não downstream.
* **Conversão de identidade** – dispositivos que não podem se conectar ao Hub IOT podem se conectar a um dispositivo de gateway, em vez disso. O gateway fornece o IoT Hub tradução de identidade e o protocolo em nome dos dispositivos de downstream. O gateway é inteligente o suficiente para compreender o protocolo utilizado pelos dispositivos downstream, identidade de fornecê-los e traduzir primitivos de IoT Hub. Dispositivos Downstream aparecem no IoT Hub como primeira classe dispositivos duplos e métodos. Um utilizador pode interagir com os dispositivos no IoT Hub e não reconhece o dispositivo de gateway intermédio.

![Diagrama - transparente, o protocolo e padrões de gateway de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Casos de utilização
Todos os padrões de gateway fornecem as seguintes vantagens:
* **Análise na borda** – use os serviços de ia localmente para processar dados provenientes de dispositivos downstream sem enviar telemetria de fidelidade total à nuvem. Encontre e reagir às informações localmente e enviar apenas um subconjunto de dados para o IoT Hub. 
* **Isolamento de dispositivo downstream** – o dispositivo de gateway pode blindar todos os dispositivos downstream da exposição à Internet. Ele pode ser usada entre uma rede ou que não tenha conectividade e uma rede de TI que fornece acesso à web. 
* **Multiplexação de conexão** -todos os dispositivos que se conectam ao Hub IOT por meio de um gateway de IOT Edge usam a mesma conexão subjacente.
* **Suavização de tráfego** -o dispositivo de IOT Edge implementará automaticamente a retirada exponencial se o Hub IOT limitar o tráfego, enquanto mantém as mensagens localmente. Este benefício torna a sua solução resiliente para picos de tráfego.
* **Suporte offline** -o dispositivo de gateway armazena mensagens e atualizações de atualização de bits que não podem ser entregues ao Hub IOT.

Um gateway que a tradução de protocolo também pode executar análises avançadas, isolamento de dispositivo, a suavização de tráfego e suporte offline para dispositivos existentes e novos dispositivos que estão a restrição de recursos. Número de dispositivos existente está produzindo dados que podem potenciar as informações de negócio No entanto não foram projetados tendo em mente, a conectividade de cloud. Os gateways opacos permitem que esses dados sejam desbloqueados e usados em uma solução de IoT.

Um gateway que faz a tradução de identidade fornece os benefícios da tradução do protocolo e adicionalmente permite a capacidade de gerenciamento completa de downstream dispositivos a partir da cloud. Todos os dispositivos na sua solução de IoT aparecem no IoT Hub, independentemente do protocolo que utilizam.

## <a name="cheat-sheet"></a>Truques e dicas
Aqui está uma rápida e dicas que compara os primitivos de IoT Hub ao utilizar transparente, opaco (protocol) e do proxy de gateways.

| &nbsp; | Gateway transparente | Tradução de protocolos | Tradução de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registo de identidade do IoT Hub | Identidades de todos os dispositivos ligados | Apenas a identidade do dispositivo de gateway | Identidades de todos os dispositivos ligados |
| Dispositivo duplo | Cada dispositivo ligado tem seu próprio dispositivo duplo | Apenas o gateway tem um duplos de módulo e dispositivo | Cada dispositivo ligado tem seu próprio dispositivo duplo |
| Os métodos diretos e mensagens da cloud para dispositivo | A cloud pode resolver individualmente cada dispositivo ligado | Cloud apenas os pode resolver o dispositivo de gateway | A cloud pode resolver individualmente cada dispositivo ligado |
| [Limitação e cotas do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplicar a cada dispositivo | Aplicam-se para o dispositivo de gateway | Aplicar a cada dispositivo |

Ao usar um padrão de gateway opaco (tradução do protocolo), todos os dispositivos que se conectam através desse gateway partilham a mesma fila de cloud para o dispositivo, que pode conter no máximo 50 mensagens. Ela segue o padrão gateway opaco deve ser utilizado apenas quando alguns dispositivos se ligarem através de cada gateway de campo e o tráfego de nuvem para o dispositivo é baixo.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar um gateway transparente: 

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)