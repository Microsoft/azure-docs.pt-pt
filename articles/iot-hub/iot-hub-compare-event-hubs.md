---
title: Comparar o Hub IoT do Azure para os Hubs de eventos do Azure | Documentos da Microsoft
description: Uma comparação dos serviços do IoT Hub e o Azure de Hubs de eventos, realçando as diferenças funcionais e de casos de utilização. A comparação inclui protocolos suportados, gestão de dispositivos, monitorização, e carrega o ficheiro.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669751"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Ligar dispositivos IoT para o Azure: O Hub IoT e dos Hubs de eventos

O Azure oferece serviços especificamente desenvolvidos para diversos tipos de conectividade e comunicação para o ajudar a ligar os seus dados para o poder da cloud. Tanto o IoT Hub do Azure como o Event Hubs do Azure são serviços em nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados informações empresariais. Os dois serviços são semelhantes em que ambos dão suporte a ingestão de dados com baixa latência e alta fiabilidade, mas eles foram criados para diferentes fins. IoT Hub foi desenvolvido para abordar os requisitos exclusivos da ligação de dispositivos de IoT na cloud do Azure, enquanto os Hubs de eventos foi concebido para grandes volumes de dados de transmissão em fluxo. A Microsoft recomenda utilizar o IoT Hub do Azure para ligar dispositivos IoT para o Azure

O IoT Hub do Azure é o gateway de cloud que liga dispositivos de IoT para recolher dados e informações de negócio de unidade e automatização. Além disso, o IoT Hub inclui funcionalidades que enriquecer a relação entre os dispositivos e de seus sistemas de back-end. Capacidades de comunicação bidirecional significam que, enquanto a receber dados dos dispositivos também pode enviar comandos e fazer uma cópia de políticas para dispositivos. Por exemplo, use as mensagens de cloud-para-dispositivo para atualizar as propriedades ou invocar ações de gestão do dispositivo. Comunicação de cloud-para-dispositivo também lhe permite enviar inteligência da cloud para os seus dispositivos edge com o Azure IoT Edge. A identidade ao nível do dispositivo exclusiva fornecido pelo IoT Hub ajuda proteger melhor sua solução de IoT de potenciais ataques. 

[Os Hubs de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) é os serviço do Azure de transmissão em fluxo grandes quantidades de dados. Ele é projetado para cenários de transmissão em fluxo de dados de alto débito em que os clientes podem enviar milhares de milhões de pedidos por dia. Os Hubs de eventos utiliza um modelo de consumidor particionado para ampliar a sua transmissão em fluxo e é integrado a grandes volumes de dados e serviços de análise do Azure, incluindo o Databricks, o Stream Analytics, o ADLS e o HDInsight. Com funcionalidades como a captura de Hubs de eventos e ampliação automática, este serviço foi concebido para suportar as suas aplicações de macrodados e soluções. Além disso, o IoT Hub utiliza os Hubs de eventos para o seu caminho de fluxo de telemetria, para que a sua solução IoT também é beneficiado com a enorme potência dos Hubs de eventos.

Para resumir, ambas as soluções foram concebidas para ingestão de dados em grande escala. Apenas o IoT Hub fornece as IoT específico capacidades avançadas que foram concebidas para que possa maximizar o valor comercial de ligar os seus dispositivos de IoT na cloud do Azure.  Se o seu percurso na IoT está apenas começando, a partir do IoT Hub para suportar os seus cenários de ingestão de dados irá garantir que tem acesso imediato a recursos de IoT completa assim que as suas necessidades comerciais e técnicas necessitem.

A tabela seguinte fornece detalhes sobre como as duas camadas do IoT Hub comparam com os Hubs de eventos quando estiver a avaliar para recursos de IoT. Para obter mais informações sobre os escalões básicos e standard do IoT Hub, veja [como escolher o escalão certo do IoT Hub](iot-hub-scaling.md).

| Capacidade de IoT | Escalão standard do IoT Hub | Escalão básico do IoT Hub | Hubs de Eventos |
| --- | --- | --- | --- |
| Mensagens do dispositivo para a cloud | ![Marcar][checkmark] | ![Marcar][checkmark] | ![Marcar][checkmark] |
| Protocolos: AMQP de HTTPS, AMQP, através de webSockets | ![Marcar][checkmark] | ![Marcar][checkmark] | ![Marcar][checkmark] |
| Protocolos: MQTT, MQTT ao longo de webSockets | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Identidade por dispositivo | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Carregamento de ficheiros a partir de dispositivos | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Serviço de Aprovisionamento do Dispositivo | ![Marcar][checkmark] | ![Marcar][checkmark] |  |
| Mensagens cloud para dispositivo | ![Marcar][checkmark] |  |  |
| Dispositivo duplo e gestão de dispositivos | ![Marcar][checkmark] |  |  |
| Fluxos de dispositivo (pré-visualização) | ![Marcar][checkmark] |  |  |
| IoT Edge | ![Marcar][checkmark] |  |  |

Mesmo que o caso de utilização única é ingestão de dados do dispositivo para a cloud, é altamente recomendável através do IoT Hub, pois fornece um serviço que foi concebido para conectividade do dispositivo IoT. 

### <a name="next-steps"></a>Passos Seguintes

Para explorar ainda mais os recursos do IoT Hub, veja a [Guia do programador do IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
