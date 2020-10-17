---
title: Compare O Hub Azure IoT com o Azure Event Hubs Microsoft Docs
description: Uma comparação entre os serviços IoT Hub e Event Hubs Azure destacando diferenças funcionais e casos de utilização. A comparação inclui protocolos suportados, gestão de dispositivos, monitorização e uploads de ficheiros.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 56bb179c50862f09f1b789c359db97976017bbb5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142759"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Dispositivos IoT de ligação ao Azure: Hub IoT e Centros de Eventos

O Azure fornece serviços especificamente desenvolvidos para diversos tipos de conectividade e comunicação para ajudá-lo a ligar os seus dados à potência da nuvem. Tanto o Azure IoT Hub como o Azure Event Hubs são serviços na nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para insights de negócios. Os dois serviços são semelhantes na medida em que ambos apoiam a ingestão de dados com baixa latência e elevada fiabilidade, mas são concebidos para diferentes finalidades. O IoT Hub foi desenvolvido para responder aos requisitos únicos de ligação de dispositivos IoT à nuvem Azure, enquanto o Event Hubs foi projetado para o streaming de big data. Microsoft recomenda usar O Hub IoT do Azure para ligar dispositivos IoT ao Azure

O Azure IoT Hub é o gateway de nuvem que conecta dispositivos IoT para recolher dados e impulsionar insights de negócios e automação. Além disso, o IoT Hub inclui funcionalidades que enriquecem a relação entre os seus dispositivos e os seus sistemas de backend. As capacidades de comunicação bidis significam que, ao receber dados de dispositivos, também pode enviar comandos e políticas para os dispositivos. Por exemplo, utilize mensagens nuvem-dispositivo para atualizar propriedades ou invocar ações de gestão de dispositivos. A comunicação nuvem-para-dispositivo também permite enviar inteligência em nuvem para os seus dispositivos de borda com Azure IoT Edge. A identidade única ao nível do dispositivo fornecida pelo IoT Hub ajuda a proteger melhor a sua solução IoT de potenciais ataques. 

[Azure Event Hubs](../event-hubs/event-hubs-about.md) é o grande serviço de streaming de dados do Azure. Foi concebido para cenários de transmissão de dados em fluxo com elevado débito, em que os clientes poderão enviar milhares de milhões de pedidos por dia. O Hubs de Eventos utiliza um modelo de consumidor de partição para ampliar o seu fluxo e está integrado nos serviços de análise e macrodados do Azure, incluindo o Databricks, Stream Analytics, ADLS e HDInsight. Com funcionalidades como Event Hubs Capture e Auto-Inflate, este serviço foi concebido para suportar as suas grandes aplicações e soluções de dados. Além disso, o IoT Hub utiliza os Centros de Eventos para o seu caminho de fluxo de telemetria, pelo que a sua solução IoT também beneficia do tremendo poder dos Centros de Eventos.

Resumindo, ambas as soluções são concebidas para a ingestão de dados em larga escala. Apenas o IoT Hub fornece as ricas capacidades específicas do IoT que são projetadas para você maximizar o valor do negócio de ligar os seus dispositivos IoT à nuvem Azure.  Se a sua jornada IoT está apenas a começar, a começar pelo IoT Hub para suportar os seus cenários de ingestão de dados irá garantir que tem acesso instantâneo às capacidades IoT completas assim que o seu negócio e as suas necessidades técnicas os exijam.

A tabela seguinte fornece detalhes sobre como os dois níveis de IoT Hub se comparam aos Centros de Eventos quando os está a avaliar para capacidades de IoT. Para obter mais informações sobre os níveis padrão e básicos do IoT Hub, consulte [Como escolher o nível IoT Hub certo](iot-hub-scaling.md).

| Capacidade IoT | Nível padrão IoT Hub | IoT Hub nível básico | Hubs de Eventos |
| --- | --- | --- | --- |
| Mensagens dispositivo-a-nuvem | ![Marcar][checkmark] | ![Marcar][checkmark] | ![Marcar][checkmark] |
| Protocolos: HTTPS, AMQP, AMQP over webSockets | ![Marcar][checkmark] | ![Marcar][checkmark] | ![Marcar][checkmark] |
| Protocolos: MQTT, MQTT sobre webSockets | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Identidade por dispositivo | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Upload de ficheiros a partir de dispositivos | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Serviço de Aprovisionamento do Dispositivos | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Mensagens nuvem-para-dispositivo | ![Marcar][checkmark] |  |  |
| Gestão de dispositivos twin e dispositivo | ![Marcar][checkmark] |  |  |
| Fluxos de dispositivos (pré-visualização) | ![Marcar][checkmark] |  |  |
| IoT Edge | ![Marcar][checkmark] |  |  |

Mesmo que o único caso de utilização seja a ingestão de dados dispositivo-a-nuvem, recomendamos vivamente a utilização do IoT Hub, uma vez que fornece um serviço concebido para a conectividade do dispositivo IoT. 

### <a name="next-steps"></a>Passos seguintes

Para explorar ainda mais as capacidades do IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png