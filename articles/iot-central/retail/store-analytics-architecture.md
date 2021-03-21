---
title: Arquitetura De Loja Analytics
description: Aprenda a construir uma aplicação de análise na loja usando o modelo de aplicação checkout no IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832203"
---
# <a name="in-store-analytics-architecture"></a>Arquitetura de análise na loja


As soluções de análise na loja permitem monitorizar várias condições dentro do ambiente da loja de retalho. Estas soluções podem ser construídas usando um dos modelos de aplicação dentro da IoT Central e a arquitetura abaixo como orientação.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Conjunto de sensores IoT que enviam dados de telemetria para um dispositivo de gateway
- Dispositivos gateway enviando telemetria e insights agregados para a IoT Central
- Exportação contínua de dados para o serviço Azure desejado para manipulação
- Os dados podem ser estruturados no formato pretendido e enviados para um serviço de armazenamento
- As aplicações empresariais podem consultar dados e gerar insights que alimentam as operações de retalho
 
Vamos dar uma olhada nos componentes-chave que geralmente desempenham um papel numa solução de análise na loja.

## <a name="condition-monitoring-sensors"></a>Sensores de monitorização da condição

Uma solução IoT começa com um conjunto de sensores que captam sinais significativos dentro de um ambiente de loja de retalho. Reflete-se por diferentes tipos de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos gateway

Muitos sensores IoT podem alimentar sinais brutos diretamente para a nuvem ou para um dispositivo de gateway localizado perto deles. O dispositivo gateway executa a agregação de dados na borda antes de enviar insights sumários para uma aplicação IoT Central. Os dispositivos gateway também são responsáveis por transmitir operações de comando e controlo aos dispositivos sensores quando aplicável. 

## <a name="iot-central-application"></a>Aplicação IoT Central

A aplicação Azure IoT Central ingere dados de diferentes tipos de sensores IoT, bem como dispositivos de gateway dentro do ambiente da loja de retalho e gera um conjunto de insights significativos.

O Azure IoT Central também proporciona uma experiência personalizada ao operador da loja, permitindo-lhes monitorizar e gerir remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
A aplicação Azure IoT Central dentro de uma solução pode ser configurada para exportar insights crus ou agregados para um conjunto de serviços Azure PaaS (Platform-as-a Service) que podem realizar manipulação de dados e enriquecer esses insights antes de os aterrar numa aplicação de negócio. 

## <a name="business-application"></a>Aplicação de negócios
Os dados IoT podem ser usados para alimentar diferentes tipos de aplicações empresariais implantadas num ambiente de retalho. Um gerente de loja de retalho ou membro do pessoal pode usar estas aplicações para visualizar insights de negócio e tomar ações significativas em tempo real. Para aprender a construir um dashboard Power BI em tempo real para a sua equipa de retalho, siga o [tutorial.](./tutorial-in-store-analytics-create-app.md)

## <a name="next-steps"></a>Passos seguintes
* Começa com os modelos de aplicação de monitorização da condição [de análise](https://aka.ms/checkouttemplate) na loja e na [loja.](https://aka.ms/conditiontemplate) 
* Dê uma olhada no [tutorial final que](https://aka.ms/storeanalytics-tutorial) o acompanha através de como construir uma solução usando um dos modelos de aplicação In-Store Analytics.
