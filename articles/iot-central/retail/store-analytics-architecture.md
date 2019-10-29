---
title: Arquitetura da análise da loja
description: Aprenda a criar um aplicativo de análise no repositório usando o modelo de aplicativo de check-out no IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 1cf4b29274bbd62aa8c78fc2907a20165280ce08
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026332"
---
# <a name="in-store-analytics-architecture"></a>Arquitetura da análise na loja

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

As soluções de análise no repositório permitem que você monitore várias condições no ambiente de loja de varejo. Essas soluções podem ser criadas aproveitando um dos modelos de aplicativo no IoT Central e a arquitetura abaixo como orientação.


![Análise do Azure IoT Central Store](./media/architecture/store-analytics-architecture-frame.png)

- Conjunto de sensores IoT enviando dados de telemetria para um dispositivo de gateway
- Dispositivos de gateway que enviam telemetria e informações agregadas para IoT Central
- Exportação de dados contínua para o serviço do Azure desejado para manipulação
- Os dados podem ser estruturados no formato desejado e enviados a um serviço de armazenamento
- Os aplicativos de negócios podem consultar dados e gerar informações que reforçam as operações de varejo
 
Vamos dar uma olhada nos principais componentes que geralmente desempenham uma parte em uma solução de análise na loja.

## <a name="condition-monitoring-sensors"></a>Sensores de monitoramento de condição

Uma solução de IoT começa com um conjunto de sensores capturando sinais significativos de dentro de um ambiente de loja de varejo. Ele é refletido por uma variedade de sensores na extrema esquerda do diagrama de arquitetura acima.

## <a name="gateway-devices"></a>Dispositivos do gateway

Muitos sensores IoT podem alimentar sinais brutos diretamente para a nuvem ou para um dispositivo de gateway localizado perto deles. O dispositivo de gateway executa a agregação de dados na borda antes de enviar informações de resumo para um aplicativo IoT Central. Os dispositivos de gateway também são responsáveis pela retransmissão de operações de comando e controle para os dispositivos do sensor quando aplicável. 

## <a name="iot-central-application"></a>IoT Central aplicativo

O aplicativo de IoT Central do Azure ingere dados de uma variedade de sensores IoT, bem como de dispositivos de gateway no ambiente de loja de varejo, e gera um conjunto de ideias significativas.

O Azure IoT Central também fornece uma experiência personalizada para o operador Store, permitindo que eles monitorem e gerenciem remotamente os dispositivos de infraestrutura.

## <a name="data-transform"></a>Transformação de dados
O aplicativo de IoT Central do Azure em uma solução pode ser configurado para exportar insights brutos ou agregados para um conjunto de serviços de PaaS (plataforma como serviço) do Azure que podem executar a manipulação de dados e enriquecer essas informações antes de confazê-los em uma empresa aplicativo. 

## <a name="business-application"></a>Aplicativo de negócios
Os dados de IoT podem ser usados para alimentar uma variedade de aplicativos de negócios implantados em um ambiente de varejo. Um gerente de loja de varejo ou membro da equipe pode utilizar esses aplicativos para visualizar informações de negócios e tomar ações significativas em tempo real. Para saber como criar um painel de Power BI em tempo real para sua equipe de varejo, siga o [tutorial](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Passos seguintes
* Introdução aos modelos de aplicativo de [check-out da análise na loja](https://aka.ms/checkouttemplate) e [análise no repositório](https://aka.ms/conditiontemplate) . 
* Veja o [tutorial de ponta a ponta](https://aka.ms/storeanalytics-tutorial) que explica como criar uma solução usando um dos modelos de aplicativo de análise no repositório.
