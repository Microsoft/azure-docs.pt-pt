---
title: Descrição geral do acelerador de soluções de Manutenção Preditiva - Azure | Microsoft Docs
description: Uma visão geral do acelerador de solução de manutenção preditiva do Azure IoT que prevê o ponto em que uma falha provavelmente ocorrerá para um cenário de negócios.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 0661503dce7ac2707065f60c3952da866ce9dcf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827418"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções de Manutenção Preditiva

O acelerador de soluções de Manutenção Preditiva é uma solução ponto a ponto para um cenário de negócio que prevê o momento em que poderá ocorrer uma falha. Pode utilizar, de forma pró-ativa, este acelerador de soluções para atividades como a otimização da manutenção. A solução combina os principais serviços do Azure IoT Solution Accelerators, como o Hub IoT e um espaço de trabalho [Azure Machine Learning][lnk-machine-learning] . Esta área de trabalho contém um modelo, com base num conjunto de dados de exemplo público, para prever a Vida Útil Remanescente (RUL) de um motor de aeronave. A solução implementa completamente o cenário empresarial IoT como um ponto de partida para que possa planear e implementar uma solução que satisfaça os seus próprios requisitos de negócio.

O código do acelerador de solução de manutenção preditiva [está disponível no GitHub](https://github.com/Azure/azure-iot-predictive-maintenance).

## <a name="logical-architecture"></a>Arquitetura lógica

O diagrama que se segue descreve os componentes lógicos do acelerador de soluções:

![Arquitetura lógica][img-architecture]

Os itens a azul são os serviços do Azure aprovisionados na região em que implementou o acelerador de soluções. A lista de regiões em que você pode implantar o Solution Accelerator é exibida na [página de provisionamento][lnk-azureiotsolutions].

O item verde é um mecanismo de aeronave simulado. Pode saber mais sobre estes dispositivos simulados na secção [Dispositivos simulados](#simulated-devices) seguinte.

Os itens em cinza são componentes que implementam os recursos de *Gerenciamento de dispositivos* . A atual versão do acelerador de soluções de Manutenção Preditiva não aprovisiona estes recursos. Para saber mais sobre o gerenciamento de dispositivos, consulte o [acelerador de solução de monitoramento remoto][lnk-remote-monitoring].

## <a name="azure-resources"></a>Recursos do Azure

No Portal do Azure, navegue para o grupo de recursos com o nome da solução que escolheu para ver os recursos aprovisionados.

![Recursos de acelerador][img-resource-group]

Quando aprovisiona o acelerador de soluções, recebe um e-mail com uma ligação para a área de trabalho do Machine Learning. Você também pode navegar até o espaço de trabalho do Machine Learning na página de [aceleradores de solução Microsoft Azure IOT][lnk-azureiotsolutions] . Quando a solução estiver no estado **Pronto**, estará disponível um mosaico nesta página.

![Modelo de aprendizado de máquina][img-machine-learning]

## <a name="simulated-devices"></a>Dispositivos simulados

No Solution Accelerator, um dispositivo simulado é um mecanismo de aeronave. A solução é aprovisionada com dois motores que mapeiam para uma única aeronave. Cada motor emite quatro tipos de telemetria: Sensor 9, Sensor 11, Sensor 14 e Sensor 15, que fornecem os dados necessários para que o modelo do Machine Learning possa calcular a RUL desse motor. Cada dispositivo simulado envia as seguintes mensagens de telemetria ao IoT Hub:

*Ciclo de contagem*. Um ciclo é um vôo concluído com uma duração entre duas e dez horas. Durante o voo, os dados telemétricos são capturados a cada meia hora.

*Telemetria*. Há quatro sensores que registram os atributos do mecanismo. Os sensores são geralmente denominados Sensor 9, Sensor 11, Sensor 14 e Sensor 15. Esses quatro sensores enviam a telemetria suficiente para obter resultados úteis do modelo RUL. O modelo utilizado no acelerador de soluções é criado a partir de um conjunto de dados públicos, que inclui dados do sensor do motor. Para obter mais informações sobre como o modelo foi criado a partir do conjunto de dados original, consulte o [modelo de manutenção preditiva Cortana Intelligence Gallery][lnk-cortana-analytics].

Os dispositivos simulados podem processar os seguintes comandos enviados a partir do hub IoT na solução:

| Comando | Descrição |
| --- | --- |
| StartTelemetry |Controla o estado da simulação.<br/>Inicia o dispositivo que envia a telemetria |
| StopTelemetry |Controla o estado da simulação.<br/>Para o dispositivo que envia a telemetria |

O IoT Hub reconhece o comando do dispositivo.

## <a name="azure-stream-analytics-job"></a>Tarefa do Azure Stream Analytics

**Tarefa: Telemetria** intervém no fluxo de telemetria de entrada do dispositivo através de duas instruções:

* A primeira seleciona toda a telemetria dos dispositivos e envia esses dados ao armazenamento de blobs. A partir daqui, ele é visualizado no aplicativo Web.
* A segunda calcula os valores médios do sensor numa janela deslizante e envia esses dados através do Hub de Eventos a um **processador de eventos**.

## <a name="event-processor"></a>Processador de eventos
O **anfitrião do processador de eventos** executa uma Tarefa Web do Azure. O **processador de eventos** obtém os valores médios do sensor para um ciclo concluído. Em seguida, ele passa esses valores para um modelo treinado que calcula o RUL para um mecanismo. Uma API fornece acesso ao modelo em um espaço de trabalho Machine Learning que faz parte da solução.

## <a name="machine-learning"></a>Machine Learning
O componente de Machine Learning utiliza um modelo derivado dos dados recolhidos de motores de aeronave reais. Você pode navegar até o espaço de trabalho Machine Learning no bloco da sua solução na página [azureiotsolutions.com][lnk-azureiotsolutions] . O mosaico está disponível quando a solução está no estado **Pronto**.

O modelo de Machine Learning está disponível como um modelo que mostra como trabalhar com a telemetria coletada por meio dos serviços do acelerador de soluções do IoT. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um mecanismo de aeronave baseado em dados publicamente disponíveis<sup>\[1\]</sup>e orientações passo a passo sobre como usar o modelo.

O acelerador de soluções de Manutenção Preditiva do Azure IoT utiliza o modelo de regressão criado a partir deste modelo. O modelo é implantado em sua assinatura do Azure e disponibilizado por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste para 4 (de 100 total) mecanismos e os fluxos de dados de sensor de 4 (de 21 totais). Estes dados são suficientes para fornecer um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). "Conjunto de dados de simulação de degradação do mecanismo turbofan", repositório de dados de NASA Ames Prognostics (https://c3.nasa.gov/dashlink/resources/139/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Passos seguintes
Agora que viu os principais componentes do acelerador de soluções de Manutenção Preditiva, deverá personalizá-los.

Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Perguntas frequentes sobre os aceleradores de solução de IoT][lnk-faq]
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
