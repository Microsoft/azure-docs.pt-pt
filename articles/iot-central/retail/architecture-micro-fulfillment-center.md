---
title: Centro central de micro-realização Azure IoT Microsoft Docs
description: Aprenda a construir uma aplicação de centro de micro-realização usando o nosso modelo de aplicação do centro de micro-realização na IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: b0d030240ebe22886826b7a25bd5ca7b8f54e358
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000428"
---
# <a name="micro-fulfillment-center-architecture"></a>Arquitetura do centro de micro-realização

As soluções do centro de micro-realização permitem-lhe conectar, monitorizar e gerir digitalmente todos os aspetos de um centro de realização totalmente automatizado para reduzir custos, eliminando o tempo de inatividade, aumentando a segurança e a eficiência global. Estas soluções podem ser construídas utilizando um dos modelos de aplicação dentro da IoT Central e a arquitetura abaixo como orientação.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Conjunto de sensores IoT enviando dados de telemetria para um dispositivo gateway
- Dispositivos gateway que enviam telemetria e insights agregados para IoT Central
- Exportação contínua de dados para o serviço Azure pretendido para manipulação
- Os dados podem ser estruturados no formato desejado e enviados para um serviço de armazenamento
- As aplicações empresariais podem consultar dados e gerar insights que alimentam as operações de retalho
 
Vamos ver os componentes-chave que geralmente desempenham um papel numa solução de centro de micro-realização.

## <a name="robotic-carriers"></a>Porta-aviões robóticos

Uma solução de centro de micro-realização provavelmente terá um grande conjunto de portadores robóticos gerando diferentes tipos de sinais de telemetria. Estes sinais podem ser ingeridos por um dispositivo de gateway, agregado, e depois enviados para a IoT Central, conforme refletido pelo lado esquerdo do diagrama de arquitetura.  

## <a name="condition-monitoring-sensors"></a>Sensores de monitorização de condições

Uma solução IoT começa com um conjunto de sensores que captam sinais significativos dentro do seu centro de realização. É refletido por diferentes tipos de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos gateway

Muitos sensores IoT podem alimentar sinais crus diretamente na nuvem ou num dispositivo de gateway localizado perto deles. O dispositivo gateway executa a agregação de dados na borda antes de enviar insights sumários para uma aplicação IoT Central. Os dispositivos gateway também são responsáveis pela transmissão de operações de comando e controlo aos dispositivos de sensor, quando aplicável. 

## <a name="iot-central-application"></a>Aplicação IoT Central

A aplicação Azure IoT Central ingere dados de diferentes tipos de sensores IoT, robôs, bem como dispositivos gateway dentro do ambiente do centro de realização e gera um conjunto de insights significativos.

A Azure IoT Central também oferece uma experiência personalizada ao operador da loja, permitindo-lhes monitorizar e gerir remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
A aplicação Azure IoT Central dentro de uma solução pode ser configurada para exportar insights brutos ou agregados para um conjunto de serviços Azure PaaS (Plataforma-as-a-Service) que podem realizar manipulação de dados e enriquecer esses insights antes de os aterrar numa aplicação de negócio. 

## <a name="business-application"></a>Aplicação de negócios
Os dados do IoT podem ser utilizados para alimentar diferentes tipos de aplicações empresariais implantadas num ambiente de retalho. Um gestor de centros de realização ou colaborador pode usar estas aplicações para visualizar insights de negócio e tomar ações significativas em tempo real. Para aprender a construir um dashboard Power BI em tempo real para a sua equipa de retalho, siga o [tutorial](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Passos seguintes
* Inicie-se com o modelo de aplicação do [Centro de Micro-realização.](https://aka.ms/checkouttemplate) 
* Dê uma olhada no [tutorial](https://aka.ms/mfc-tutorial) que o acompanha através de como construir uma solução usando o modelo de aplicação do Micro-Fulfillment Center.
