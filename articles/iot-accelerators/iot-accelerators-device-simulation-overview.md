---
title: Visão geral da simulação do dispositivo - Azure / Microsoft Docs
description: Uma descrição do acelerador de solução de simulação de dispositivo e as suas capacidades.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65464879"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções da Simulação de Dispositivos

Numa solução IoT baseada em nuvem, os seus dispositivos ligam-se a um ponto final de nuvem para enviar telemetria, como temperatura, localização e estado. A sua solução consome esta telemetria, permitindo-lhe tomar ações ou obter insights dela.

Quando se desenvolve uma Solução IoT, a experimentação e os testes são partes essenciais desse processo. A simulação é uma ferramenta importante durante todo este processo. Com simulação de dispositivo pode:

* Rapidamente coloque um protótipo em funcionamento e, em seguida, iterado ajustando o comportamento simulado do dispositivo em movimento. Este processo permite-lhe provar a ideia antes de investir em hardware dispendioso. Pode criar dispositivos personalizados através da UI web para gerar um dispositivo protótipo em segundos.
* Validar a solução funciona como esperado de dispositivo em solução, simulando comportamentos de dispositivos do mundo real. Pode scripts comportamentos complexos de dispositivos usando javaScript para gerar telemetria simulada realista.
* A escala testa a sua solução simulando condições normais, máximas e para além das condições de carga máxima. Os testes de escala também ajudam a corrigir o tamanho certo dos recursos Azure necessários para executar a sua solução.

![Simulação de drone de amostra](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Com a Simulação do Dispositivo, pode definir modelos de dispositivos para simular os seus dispositivos reais. Este modelo inclui formatos de mensagens, propriedades gémeas e métodos. Também pode simular comportamentos complexos do dispositivo com o JavaScript.

Pode executar simulações para um a milhares de dispositivos que se ligam a qualquer hub IoT. Para ajudar nos testes, pode implementar opcionalmente um hub IoT juntamente com a Simulação do Dispositivo para um ambiente autónomo.

A simulação do dispositivo é gratuita. No entanto, a Simulação de Dispositivos implementa-se na sua subscrição Azure na nuvem e consome recursos Azure. Se a Simulação do Dispositivo não cumprir os seus requisitos, o [código fonte também está disponível no GitHub](https://github.com/Azure/device-simulation-dotnet) para que copie e modifique.

## <a name="sample-simulations"></a>Simulações de amostras

Quando implementa a Simulação do Dispositivo, obtém-se algumas simulações de amostra e dispositivos de amostra. Pode utilizar estas amostras para aprender a utilizar a Simulação do Dispositivo. Para começar, faça uma [simulação de amostra que simula 10 camiões.](quickstart-device-simulation-deploy.md) Também pode [criar a sua própria simulação utilizando um dos muitos dispositivos de amostra fornecidos.](iot-accelerators-device-simulation-create-simulation.md)

![Configuração de simulação](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Dispositivos simulados personalizados

Pode utilizar a Simulação do Dispositivo para [criar modelos de dispositivos personalizados](iot-accelerators-device-simulation-create-custom-device.md) para utilizar nas suas simulações. Por exemplo, pode definir um novo modelo de dispositivo frigorífico que envia telemetria de temperatura e humidade. Os dispositivos simulados personalizados são ideais para comportamentos simples do dispositivo com valores aleatórios, incrementados ou descedição de telemetria.

![Criar o modelo de dispositivo](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Dispositivos simulados avançados

Quando precisa de mais controlo sobre os valores de telemetria que um dispositivo envia, pode utilizar um modelo avançado de dispositivo. Os modelos avançados do dispositivo permitem que o suporte javaScript manipule os valores de telemetria enviados. Por exemplo, pode simular a temperatura interior de um carro estacionado num dia quente e ensolarado - à medida que a temperatura exterior sobe, a temperatura interior aumenta exponencialmente.

Os modelos avançados do dispositivo [permitem-lhe criar e carregar os seus próprios modelos](iot-accelerators-device-simulation-advanced-device.md) de dispositivos que consistem num ficheiro de definição de dispositivo JSON e ficheiros JavaScript correspondentes.

Modelos avançados de dispositivos permitem::

* Especifique o formato de mensagem enviado do dispositivo juntamente com os tipos de telemetria.
* Utilize scripts personalizados para gerar valores de telemetria que mantenham o estado do dispositivo ao longo do tempo.
* Utilize scripts personalizados para especificar como o dispositivo simulado responde aos métodos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o acelerador de solução de simulação de dispositivo e as suas capacidades. Para começar a usar o acelerador de solução, continue a acelerar:

> [!div class="nextstepaction"]
> [Implementar e executar uma simulação de dispositivo IoT em Azure](quickstart-device-simulation-deploy.md)
