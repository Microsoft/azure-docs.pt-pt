---
title: Visão geral da simulação do dispositivo - Azure | Microsoft Docs
description: Uma descrição do acelerador de solução de simulação do dispositivo e das suas capacidades.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 979643c76b8bded51e30232228b7fe7485a1f53b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057682"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções da Simulação de Dispositivos

Numa solução IoT baseada na nuvem, os seus dispositivos ligam-se a um ponto final de nuvem para enviar telemetria, como temperatura, localização e estado. A sua solução consome esta telemetria, permitindo-lhe tomar ações ou obter insights a partir dela.

Quando desenvolves uma Solução IoT, a experimentação e os testes são partes essenciais desse processo. A simulação é uma ferramenta importante ao longo deste processo. Com a simulação do dispositivo pode:

* Rapidamente obtenha um protótipo em funcionamento e, em seguida, itere ajustando o comportamento simulado do dispositivo em movimento. Este processo permite-lhe provar a ideia antes de investir em hardware dispendioso. Pode criar dispositivos personalizados através da UI web para gerar um dispositivo protótipo em segundos.
* Validar o funcionamento da solução como esperado do dispositivo para a solução simulando comportamentos de dispositivos no mundo real. Pode script comportamentos complexos do dispositivo usando JavaScript para gerar telemetria simulada realista.
* Teste a sua solução simulando condições normais, de pico e além das condições de carga máxima. Os testes de escala também o ajudam a corrigir os recursos Azure necessários para executar a sua solução.

![Simulação de drone de amostra](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Com a Simulação do Dispositivo, pode definir modelos de dispositivos para simular os seus verdadeiros dispositivos. Este modelo inclui formatos de mensagens, propriedades gémeas e métodos. Também pode simular comportamentos complexos do dispositivo com o JavaScript.

Pode executar simulações para um a milhares de dispositivos que se ligam a qualquer hub IoT. Para ajudar no teste, pode opcionalmente implantar um hub IoT juntamente com a Simulação do Dispositivo para um ambiente autónomo.

A simulação do dispositivo é gratuita. No entanto, a Simulação de Dispositivo implementa-se na subscrição do Azure na nuvem e consome recursos Azure. Se a Simulação do Dispositivo não cumprir os seus requisitos, o [código fonte também está disponível no GitHub](https://github.com/Azure/device-simulation-dotnet) para que possa copiar e modificar.

## <a name="sample-simulations"></a>Simulações de amostras

Quando implementa a Simulação do Dispositivo, obtém-se algumas simulações de amostra e dispositivos de amostra. Pode utilizar estas amostras para aprender a utilizar a Simulação do Dispositivo. Para começar, faça uma [simulação de amostra.](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) Também pode [criar a sua própria simulação utilizando um dos muitos dispositivos de amostra fornecidos.](iot-accelerators-device-simulation-create-simulation.md)

![Configuração de simulação](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Dispositivos simulados personalizados

Pode utilizar a Simulação do Dispositivo para [criar modelos de dispositivos personalizados](iot-accelerators-device-simulation-create-custom-device.md) para utilizar nas suas simulações. Por exemplo, pode definir um novo modelo de dispositivo de frigorífico que envia telemetria de temperatura e humidade. Dispositivos simulados personalizados são ideais para comportamentos simples do dispositivo com valores aleatórios, incrementantes ou depreciativos da telemetria.

![Criar o modelo de dispositivo](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Dispositivos simulados avançados

Quando necessitar de mais controlo sobre os valores de telemetria que um dispositivo envia, pode utilizar um modelo avançado do dispositivo. Os modelos avançados do dispositivo permitem o suporte JavaScript manipular os valores de telemetria enviados. Por exemplo, pode simular a temperatura interior de um carro estacionado num dia quente e ensolarado - à medida que a temperatura exterior aumenta, a temperatura interior aumenta exponencialmente.

Os modelos avançados de dispositivo permitem [criar e carregar os seus próprios modelos](iot-accelerators-device-simulation-advanced-device.md) de dispositivos que consistem num ficheiro de definição de dispositivo JSON e nos ficheiros JavaScript correspondentes.

Modelos avançados de dispositivos permitem:00

* Especifique o formato de mensagem enviado do dispositivo juntamente com os tipos de telemetria.
* Utilize scripts personalizados para gerar valores de telemetria que mantenham o estado do dispositivo ao longo do tempo.
* Utilize scripts personalizados para especificar como o dispositivo simulado responde aos métodos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre o acelerador de solução de Simulação de Dispositivos e as suas capacidades. Para implementar o acelerador de solução, visite o repositório GitHub:

> [!div class="nextstepaction"]
> [Implementar e executar uma simulação de dispositivo IoT em Azure](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)
