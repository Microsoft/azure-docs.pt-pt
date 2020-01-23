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
ms.openlocfilehash: cbca0c2509e74a7debf5ba26b361c79b9b208f08
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547120"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>De que forma um dispositivo IoT Edge pode ser utilizado como gateway

Os gateways em soluções IoT Edge fornecem conectividade de dispositivo e análise de borda para dispositivos IoT que, caso contrário, não teriam esses recursos. Azure IoT Edge pode ser usado para atender a qualquer necessidade de um gateway de IoT, seja relacionado à conectividade, identidade ou análise de borda. Padrões de gateway neste artigo só fazer referência às características de conectividade do dispositivo downstream e identidade de dispositivo, não como dados de dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões

Existem três padrões para a utilização de um dispositivo IoT Edge como gateway: protocolo transparente, tradução e tradução de identidade:

* **Transparente** – dispositivos que, teoricamente, é podem ligar ao IoT Hub podem ligar a um dispositivo de gateway em vez disso. Os dispositivos de downstream têm suas próprias identidades do IoT Hub e estiver a utilizar qualquer um dos protocolos MQTT, AMQP ou HTTP. O gateway de limite a passar comunicações entre os dispositivos e IoT Hub. Os dispositivos e os usuários que interagem com eles por meio do Hub IoT não sabem que um gateway está mediando suas comunicações. Essa falta de conscientização significa que o gateway é considerado *transparente*. Consulte a [criar um gateway transparente](how-to-create-transparent-gateway.md) para obter informações específicas sobre como utilizar um dispositivo IoT Edge como gateway transparente.
* **Tradução de protocolos** – também conhecido como um padrão de gateway opaco, dispositivos que não suportam MQTT, AMQP ou HTTP podem utilizar um dispositivo de gateway para enviar dados para o IoT Hub em nome deles. O gateway compreende o protocolo usado pelos dispositivos downstream e é o único dispositivo que tem uma identidade no Hub IoT. Parece todas as informações que ele é proveniente de um dispositivo, o gateway. Dispositivos Downstream devem incorporá-informações de identificação adicionais nas suas mensagens se quiser que aplicações na cloud analisar os dados numa base por dispositivo. Além disso, primitivos de IoT Hub como gémeos e métodos só estão disponíveis para o dispositivo de gateway, os dispositivos não downstream.
* **Tradução de identidade** -dispositivos que não é possível ligar ao IoT Hub podem ligar a um dispositivo de gateway, em vez disso. O gateway fornece o IoT Hub tradução de identidade e o protocolo em nome dos dispositivos de downstream. O gateway é inteligente o suficiente para compreender o protocolo utilizado pelos dispositivos downstream, identidade de fornecê-los e traduzir primitivos de IoT Hub. Dispositivos Downstream aparecem no IoT Hub como primeira classe dispositivos duplos e métodos. Um utilizador pode interagir com os dispositivos no IoT Hub e não reconhece o dispositivo de gateway intermédio.

![Diagrama - transparente, o protocolo e padrões de gateway de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Casos de utilização

Todos os padrões de gateway fornecem as seguintes vantagens:

* **Análise na borda** – use os serviços de ia localmente para processar dados provenientes de dispositivos downstream sem enviar telemetria de fidelidade total à nuvem. Encontre e reagir às informações localmente e enviar apenas um subconjunto de dados para o IoT Hub.
* **Isolamento de dispositivo Downstream** – o dispositivo de gateway pode proteger todos os dispositivos de downstream da exposição à internet. Ele pode ser usada entre uma rede ou que não tenha conectividade e uma rede de TI que fornece acesso à web.
* **Ligação multiplexação** - todos os dispositivos para ligar ao IoT Hub, por meio de uma utilização de gateway do IoT Edge, a mesma ligação subjacente.
* **Suavização de tráfego** -dispositivo do IoT Edge irá automaticamente implementar um término exponencial se IoT Hub limita o tráfego, ao persistir as mensagens localmente. Este benefício torna a sua solução resiliente para picos de tráfego.
* **Suporte offline** -o dispositivo de gateway armazena mensagens e atualizações de atualização de bits que não podem ser entregues ao Hub IOT.

Um gateway que a tradução de protocolo também pode executar análises avançadas, isolamento de dispositivo, a suavização de tráfego e suporte offline para dispositivos existentes e novos dispositivos que estão a restrição de recursos. Número de dispositivos existente está produzindo dados que podem potenciar as informações de negócio No entanto não foram projetados tendo em mente, a conectividade de cloud. Os gateways opacos permitem que esses dados sejam desbloqueados e usados em uma solução de IoT.

Um gateway que faz a tradução de identidade fornece os benefícios da tradução do protocolo e adicionalmente permite a capacidade de gerenciamento completa de downstream dispositivos a partir da cloud. Todos os dispositivos na sua solução de IoT aparecem no IoT Hub, independentemente do protocolo que utilizam.

## <a name="cheat-sheet"></a>Nota

Aqui está uma rápida e dicas que compara os primitivos de IoT Hub ao utilizar transparente, opaco (protocol) e do proxy de gateways.

| &nbsp; | Gateway transparente | Tradução de protocolos | Tradução de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registo de identidade do IoT Hub | Identidades de todos os dispositivos ligados | Apenas a identidade do dispositivo de gateway | Identidades de todos os dispositivos ligados |
| Dispositivo duplo | Cada dispositivo ligado tem seu próprio dispositivo duplo | Apenas o gateway tem um duplos de módulo e dispositivo | Cada dispositivo ligado tem seu próprio dispositivo duplo |
| Os métodos diretos e mensagens da cloud para dispositivo | A cloud pode resolver individualmente cada dispositivo ligado | Cloud apenas os pode resolver o dispositivo de gateway | A cloud pode resolver individualmente cada dispositivo ligado |
| [Quotas e limitações do IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplicar a cada dispositivo | Aplicam-se para o dispositivo de gateway | Aplicar a cada dispositivo |

Ao usar um padrão de gateway opaco (tradução do protocolo), todos os dispositivos que se conectam através desse gateway partilham a mesma fila de cloud para o dispositivo, que pode conter no máximo 50 mensagens. Ela segue o padrão gateway opaco deve ser utilizado apenas quando alguns dispositivos se ligarem através de cada gateway de campo e o tráfego de nuvem para o dispositivo é baixo.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar um gateway transparente:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
