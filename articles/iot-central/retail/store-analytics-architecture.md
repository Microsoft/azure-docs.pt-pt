---
title: Arquitetura de Análise de Loja
description: Aprenda a construir uma aplicação de análise na loja usando o modelo de aplicação checkout na IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f1f83fdd73816e6e30c5cac7d193719591bb8dc1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999024"
---
# <a name="in-store-analytics-architecture"></a>Arquitetura de análise na loja



As soluções de análise na loja permitem monitorizar várias condições no ambiente da loja de retalho. Estas soluções podem ser construídas utilizando um dos modelos de aplicação dentro da IoT Central e a arquitetura abaixo como orientação.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Conjunto de sensores IoT enviando dados de telemetria para um dispositivo gateway
- Dispositivos gateway que enviam telemetria e insights agregados para IoT Central
- Exportação contínua de dados para o serviço Azure pretendido para manipulação
- Os dados podem ser estruturados no formato desejado e enviados para um serviço de armazenamento
- As aplicações empresariais podem consultar dados e gerar insights que alimentam as operações de retalho
 
Vamos ver os componentes-chave que geralmente desempenham um papel numa solução de análise na loja.

## <a name="condition-monitoring-sensors"></a>Sensores de monitorização de condições

Uma solução IoT começa com um conjunto de sensores que captam sinais significativos dentro de um ambiente de loja de retalho. Reflete-se por diferentes tipos de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos gateway

Muitos sensores IoT podem alimentar sinais crus diretamente na nuvem ou num dispositivo de gateway localizado perto deles. O dispositivo gateway executa a agregação de dados na borda antes de enviar insights sumários para uma aplicação IoT Central. Os dispositivos gateway também são responsáveis pela transmissão de operações de comando e controlo aos dispositivos de sensor, quando aplicável. 

## <a name="iot-central-application"></a>Aplicação IoT Central

A aplicação Azure IoT Central ingere dados de diferentes tipos de sensores IoT, bem como dispositivos gateway dentro do ambiente de loja de retalho e gera um conjunto de insights significativos.

A Azure IoT Central também oferece uma experiência personalizada ao operador da loja, permitindo-lhes monitorizar e gerir remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
A aplicação Azure IoT Central dentro de uma solução pode ser configurada para exportar insights brutos ou agregados para um conjunto de serviços Azure PaaS (Plataforma-as-a-a Service) que podem realizar manipulação de dados e enriquecer esses insights antes de os aterrar numa aplicação de negócio. 

## <a name="business-application"></a>Aplicação de negócios
Os dados do IoT podem ser utilizados para alimentar diferentes tipos de aplicações empresariais implantadas num ambiente de retalho. Um gerente de loja de retalho ou membro do staff pode usar estas aplicações para visualizar insights de negócio e tomar ações significativas em tempo real. Para aprender a construir um dashboard Power BI em tempo real para a sua equipa de retalho, siga o [tutorial](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Passos seguintes
* Inicie-se com os modelos de aplicação de [monitorização](https://aka.ms/checkouttemplate) de condições de análise na loja e na [loja.](https://aka.ms/conditiontemplate) 
* Dê uma olhada no [tutorial de ponta a ponta](https://aka.ms/storeanalytics-tutorial) que o acompanha através de como construir uma solução usando um dos modelos de aplicação In-Store Analytics.
