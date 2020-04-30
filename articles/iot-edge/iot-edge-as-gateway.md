---
title: Gateways para dispositivos a jusante - Azure IoT Edge [ Microsoft Docs
description: Utilize o Azure IoT Edge para criar um dispositivo transparente, opaco ou proxy gateway que envia dados de vários dispositivos a jusante para a nuvem ou processa-o localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 916eeaa60bc054301af039164ce1c14e77ceb91a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733518"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>De que forma um dispositivo IoT Edge pode ser utilizado como gateway

Gateways em soluções IoT Edge fornecem conectividade do dispositivo e análise de borda para dispositivos IoT que de outra forma não teriam essas capacidades. O Azure IoT Edge pode ser usado para satisfazer qualquer necessidade de um gateway IoT, seja relacionado com conectividade, identidade ou análise de bordas. Os padrões de gateway neste artigo referem-se apenas às características da conectividade do dispositivo a jusante e à identidade do dispositivo, e não à forma como os dados do dispositivo são processados no portal.

## <a name="patterns"></a>Padrões

Existem três padrões para a utilização de um dispositivo IoT Edge como porta de entrada: transparente, tradução protocolar e tradução de identidade:

* **Transparente** – Dispositivos que teoricamente poderiam ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway. Os dispositivos a jusante têm as suas próprias identidades IoT Hub e estão a usar qualquer um dos protocolos MQTT, AMQP ou HTTP. O portal simplesmente passa comunicações entre os dispositivos e o IoT Hub. Tanto os dispositivos como os utilizadores que interagem com eles através do IoT Hub desconhecem que uma porta de entrada está a mediar as suas comunicações. Esta falta de consciência significa que a porta de entrada é considerada *transparente.* Consulte a [Create a Transparent gateway](how-to-create-transparent-gateway.md) para obter detalhes sobre a utilização de um dispositivo IoT Edge como porta de entrada transparente.
* **Tradução protocolar** – Também conhecido como um padrão de gateway opaco, dispositivos que não suportam MQTT, AMQP ou HTTP podem usar um dispositivo de gateway para enviar dados para ioT Hub em seu nome. O portal compreende o protocolo usado pelos dispositivos a jusante, e é o único dispositivo que tem uma identidade no IoT Hub. Toda a informação parece estar vindo de um dispositivo, o portal. Os dispositivos a jusante devem incorporar informações adicionais de identificação nas suas mensagens se as aplicações em nuvem quiserem analisar os dados numa base por dispositivo. Além disso, os primitivos do IoT Hub como gémeos e métodos só estão disponíveis para o dispositivo gateway, e não para dispositivos a jusante.
* **Tradução de identidade** - Dispositivos que não conseguem ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway. O portal fornece identidade ioT hub e tradução protocolar em nome dos dispositivos a jusante. O portal é inteligente o suficiente para entender o protocolo usado pelos dispositivos a jusante, fornecer-lhes identidade, e traduzir os primitivos do IoT Hub. Dispositivos a jusante aparecem no IoT Hub como dispositivos de primeira classe com gémeos e métodos. Um utilizador pode interagir com os dispositivos no IoT Hub e desconhece o dispositivo de gateway intermédio.

![Diagrama - Padrões transparentes, protocolares e de gateways de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Casos de utilização

Todos os padrões de gateway proporcionam os seguintes benefícios:

* **Analytics at the edge** – Use os serviços de IA localmente para processar dados provenientes de dispositivos a jusante sem enviar telemetria de fidelidade total para a nuvem. Encontre e reaja a insights localmente e envie apenas um subconjunto de dados para o IoT Hub.
* Isolamento a **jusante** do dispositivo – O dispositivo gateway pode proteger todos os dispositivos a jusante da exposição à internet. Pode sentar-se entre uma rede OT que não tem conectividade e uma rede de TI que fornece acesso à web.
* **Ligação multiplexing** - Todos os dispositivos que ligam ao IoT Hub através de um gateway IoT Edge utilizam a mesma ligação subjacente.
* **Alisação de tráfego** - O dispositivo IoT Edge implementará automaticamente backoff exponencial se o IoT Hub acelerar o tráfego, enquanto persiste as mensagens localmente. Este benefício torna a sua solução resiliente a picos de tráfego.
* **Suporte offline** - O dispositivo gateway armazena mensagens e atualizações duplas que não podem ser entregues no IoT Hub.

Um portal que faz a tradução protocolar também pode realizar análises de bordas, isolamento de dispositivos, suavização de tráfego e suporte offline a dispositivos existentes e novos dispositivos que estão limitados de recursos. Muitos dispositivos existentes estão a produzir dados que podem alimentar insights de negócios; no entanto, não foram projetados com conectividade em nuvem em mente. Os gateways opacos permitem que estes dados sejam desbloqueados e utilizados numa solução IoT.

Um portal que faz tradução de identidade proporciona os benefícios da tradução protocolar e permite adicionalmente a plena gestão de dispositivos a jusante a partir da nuvem. Todos os dispositivos da sua solução IoT aparecem no IoT Hub, independentemente do protocolo que utilizam.

## <a name="cheat-sheet"></a>Truques e dicas

Aqui está uma folha de batota rápida que compara os primitivos do IoT Hub quando se usam portas transparentes, opacas (protocolo) e proxy gateways.

| &nbsp; | Gateway transparente | Tradução protocolal | Tradução de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registo de identidade do IoT Hub | Identidades de todos os dispositivos conectados | Apenas a identidade do dispositivo gateway | Identidades de todos os dispositivos conectados |
| Dispositivo duplo | Cada dispositivo conectado tem o seu próprio dispositivo twin | Apenas o portal tem um dispositivo e módulo gémeos | Cada dispositivo conectado tem o seu próprio dispositivo twin |
| Métodos diretos e mensagens cloud-to-device | A nuvem pode abordar cada dispositivo conectado individualmente | A nuvem só pode dirigir-se ao dispositivo gateway | A nuvem pode abordar cada dispositivo conectado individualmente |
| [Aceleradores e quotas do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplicar a cada dispositivo | Aplicar no dispositivo gateway | Aplicar a cada dispositivo |

Ao utilizar um padrão de gateway opaco (tradução protocolar), todos os dispositivos que ligam através desse gateway partilham a mesma fila cloud-to-device, que pode conter no máximo 50 mensagens. Daí resulta que o padrão de gateway opaco só deve ser utilizado quando poucos dispositivos estão a ligar-se através de cada gateway de campo, e o seu tráfego cloud-to-device é baixo.

## <a name="next-steps"></a>Passos seguintes

Aprenda a criar um portal transparente:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
