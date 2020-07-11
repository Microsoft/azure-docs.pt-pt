---
title: Gateways para dispositivos a jusante - Azure IoT Edge / Microsoft Docs
description: Utilize o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou proxy que envia dados de vários dispositivos a jusante para a nuvem ou processa-os localmente.
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
ms.openlocfilehash: d7c924af297d9a315b61351b69d2fe6346bc1178
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232632"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>De que forma um dispositivo IoT Edge pode ser utilizado como gateway

Gateways em soluções IoT Edge fornecem conectividade do dispositivo e análise de bordas para dispositivos IoT que de outra forma não teriam essas capacidades. Azure IoT Edge pode ser usado para satisfazer qualquer necessidade de um gateway IoT, seja relacionado com conectividade, identidade ou análise de borda. Os padrões de gateway neste artigo referem-se apenas às características da conectividade do dispositivo a jusante e da identidade do dispositivo, e não como os dados do dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões

Existem três padrões para a utilização de um dispositivo do IoT Edge como gateway: transparente, tradução de protocolo e tradução de identidade:

* **Transparente** – Os dispositivos que teoricamente poderiam ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway. Os dispositivos a jusante têm as suas próprias identidades do Hub IoT e estão a utilizar quaisquer dos protocolos MQTT, AMQP ou HTTP. O gateway simplesmente transmite as comunicações entre os dispositivos e o Hub IoT. Tanto os dispositivos como os utilizadores que interagem com eles através do IoT Hub desconhecem que um portal está a mediar as suas comunicações. Esta falta de consciência significa que a porta de entrada é considerada *transparente.* Consulte o artigo [Create a transparent gateway](how-to-create-transparent-gateway.md) (Criar um gateway transparente) para ver especificações sobre como utilizar um dispositivo do IoT Edge como um gateway transparente.
* **Tradução protocolar** – Também conhecido como um padrão de gateway opaco, dispositivos que não suportam MQTT, AMQP ou HTTP podem usar um dispositivo de gateway para enviar dados para o IoT Hub em seu nome. O gateway compreende o protocolo utilizado pelos dispositivos a jusante e é o único dispositivo que tem uma identidade no Hub IoT. Toda a informação parece que vem de um dispositivo, o portal. Os dispositivos a jusante devem incorporar informações de identificação adicionais nas suas mensagens se as aplicações na cloud quiserem analisar os dados por dispositivo. Adicionalmente, os primitivos do Hub IoT como duplos e métodos apenas estão disponíveis para o dispositivo do gateway e não para dispositivos a jusante.
* **Tradução de identidade** - Os dispositivos que não conseguem ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway. O gateway fornece a identidade do Hub IoT e a tradução de protocolo em nome dos dispositivos a jusante. O gateway é suficientemente inteligente para compreender o protocolo utilizado pelos dispositivos a jusante, fornecer-lhes uma identidade e traduzir primitivos do Hub IoT. Os dispositivos a jusante aparecem no Hub IoT como dispositivos de primeira classe com duplos e métodos. Um utilizador pode interagir com os dispositivos no Hub IoT e não tem conhecimento do dispositivo do gateway intermédio.

![Diagrama - Padrões transparentes, protocolos e gateways de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Casos de utilização

Todos os padrões de gateway fornecem os seguintes benefícios:

* **Analytics at the edge** – Use serviços de IA localmente para processar dados provenientes de dispositivos a jusante sem enviar telemetria de fidelidade total para a nuvem. Encontre e reaja a insights localmente e envie apenas um subconjunto de dados para o IoT Hub.
* **Isolamento do dispositivo a jusante** – O dispositivo gateway pode proteger todos os dispositivos a jusante da exposição à internet. Pode sentar-se entre uma rede OT que não tem conectividade e uma rede de TI que fornece acesso à web.
* **Multiplexing de ligação** - Todos os dispositivos que se ligam ao IoT Hub através de um gateway IoT Edge utilizam a mesma ligação subjacente.
* **Alisamento do tráfego** - O dispositivo IoT Edge implementará automaticamente o backoff exponencial se o IoT Hub acelerar o tráfego, enquanto persiste as mensagens localmente. Este benefício torna a sua solução resiliente a picos de tráfego.
* **Suporte offline** - O dispositivo gateway armazena mensagens e duas atualizações que não podem ser entregues no IoT Hub.

Um gateway que faça tradução protocolar também pode realizar análises de bordas, isolamento de dispositivos, suavizar o tráfego e suporte offline para dispositivos existentes e novos dispositivos que estão limitados a recursos. Muitos dispositivos existentes estão a produzir dados que podem alimentar insights de negócios; no entanto, não foram projetados com conectividade em nuvem em mente. Gateways opacos permitem que estes dados sejam desbloqueados e utilizados numa solução IoT.

Um gateway que faz a tradução de identidade fornece os benefícios da tradução protocolar e permite ainda uma gestão completa dos dispositivos a jusante a partir da nuvem. Todos os dispositivos da sua solução IoT aparecem no IoT Hub independentemente do protocolo que utilizam.

## <a name="cheat-sheet"></a>Truques e dicas

Aqui está uma folha de batota rápida que compara primitivos IoT Hub ao usar gateways transparentes, opacos (protocolo) e proxy.

| Primitivo | Gateway transparente | Tradução do protocolo | Tradução de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registo de identidade do IoT Hub | Identidades de todos os dispositivos conectados | Apenas a identidade do dispositivo de gateway | Identidades de todos os dispositivos conectados |
| Dispositivo duplo | Cada dispositivo conectado tem o seu próprio dispositivo gémeo | Só o portal tem um dispositivo e gémeos módulos | Cada dispositivo conectado tem o seu próprio dispositivo gémeo |
| Métodos diretos e mensagens nuvem-para-dispositivo | A nuvem pode endereçar cada dispositivo conectado individualmente | A nuvem só pode dirigir-se ao dispositivo gateway | A nuvem pode endereçar cada dispositivo conectado individualmente |
| [Aceleradores e quotas do Hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Aplicar a cada dispositivo | Aplicar no dispositivo gateway | Aplicar a cada dispositivo |

Ao utilizar um padrão de gateway opaco (tradução protocolar), todos os dispositivos que se conectam através desse gateway partilham a mesma fila nuvem-dispositivo, que pode conter no máximo 50 mensagens. Segue-se que o padrão de gateway opaco deve ser utilizado apenas quando poucos dispositivos estão a ligar-se através de cada porta de entrada de campo, e o seu tráfego nuvem-dispositivo é baixo.

## <a name="next-steps"></a>Passos seguintes

Saiba como configurar uma porta de entrada transparente:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
