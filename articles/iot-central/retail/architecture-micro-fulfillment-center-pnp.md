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
ms.openlocfilehash: 35f99abaf5e0142c29d6dd43c968b66b21a28a50
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75899217"
---
# <a name="micro-fulfillment-center-architecture"></a>Arquitetura do centro do micro-cumprimento

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

As soluções de microatendimento do centro permitem que você conecte, monitore e gerencie digitalmente todos os aspectos de um centro de preenchimento totalmente automatizado para reduzir os custos, eliminando o tempo de inatividade e aumentando a segurança e a eficiência geral. Essas soluções podem ser criadas aproveitando um dos modelos de aplicativo no IoT Central e a arquitetura abaixo como orientação.

![Análise do Azure IoT Central Store](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Conjunto de sensores IoT enviando dados de telemetria para um dispositivo de gateway
- Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
- Exportação de dados contínua para o serviço do Azure desejado para manipulação
- Os dados podem ser estruturados no formato desejado e enviados a um serviço de armazenamento
- Os aplicativos de negócios podem consultar dados e gerar informações que reforçam as operações de varejo
 
Vamos dar uma olhada nos principais componentes que geralmente desempenham uma parte em uma solução de microatendimento do centro.

## <a name="robotic-carriers"></a>Operadoras robóticas

Uma solução de microatendimento do centro provavelmente terá um grande conjunto de operadoras de robótica gerando uma variedade de sinais de telemetria. Esses sinais podem ser ingeridos por um dispositivo de gateway, agregados e enviados para IoT Central conforme refletido pelo lado esquerdo do diagrama de arquitetura.  

## <a name="condition-monitoring-sensors"></a>Sensores de monitoramento de condição

Uma solução de IoT começa com um conjunto de sensores capturando sinais significativos de dentro de seu centro de atendimento. Ele é refletido por uma variedade de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos do gateway

Muitos sensores IoT podem alimentar sinais brutos diretamente para a nuvem ou para um dispositivo de gateway localizado perto deles. O dispositivo de gateway executa a agregação de dados na borda antes de enviar informações de resumo para um aplicativo IoT Central. Os dispositivos de gateway também são responsáveis pela retransmissão de operações de comando e controle para os dispositivos do sensor quando aplicável. 

## <a name="iot-central-application"></a>IoT Central aplicativo

O aplicativo de IoT Central do Azure ingere dados de uma variedade de sensores IoT, robôs, bem como dispositivos de gateway dentro do ambiente de centro de atendimento e gera um conjunto de ideias significativas.

O Azure IoT Central também fornece uma experiência personalizada para o operador Store, permitindo que eles monitorem e gerenciem remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
O aplicativo de IoT Central do Azure dentro de uma solução pode ser configurado para exportar insights brutos ou agregados para um conjunto de serviços de PaaS (plataforma como serviço) do Azure que podem executar a manipulação de dados e enriquecer essas informações antes de provisioná-las em uma empresa aplicativo. 

## <a name="business-application"></a>Aplicativo de negócios
Os dados de IoT podem ser usados para alimentar uma variedade de aplicativos de negócios implantados em um ambiente de varejo. Um gerente ou funcionário do centro de atendimento pode utilizar esses aplicativos para visualizar informações de negócios e tomar ações significativas em tempo real. Para saber como criar um painel de Power BI em tempo real para sua equipe de varejo, siga o [tutorial](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Passos seguintes
* Introdução ao modelo de aplicativo do [centro de microatendimento](https://aka.ms/checkouttemplate) . 
* Dê uma olhada no [tutorial](https://aka.ms/mfc-tutorial) que explica como criar uma solução utilizando o modelo de aplicativo do centro de distribuição.
