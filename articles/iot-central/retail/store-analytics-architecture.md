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
ms.openlocfilehash: 6c2514bd078cc3feee4bd2802cf314079b824311
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022125"
---
# <a name="in-store-analytics-architecture"></a>Arquitetura de análise na loja



As soluções de análise na loja permitem monitorizar várias condições no ambiente da loja de retalho. Estas soluções podem ser construídas utilizando um dos modelos de aplicação dentro da IoT Central e a arquitetura abaixo como orientação.


![Análise do Azure IoT Central Store](./media/architecture/store-analytics-architecture-frame.png)

- Conjunto de sensores IoT enviando dados de telemetria para um dispositivo de gateway
- Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
- Exportação de dados contínua para o serviço do Azure desejado para manipulação
- Os dados podem ser estruturados no formato desejado e enviados a um serviço de armazenamento
- Os aplicativos de negócios podem consultar dados e gerar informações que reforçam as operações de varejo
 
Vamos ver os componentes-chave que geralmente desempenham um papel numa solução de análise na loja.

## <a name="condition-monitoring-sensors"></a>Sensores de monitoramento de condição

Uma solução IoT começa com um conjunto de sensores que captam sinais significativos dentro de um ambiente de loja de retalho. Reflete-se por diferentes tipos de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos do gateway

Muitos sensores IoT podem alimentar sinais brutos diretamente para a nuvem ou para um dispositivo de gateway localizado perto deles. O dispositivo de gateway executa a agregação de dados na borda antes de enviar informações de resumo para um aplicativo IoT Central. Os dispositivos de gateway também são responsáveis pela retransmissão de operações de comando e controle para os dispositivos do sensor quando aplicável. 

## <a name="iot-central-application"></a>IoT Central aplicativo

A aplicação Azure IoT Central ingere dados de diferentes tipos de sensores IoT, bem como dispositivos gateway dentro do ambiente de loja de retalho e gera um conjunto de insights significativos.

O Azure IoT Central também fornece uma experiência personalizada para o operador Store, permitindo que eles monitorem e gerenciem remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
A aplicação Azure IoT Central dentro de uma solução pode ser configurada para exportar insights brutos ou agregados para um conjunto de serviços Azure PaaS (Plataforma-as-a-a Service) que podem realizar manipulação de dados e enriquecer esses insights antes de os aterrar numa empresa aplicação. 

## <a name="business-application"></a>Aplicativo de negócios
Os dados do IoT podem ser utilizados para alimentar diferentes tipos de aplicações empresariais implantadas num ambiente de retalho. Um gerente de loja de retalho ou membro do staff pode usar estas aplicações para visualizar insights de negócio e tomar ações significativas em tempo real. Para saber como criar um painel de Power BI em tempo real para sua equipe de varejo, siga o [tutorial](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Passos seguintes
* Inicie-se com os modelos de aplicação de [monitorização](https://aka.ms/checkouttemplate) de condições de análise na loja e na [loja.](https://aka.ms/conditiontemplate) 
* Dê uma olhada no [tutorial de ponta a ponta](https://aka.ms/storeanalytics-tutorial) que o acompanha através de como construir uma solução usando um dos modelos de aplicação In-Store Analytics.
