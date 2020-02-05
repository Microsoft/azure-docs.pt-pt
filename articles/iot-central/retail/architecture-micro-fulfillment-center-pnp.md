---
title: Centro de micro IoT Central do Azure | Microsoft Docs
description: Aprenda a criar um aplicativo de centro de microatendimento usando nosso modelo de aplicativo de microatendimento do centro no IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f752c77a6a62b9b259a8bb1869ca03ff6a19b1f5
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020884"
---
# <a name="micro-fulfillment-center-architecture"></a>Arquitetura do centro do micro-cumprimento

As soluções de microatendimento do centro permitem que você conecte, monitore e gerencie digitalmente todos os aspectos de um centro de preenchimento totalmente automatizado para reduzir os custos, eliminando o tempo de inatividade e aumentando a segurança e a eficiência geral. Estas soluções podem ser construídas utilizando um dos modelos de aplicação dentro da IoT Central e a arquitetura abaixo como orientação.

![Análise do Azure IoT Central Store](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Conjunto de sensores IoT enviando dados de telemetria para um dispositivo de gateway
- Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
- Exportação de dados contínua para o serviço do Azure desejado para manipulação
- Os dados podem ser estruturados no formato desejado e enviados a um serviço de armazenamento
- Os aplicativos de negócios podem consultar dados e gerar informações que reforçam as operações de varejo
 
Vamos dar uma olhada nos principais componentes que geralmente desempenham uma parte em uma solução de microatendimento do centro.

## <a name="robotic-carriers"></a>Operadoras robóticas

Uma solução de centro de micro-realização provavelmente terá um grande conjunto de portadores robóticos gerando diferentes tipos de sinais de telemetria. Esses sinais podem ser ingeridos por um dispositivo de gateway, agregados e enviados para IoT Central conforme refletido pelo lado esquerdo do diagrama de arquitetura.  

## <a name="condition-monitoring-sensors"></a>Sensores de monitoramento de condição

Uma solução de IoT começa com um conjunto de sensores capturando sinais significativos de dentro de seu centro de atendimento. É refletido por diferentes tipos de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos do gateway

Muitos sensores IoT podem alimentar sinais brutos diretamente para a nuvem ou para um dispositivo de gateway localizado perto deles. O dispositivo de gateway executa a agregação de dados na borda antes de enviar informações de resumo para um aplicativo IoT Central. Os dispositivos de gateway também são responsáveis pela retransmissão de operações de comando e controle para os dispositivos do sensor quando aplicável. 

## <a name="iot-central-application"></a>IoT Central aplicativo

A aplicação Azure IoT Central ingere dados de diferentes tipos de sensores IoT, robôs, bem como dispositivos gateway dentro do ambiente do centro de realização e gera um conjunto de insights significativos.

O Azure IoT Central também fornece uma experiência personalizada para o operador Store, permitindo que eles monitorem e gerenciem remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
O aplicativo de IoT Central do Azure dentro de uma solução pode ser configurado para exportar insights brutos ou agregados para um conjunto de serviços de PaaS (plataforma como serviço) do Azure que podem executar a manipulação de dados e enriquecer essas informações antes de provisioná-las em uma empresa aplicativo. 

## <a name="business-application"></a>Aplicativo de negócios
Os dados do IoT podem ser utilizados para alimentar diferentes tipos de aplicações empresariais implantadas num ambiente de retalho. Um gestor de centros de realização ou colaborador pode usar estas aplicações para visualizar insights de negócio e tomar ações significativas em tempo real. Para saber como criar um painel de Power BI em tempo real para sua equipe de varejo, siga o [tutorial](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Passos seguintes
* Introdução ao modelo de aplicativo do [centro de microatendimento](https://aka.ms/checkouttemplate) . 
* Dê uma olhada no [tutorial](https://aka.ms/mfc-tutorial) que o acompanha através de como construir uma solução usando o modelo de aplicação do Micro-Fulfillment Center.
