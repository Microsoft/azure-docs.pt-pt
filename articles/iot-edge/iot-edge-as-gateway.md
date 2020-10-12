---
title: Gateways para dispositivos a jusante - Azure IoT Edge / Microsoft Docs
description: Utilize o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou proxy que envia dados de vários dispositivos a jusante para a nuvem ou processa-os localmente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017027"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>De que forma um dispositivo IoT Edge pode ser utilizado como gateway

Gateways em soluções IoT Edge fornecem conectividade do dispositivo e análise de bordas para dispositivos IoT que de outra forma não teriam essas capacidades. Azure IoT Edge pode ser usado para satisfazer qualquer necessidade de um gateway IoT, seja relacionado com conectividade, identidade ou análise de borda. Os padrões de gateway neste artigo referem-se apenas às características da conectividade do dispositivo a jusante e da identidade do dispositivo, e não como os dados do dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões

Existem três padrões para a utilização de um dispositivo IoT Edge como porta de entrada: tradução transparente, protocolar e tradução de identidade.

Uma diferença fundamental entre os padrões é que um gateway transparente transmite mensagens entre dispositivos a jusante e IoT Hub sem precisar de qualquer processamento adicional. No entanto, a tradução protocolar e a tradução de identidade requerem o processamento no portal para permitir a comunicação.

Qualquer gateway pode utilizar módulos IoT Edge para realizar análises ou pré-processamento na borda antes de passar mensagens de dispositivos a jusante para o IoT Hub.

![Diagrama - Padrões transparentes, protocolos e gateways de identidade](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Padrão transparente

Num padrão *transparente* de gateway, os dispositivos que teoricamente poderiam ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway. Os dispositivos a jusante têm as suas próprias identidades do Hub IoT e estão a utilizar quaisquer dos protocolos MQTT, AMQP ou HTTP. O gateway simplesmente transmite as comunicações entre os dispositivos e o Hub IoT. Tanto os dispositivos como os utilizadores que interagem com eles através do IoT Hub desconhecem que um portal está a mediar as suas comunicações. Esta falta de consciência significa que a porta de entrada é considerada *transparente.*

O tempo de execução IoT Edge inclui capacidades de gateway transparentes. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para funcionar como um gateway transparente.](how-to-create-transparent-gateway.md)

### <a name="protocol-translation-pattern"></a>Padrão de tradução de protocolo

Um portal de *tradução protocolar* também é conhecido como um gateway *opaco,* em contraste com o padrão transparente de gateway. Neste padrão, os dispositivos que não suportam MQTT, AMQP ou HTTP podem utilizar um dispositivo de gateway para enviar dados para o IoT Hub em seu nome. O gateway compreende o protocolo utilizado pelos dispositivos a jusante e é o único dispositivo que tem uma identidade no Hub IoT. Toda a informação parece que vem de um dispositivo, o portal. Os dispositivos a jusante devem incorporar informações de identificação adicionais nas suas mensagens se as aplicações na cloud quiserem analisar os dados por dispositivo. Adicionalmente, os primitivos do Hub IoT como duplos e métodos apenas estão disponíveis para o dispositivo do gateway e não para dispositivos a jusante.

O tempo de execução IoT Edge não inclui capacidades de tradução de protocolos. Este padrão requer módulos personalizados ou de terceiros que são frequentemente específicos do hardware e protocolo utilizados. [O Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contém vários módulos de tradução protocolar para escolher.

### <a name="identity-translation-pattern"></a>Padrão de tradução de identidade

Num padrão de gateway *de tradução de identidade,* os dispositivos que não conseguem ligar-se ao IoT Hub podem ligar-se a um dispositivo de gateway, em vez disso. O gateway fornece a identidade do Hub IoT e a tradução de protocolo em nome dos dispositivos a jusante. O gateway é suficientemente inteligente para compreender o protocolo utilizado pelos dispositivos a jusante, fornecer-lhes uma identidade e traduzir primitivos do Hub IoT. Os dispositivos a jusante aparecem no Hub IoT como dispositivos de primeira classe com duplos e métodos. Um utilizador pode interagir com os dispositivos no Hub IoT e não tem conhecimento do dispositivo do gateway intermédio.

O tempo de execução IoT Edge não inclui capacidades de tradução de identidade. Este padrão requer módulos personalizados ou de terceiros que são frequentemente específicos do hardware e protocolo utilizados. Para obter uma amostra que utilize o padrão de tradução de identidade, consulte [o Kit de Arranque Azure Edge LoRaWAN](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="use-cases"></a>Casos de utilização

Todos os padrões de gateway fornecem os seguintes benefícios:

* **Analytics at the edge** – Use serviços de IA localmente para processar dados provenientes de dispositivos a jusante sem enviar telemetria de fidelidade total para a nuvem. Encontre e reaja a insights localmente e envie apenas um subconjunto de dados para o IoT Hub.
* **Isolamento do dispositivo a jusante** – O dispositivo gateway pode proteger todos os dispositivos a jusante da exposição à internet. Pode sentar-se entre uma rede OT que não tem conectividade e uma rede de TI que fornece acesso à web.
* **Multiplexing de ligação** - Todos os dispositivos que se ligam ao IoT Hub através de um gateway IoT Edge utilizam a mesma ligação subjacente.
* **Alisamento do tráfego** - O dispositivo IoT Edge implementará automaticamente o backoff exponencial se o IoT Hub acelerar o tráfego, enquanto persiste as mensagens localmente. Este benefício torna a sua solução resiliente a picos de tráfego.
* **Suporte offline** - O dispositivo gateway armazena mensagens e duas atualizações que não podem ser entregues no IoT Hub.

Um gateway que faça tradução protocolar pode suportar dispositivos existentes e novos dispositivos que estão limitados a recursos. Muitos dispositivos existentes estão a produzir dados que podem alimentar insights de negócios; no entanto, não foram projetados com conectividade em nuvem em mente. Gateways opacos permitem que estes dados sejam desbloqueados e utilizados numa solução IoT.

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

Aprenda os três passos para configurar uma porta de entrada transparente:

* [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo a jusante no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Ligar um dispositivo a jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
