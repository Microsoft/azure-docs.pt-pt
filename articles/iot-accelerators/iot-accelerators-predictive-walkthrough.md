---
title: Descrição geral do acelerador de soluções de Manutenção Preditiva - Azure | Microsoft Docs
description: Uma visão geral do solution accelerator manutenção preditiva do IoT do Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: dobett
ms.openlocfilehash: ffa85a1a14434e638e76e699650a0be8e31ade00
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010961"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções de Manutenção Preditiva

O acelerador de soluções de Manutenção Preditiva é uma solução ponto a ponto para um cenário de negócio que prevê o momento em que poderá ocorrer uma falha. Pode utilizar, de forma pró-ativa, este acelerador de soluções para atividades como a otimização da manutenção. A solução combina principais serviços de Aceleradores de solução IoT do Azure, como o IoT Hub e um [do Azure Machine Learning] [ lnk-machine-learning] área de trabalho. Esta área de trabalho contém um modelo, com base num conjunto de dados de exemplo público, para prever a Vida Útil Remanescente (RUL) de um motor de aeronave. A solução implementa completamente o cenário empresarial IoT como um ponto de partida para que possa planear e implementar uma solução que satisfaça os seus próprios requisitos de negócio.

O acelerador de solução de manutenção preditiva [código está disponível no GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos do acelerador de soluções:

![Arquitetura lógica][img-architecture]

Os itens a azul são os serviços do Azure aprovisionados na região em que implementou o acelerador de soluções. Apresenta a lista de regiões onde pode implementar o acelerador de soluções na [página aprovisionamento][lnk-azureiotsolutions].

O item a verde é um motor de aeronave simulado. Pode saber mais sobre estes dispositivos simulados na secção [Dispositivos simulados](#simulated-devices) seguinte.

Os itens a cinzento são componentes que implementam *gestão de dispositivos* capacidades. A atual versão do acelerador de soluções de Manutenção Preditiva não aprovisiona estes recursos. Para saber mais sobre a gestão de dispositivos, consulte a [acelerador de solução de monitorização remota][lnk-remote-monitoring].

## <a name="azure-resources"></a>Recursos do Azure

No Portal do Azure, navegue para o grupo de recursos com o nome da solução que escolheu para ver os recursos aprovisionados.

![Recursos de acelerador][img-resource-group]

Quando aprovisiona o acelerador de soluções, recebe um e-mail com uma ligação para a área de trabalho do Machine Learning. Também pode navegar para a área de trabalho do Machine Learning do [Aceleradores de soluções do Microsoft Azure IoT] [ lnk-azureiotsolutions] página. Quando a solução estiver no estado **Pronto**, estará disponível um mosaico nesta página.

![Modelo de aprendizagem automática][img-machine-learning]

## <a name="simulated-devices"></a>Dispositivos simulados

No solution accelerator, um dispositivo simulado é um motor de aeronave. A solução é aprovisionada com dois motores que mapeiam para uma única aeronave. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e Sensor 15 que fornecem os dados necessários para o modelo do Machine Learning calcular a rul desse motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao IoT Hub:

*Ciclo de contagem*. Um ciclo é um voo concluído com uma duração entre duas a dez horas. Durante o voo, os dados telemétricos são capturados a cada meia hora.

*Telemetria*. Existem quatro sensores que registam os atributos do motor. Os sensores são geralmente denominados Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Estes quatro sensores enviar telemetria suficiente para obter resultados úteis a partir do modelo de RUL. O modelo utilizado no acelerador de soluções é criado a partir de um conjunto de dados públicos, que inclui dados do sensor do motor. Para obter mais informações sobre a criação do modelo a partir do conjunto de dados original, consulte o artigo [Cortana Intelligence Gallery Predictive Maintenance Template (Modelo de Manutenção Preditiva do Cortana Intelligence Gallery)][lnk-cortana-analytics].

Os dispositivos simulados podem processar os seguintes comandos enviados a partir do hub IoT na solução:

| Comando | Descrição |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O IoT Hub reconhece o comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Tarefa do Azure Stream Analytics

**Tarefa: Telemetria** intervém no fluxo de telemetria dispositivo entrada utilizando duas instruções:

* A primeira seleciona toda a telemetria dos dispositivos e envia esses dados ao armazenamento de blobs. Aqui, são visualizado na aplicação web.
* A segunda calcula os valores médios do sensor numa janela deslizante e envia esses dados através do Hub de Eventos a um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **anfitrião do processador de eventos** executa uma Tarefa Web do Azure. O **processador de eventos** obtém os valores médios do sensor para um ciclo concluído. Em seguida, transmite esses valores para um modelo preparado, que calcula a RUL de um motor. Uma API fornece acesso ao modelo numa área de trabalho do Machine Learning que faz parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning utiliza um modelo derivado dos dados recolhidos de motores de aeronave reais. Pode navegar para a área de trabalho do Machine Learning do mosaico da sua solução na [azureiotsolutions.com] [ lnk-azureiotsolutions] página. O mosaico está disponível quando a solução está no estado **Pronto**.

O modelo de Machine Learning está disponível como um modelo que mostra como trabalhar com a telemetria recolhida através dos serviços de acelerador de solução de IoT. A Microsoft incorporou um [modelo de regressão] [ lnk_regression_model] de um motor de aeronave com base nos dados publicamente disponíveis<sup>\[1\]</sup>e orientações passo a passo como utilizar o modelo.

O acelerador de soluções de Manutenção Preditiva do Azure IoT utiliza o modelo de regressão criado a partir deste modelo. O modelo é implementado na sua subscrição do Azure e disponibilizado por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste para 4 (em 100, no total) motores e os 4 (21, no total) fluxos de dados de sensor. Estes dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Passos Seguintes
Agora que viu os principais componentes do acelerador de soluções de Manutenção Preditiva, deverá personalizá-los.

Também pode explorar alguns dos outros recursos de Aceleradores de solução IoT:

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
