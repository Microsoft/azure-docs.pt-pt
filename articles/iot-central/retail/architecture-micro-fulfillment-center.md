---
title: Centro de micro-realização Azure IoT Central | Microsoft Docs
description: Aprenda a construir uma aplicação de centro de micro-realização usando o nosso modelo de aplicação do centro de micro-realização no IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 23e1880a3abac6ebda71eaa36276d2bf5815048e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833784"
---
# <a name="micro-fulfillment-center-architecture"></a>Arquitetura do centro de micro-realização

As soluções do centro de micro-realização permitem conectar, monitorizar e gerir digitalmente todos os aspetos de um centro de realização totalmente automatizado para reduzir custos, eliminando o tempo de inatividade, aumentando a segurança e a eficiência global. Estas soluções podem ser construídas usando um dos modelos de aplicação dentro da IoT Central e a arquitetura abaixo como orientação.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. Conjunto de sensores IoT que enviam dados de telemetria para um dispositivo de gateway
2. Dispositivos gateway enviando telemetria e insights agregados para a IoT Central
3. Exportação contínua de dados para o serviço Azure desejado para manipulação
4. Os dados podem ser estruturados no formato pretendido e enviados para um serviço de armazenamento
5. As aplicações empresariais podem consultar dados e gerar insights que alimentam as operações de retalho
 
Vamos dar uma olhada nos componentes chave que geralmente desempenham um papel numa solução de centro de micro-realização.

## <a name="robotic-carriers"></a>Portadores robóticos

Uma solução de centro de micro-realização provavelmente terá um grande conjunto de portadores robóticos gerando diferentes tipos de sinais de telemetria. Estes sinais podem ser ingeridos por um dispositivo de gateway, agregado, e depois enviados para a IoT Central, como refletido pelo lado esquerdo do diagrama da arquitetura.  

## <a name="condition-monitoring-sensors"></a>Sensores de monitorização da condição

Uma solução IoT começa com um conjunto de sensores que captam sinais significativos dentro do seu centro de realização. É refletida por diferentes tipos de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos gateway

Muitos sensores IoT podem alimentar sinais brutos diretamente para a nuvem ou para um dispositivo de gateway localizado perto deles. O dispositivo gateway executa a agregação de dados na borda antes de enviar insights sumários para uma aplicação IoT Central. Os dispositivos gateway também são responsáveis por transmitir operações de comando e controlo aos dispositivos sensores quando aplicável. 

## <a name="iot-central-application"></a>Aplicação IoT Central

A aplicação Azure IoT Central ingere dados de diferentes tipos de sensores IoT, robôs, bem como dispositivos de gateway dentro do ambiente do centro de realização e gera um conjunto de insights significativos.

O Azure IoT Central também proporciona uma experiência personalizada ao operador da loja, permitindo-lhes monitorizar e gerir remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
A aplicação Azure IoT Central dentro de uma solução pode ser configurada para exportar insights crus ou agregados para um conjunto de serviços Azure PaaS (Platform-as-a-Service) que podem realizar manipulação de dados e enriquecer esses insights antes de aterrar numa aplicação de negócio. 

## <a name="business-application"></a>Aplicação de negócios
Os dados IoT podem ser usados para alimentar diferentes tipos de aplicações empresariais implantadas num ambiente de retalho. Um gestor ou colaborador do centro de realização pode usar estas aplicações para visualizar insights de negócio e tomar ações significativas em tempo real. Para aprender a construir um dashboard Power BI em tempo real para a sua equipa de retalho, siga o [tutorial.](./tutorial-in-store-analytics-create-app.md)

## <a name="next-steps"></a>Passos seguintes
* Começa com o modelo de aplicação [do Centro de Micro-realização.](https://aka.ms/checkouttemplate) 
* Veja o [tutorial](https://aka.ms/mfc-tutorial) que o acompanha como construir uma solução usando o modelo de aplicação do Centro de Micro-realização.
