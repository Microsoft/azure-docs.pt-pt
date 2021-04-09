---
title: Introdução ao Hub IoT do Azure | Microsoft Docs
description: Saiba mais sobre o Hub IoT do Azure. Este serviço de IoT foi concebido para ingestão de dados, gestão de dispositivos e segurança dimensionáveis.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 86a373844b370cc9f9ce31dc65b2039a81279803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454775"
---
# <a name="what-is-azure-iot-hub"></a>O que é o IoT Hub do Azure?

O Hub IoT é um serviço gerido, alojado na cloud, que atua como um centro de mensagens para assegurar a comunicação bidirecional entre a aplicação IoT e os dispositivos que esta gere. Pode utilizar o Hub IoT do Azure para criar soluções de IoT com comunicações fiáveis e seguras entre milhões de dispositivos IoT e um back-end de solução alojado na cloud. Pode ligar praticamente qualquer dispositivo ao Hub IoT.

O Hub IoT suporta comunicações tanto do dispositivo para a cloud como da cloud para o dispositivo. O Hub IoT suporta múltiplos padrões de mensagens, tais como telemetria dispositivo para cloud, carregamento de ficheiros a partir de dispositivos e métodos de pedido-resposta, para controlar os seus dispositivos a partir da cloud. A monitorização do Hub IoT ajuda a manter o bom estado de funcionamento da sua solução através do controlo de eventos, tais como a criação de dispositivos, falhas de dispositivo e ligações de dispositivos.

O Hub IoT ajuda-o a criar soluções de IoT dimensionáveis e com todas as funcionalidades, como a gestão de equipamento industrial utilizado em fabrico, o controlo de recursos importantes na área dos cuidados de saúde e a monitorização da forma como um edifício de escritórios é utilizado.

## <a name="scale-your-solution"></a>Dimensionar a sua solução

O Hub IoT dimensiona milhões de dispositivos ligados em simultâneo e milhões de eventos por segundo para suportar as suas cargas de trabalho de IoT. Para obter mais informações sobre o dimensionamento do seu Hub IoT, consulte [ioT Hub Scaling.](iot-hub-scaling.md) Para saber mais sobre os múltiplos níveis de serviço oferecidos pelo IoT Hub e como melhor se adequa às suas necessidades de escalabilidade, consulte a [página de preços.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="secure-your-communications"></a>Comunicações protegidas

O Hub IoT dá-lhe um canal de comunicação seguro através do qual os seus dispositivos podem enviar dados.

* A autenticação por dispositivo permite que cada dispositivo se ligue com segurança ao Hub IoT e que cada dispositivo seja gerido de forma segura.

* Tem controlo total sobre o acesso do dispositivo e pode controlar as ligações ao nível por dispositivo.

* O [Serviço Aprovisionamento de Dispositivos no Hub IoT](../iot-dps/index.yml) aprovisiona automaticamente dispositivos ao hub IoT certo quando o dispositivo arranca pela primeira vez.

* Vários tipos de autenticação suportam uma variedade de funcionalidades do dispositivo:

  * Autenticação baseada em token de SAS para que comece rapidamente a utilizar a sua solução de IoT.

  * Autenticação de certificado X.509 individual para assegurar uma autenticação segura baseada em normas.

  * Autenticação X.509 de AC para garantir uma inscrição simples baseada em normas.

## <a name="route-device-data"></a>Encaminhar dados de dispositivo

A funcionalidade de encaminhamento de mensagens incorporada dá-lhe a flexibilidade de que precisa para configurar fan-out automático de mensagens baseado em regras:

* Utilize [o encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md) para controlar onde o seu hub envia telemetria do dispositivo.

* Não incorre em custos adicionais para encaminhar mensagens para múltiplos pontos finais.

* As regras de encaminhamento sem código substituem o código do distribuidor de mensagens personalizado.

## <a name="integrate-with-other-services"></a>Integrar noutros serviços

Pode integrar o Hub IoT noutros serviços do Azure para criar soluções ponto a ponto completas. Por exemplo, utilize:

* O [Azure Event Grid](../event-grid/index.yml) para permitir que a sua empresa reaja rapidamente a eventos críticos de forma segura, fiável e dimensionável.

* O [Azure Logic Apps](../logic-apps/index.yml) para automatizar os processos de negócio.

* O [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) para adicionar a aprendizagem automática e modelos de IA à sua solução.

* O [Azure Stream Analytics](../stream-analytics/index.yml) para executar cálculos de análise em tempo real sobre a transmissão de dados a partir dos seus dispositivos.

## <a name="configure-and-control-your-devices"></a>Configurar e controlar os dispositivos

Pode gerir os dispositivos ligados ao Hub IoT com uma matriz de funcionalidade incorporada.

* Armazene, sincronize e consulte metadados e informações de estado de dispositivo de todos os seus dispositivos.

* Defina o estado do dispositivo por dispositivo individual ou com base nas características comuns dos dispositivos.

* Responda automaticamente a uma alteração de estado comunicada pelo dispositivo com a integração do encaminhamento de mensagens.

## <a name="make-your-solution-highly-available"></a>Tornar a solução altamente disponível

Existe um [Contrato de Nível de Serviço para o Hub IoT](https://azure.microsoft.com/support/legal/sla/iot-hub/) de 99,9%. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/) completo explica a disponibilidade garantida do Azure em termos globais.

## <a name="connect-your-devices"></a>Ligar os dispositivos

Utilize as bibliotecas do [Azure IoT Device SDK](./iot-hub-devguide-sdks.md) para criar aplicações que são executadas nos seus dispositivos e que interagem com o Hub IoT. As plataformas suportadas incluem múltiplas distribuições do Linux, Windows e sistemas operativos em tempo real. As linguagens suportadas incluem:

* C
* Incorporado C
* C#
* Java
* Python
* Node.js.

O Hub IoT e os SDKs de dispositivo suportam os seguintes protocolos para ligar dispositivos:

* HTTPS
* AMQP
* AMQP através de WebSockets
* MQTT
* MQTT através de WebSockets

O IoT Hub e o dispositivo SDKs suportam as convenções [Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) para dispositivos de ligação. Os dispositivos IoT Plug e Play utilizam um modelo de dispositivo para anunciar as suas capacidades para aplicações IoT Plug e Play. O modelo do dispositivo permite aos construtores de soluções integrar dispositivos inteligentes com as suas soluções sem qualquer configuração manual.

Se a solução não conseguir utilizar as bibliotecas do dispositivo, os dispositivos podem utilizar os protocolos MQTT v3.1.1, HTTPS 1.1 ou AMQP 1.0 para ligar de forma nativa ao seu hub.

Se a sua solução não conseguir utilizar um dos protocolos suportados, pode expandir o Hub IoT para suportar protocolos personalizados:

* Utilize o [Azure IoT Edge](../iot-edge/index.yml) para criar um gateway de campo para fazer a tradução de protocolos na periferia.

* Personalize o [gateway de protocolo do Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) para fazer a tradução de protocolos na cloud.

## <a name="quotas-and-limits"></a>Quotas e limites

Cada subscrição do Azure tem limites de quota predefinidos para evitar o abuso do serviço, pelo que estes limites podem afetar o âmbito da sua solução de IoT. O limite atual numa base por subscrição é de 50 hubs IoT por subscrição. Pode contactar o suporte para pedir o aumento da quota. Para mais informações, consulte [IoT Hub Quotas e Throttling.](iot-hub-devguide-quotas-throttling.md) Para obter mais informações sobre os limites de quota, consulte um dos seguintes artigos:

* [Limites de serviço de subscrição Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [A limitação do Hub IoT e o que significa para si](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>Hub IoT no Azure Stack Hub (pré-visualização)

O IoT Hub no Azure Stack Hub (pré-visualização) permite-lhe criar soluções híbridas de IoT. O IoT Hub é um serviço gerido, funcionando como um centro de mensagens centrais para a comunicação bidicional entre a sua aplicação IoT e os dispositivos que gere. Você pode usar o IoT Hub no Azure Stack Hub para construir soluções IoT com comunicações confiáveis e seguras entre dispositivos IoT e suas soluções no local.

O IoT Hub no Azure Stack Hub é gratuito durante a pré-visualização pública. Para mais informações, consulte o [IoT Hub na visão geral do Azure Stack Hub](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Passos seguintes

Para experimentar uma solução de IoT ponto a ponto, veja os guias de início rápido do Hub IoT:

* [Guia de Início Rápido: Enviar telemetria a partir de um dispositivo para um hub IoT](quickstart-send-telemetry-node.md)

Para saber mais sobre as formas de construir e implementar soluções IoT com Azure IoT, visite:

* [Fundamentos: Tecnologias e soluções Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).