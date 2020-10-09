---
title: Descrição geral do acelerador de soluções de Manutenção Preditiva - Azure | Microsoft Docs
description: Uma visão geral do acelerador de solução de manutenção preditiva Azure IoT que prevê o ponto em que é provável que ocorra uma falha para um cenário de negócio.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73827418"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções de Manutenção Preditiva

O acelerador de soluções de Manutenção Preditiva é uma solução ponto a ponto para um cenário de negócio que prevê o momento em que poderá ocorrer uma falha. Pode utilizar, de forma pró-ativa, este acelerador de soluções para atividades como a otimização da manutenção. A solução combina serviços de aceleradores de solução Azure IoT, como o IoT Hub e um espaço de trabalho [de Aprendizagem automática Azure.][lnk-machine-learning] Esta área de trabalho contém um modelo, com base num conjunto de dados de exemplo público, para prever a Vida Útil Remanescente (RUL) de um motor de aeronave. A solução implementa completamente o cenário empresarial IoT como um ponto de partida para que possa planear e implementar uma solução que satisfaça os seus próprios requisitos de negócio.

O código do acelerador de solução de manutenção preditiva [está disponível no GitHub.](https://github.com/Azure/azure-iot-predictive-maintenance)

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos do acelerador de soluções:

![Arquitetura lógica][img-architecture]

Os itens a azul são os serviços do Azure aprovisionados na região em que implementou o acelerador de soluções. Apresenta a lista de regiões onde pode implementar o acelerador de soluções na [página aprovisionamento][lnk-azureiotsolutions].

O item verde é um motor de avião simulado. Pode saber mais sobre estes dispositivos simulados na secção [Dispositivos simulados](#simulated-devices) seguinte.

Os itens cinzentos são componentes que implementam capacidades *de gestão de dispositivos.* A atual versão do acelerador de soluções de Manutenção Preditiva não aprovisiona estes recursos. Para saber mais sobre a gestão do dispositivo, consulte o [acelerador de solução de monitorização remota.][lnk-remote-monitoring]

## <a name="azure-resources"></a>Recursos do Azure

No Portal do Azure, navegue para o grupo de recursos com o nome da solução que escolheu para ver os recursos aprovisionados.

![Recursos aceleradores][img-resource-group]

Quando aprovisiona o acelerador de soluções, recebe um e-mail com uma ligação para a área de trabalho do Machine Learning. Também pode navegar para o espaço de trabalho machine learning a partir da página [microsoft Azure IoT Solution Accelerators.][lnk-azureiotsolutions] Quando a solução estiver no estado **Pronto**, estará disponível um mosaico nesta página.

![Modelo de aprendizagem automática][img-machine-learning]

## <a name="simulated-devices"></a>Dispositivos simulados

No acelerador de solução, um dispositivo simulado é um motor de avião. A solução é aprovisionada com dois motores que mapeiam para uma única aeronave. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e Sensor 15, que fornecem os dados necessários para que o modelo do Machine Learning possa calcular a RUL desse motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao IoT Hub:

*Ciclo de contagem*. Um ciclo é um voo completo com uma duração entre duas e dez horas. Durante o voo, os dados telemétricos são capturados a cada meia hora.

*Telemetria.* Há quatro sensores que registam atributos do motor. Os sensores são geralmente denominados Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Estes quatro sensores enviam telemetria suficiente para obter resultados úteis do modelo RUL. O modelo utilizado no acelerador de soluções é criado a partir de um conjunto de dados públicos, que inclui dados do sensor do motor. Para obter mais informações sobre a criação do modelo a partir do conjunto de dados original, consulte o artigo [Cortana Intelligence Gallery Predictive Maintenance Template (Modelo de Manutenção Preditiva do Cortana Intelligence Gallery)][lnk-cortana-analytics].

Os dispositivos simulados podem processar os seguintes comandos enviados a partir do hub IoT na solução:

| Comando | Descrição |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O IoT Hub reconhece o comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Tarefa do Azure Stream Analytics

**Trabalho: A telemetria** opera no fluxo de telemetria do dispositivo de entrada utilizando duas declarações:

* A primeira seleciona toda a telemetria dos dispositivos e envia esses dados ao armazenamento de blobs. A partir daqui, é visualizado na aplicação web.
* A segunda calcula os valores médios do sensor numa janela deslizante e envia esses dados através do Hub de Eventos a um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **anfitrião do processador de eventos** executa uma Tarefa Web do Azure. O **processador de eventos** obtém os valores médios do sensor para um ciclo concluído. Em seguida, passa esses valores para um modelo treinado que calcula o RUL para um motor. Uma API fornece acesso ao modelo num espaço de trabalho machine learning que faz parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning utiliza um modelo derivado dos dados recolhidos de motores de aeronave reais. Pode navegar para o espaço de trabalho machine learning a partir do azulejo da sua solução na página [azureiotsolutions.com.][lnk-azureiotsolutions] O mosaico está disponível quando a solução está no estado **Pronto**.

O modelo machine learning está disponível como um modelo que mostra como trabalhar com telemetria recolhida através de serviços de acelerador de solução IoT. A Microsoft construiu um modelo de [regressão][lnk_regression_model] de um motor de avião baseado em dados<sup> \[ 1 \] </sup>publicamente disponíveis , e orientação passo a passo sobre como usar o modelo.

O acelerador de soluções de Manutenção Preditiva do Azure IoT utiliza o modelo de regressão criado a partir deste modelo. O modelo é implantado na sua subscrição Azure e disponibilizado através de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste para 4 (de 100 no total) motores e os fluxos de dados de sensores 4 (de 21 no total). Estes dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1 \] A. Saxena e K. Goebel (2008). "Turbofan Engine Degradation Simulation Data set", Repositório de Dados da NASA Ames Prognostics https://c3.nasa.gov/dashlink/resources/139/) (, Centro de Investigação da NASA Ames, Campo Moffett, CA*

## <a name="next-steps"></a>Passos seguintes
Agora que viu os principais componentes do acelerador de soluções de Manutenção Preditiva, deverá personalizá-los.

Também pode explorar algumas das outras características dos aceleradores de solução IoT:

* [Perguntas mais frequentes sobre aceleradores de soluções do IoT][lnk-faq]
* [Segurança de IoT desde o início][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsolutions]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: https://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
