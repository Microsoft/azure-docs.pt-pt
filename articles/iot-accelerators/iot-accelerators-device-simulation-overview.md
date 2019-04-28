---
title: Descrição geral simulação do dispositivo - Azure | Documentos da Microsoft
description: Uma descrição do solution accelerator simulação do dispositivo e as respetivas capacidades.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 173963470a77932186b3c51f17ccb406b32a13f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448491"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Descrição geral do acelerador de soluções da Simulação de Dispositivos

Numa solução de IoT com base na cloud, os seus dispositivos ligam a um ponto final da cloud para enviar telemetria, tais como temperatura, localização e o estado. Sua solução consome esta telemetria, permitindo-lhe executar ações ou derivar as informações dos mesmos.

Ao desenvolver uma solução de IoT, experimentação e de teste são partes essenciais do processo. Simulação é uma ferramenta importante ao longo deste processo. Com a simulação do dispositivo, pode:

* Obtenha rapidamente um protótipo em funcionamento e, em seguida, iterar ao ajustar simulated o comportamento do dispositivo em tempo real. Este processo permite-lhe provar que a idéia antes de investir em hardware dispendiosa. Pode criar dispositivos personalizados através da web da interface do Usuário para gerar um dispositivo de protótipo em segundos.
* Simule comportamentos de dispositivos do mundo real para confirmar se a solução funciona conforme o esperado do dispositivo até à solução. Pode criar um script comportamentos de dispositivo complexos usando o JavaScript para gerar a telemetria simulada realista.
* Dimensionamento testar sua solução por simulação normal, pico e muito mais condições de carga de pico. Testes de dimensionamento também ajudá-lo a dimensão certa de recursos do Azure necessários para executar a sua solução.

![Simulação de drones de exemplo](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Com a simulação do dispositivo, pode definir modelos de dispositivos para simular os seus dispositivos reais. Esse modelo inclui métodos, propriedades de twin e formatos de mensagem. Também pode simular a comportamentos de dispositivo complexos com JavaScript.

Pode executar simulações para uma a milhares de dispositivos que ligam ao qualquer hub de IoT. Para ajudar com o teste, opcionalmente, pode implementar um hub IoT, juntamente com a simulação do dispositivo para um ambiente autônomo.

Simulação do dispositivo é gratuita. No entanto, a simulação do dispositivo implementa a sua subscrição do Azure na cloud e a consumir recursos do Azure. Se a simulação do dispositivo não cumpre os requisitos, o [código-fonte também está disponível no GitHub](https://github.com/Azure/device-simulation-dotnet) para que possa copiar e modificar.

## <a name="sample-simulations"></a>Simulações de exemplo

Quando implementar simulação do dispositivo, obtém algumas simulações de exemplo e dispositivos de exemplo. Pode utilizar estes exemplos para saber como utilizar a simulação do dispositivo. Para começar a utilizar, execute uma [exemplo de simulação, que simula 10 trucks](quickstart-device-simulation-deploy.md). Também pode [criar sua própria simulação usando um dos muitos dispositivos de exemplo fornecidos](iot-accelerators-device-simulation-create-simulation.md).

![Configuração de simulação](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>Dispositivos simulados personalizados

Pode usar a simulação de dispositivo para [criar modelos de dispositivos personalizados](iot-accelerators-device-simulation-create-custom-device.md) usar em suas simulações. Por exemplo, pode definir um novo modelo de dispositivo de refrigerator (refrigerador) que envia telemetria de temperatura e humidade. Dispositivos simulados personalizados são ideais para comportamentos de dispositivo de simples com aleatório, incrementando ou valores de telemetria de diminuição.

![Criar o modelo de dispositivo](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Dispositivos simulados avançados

Quando precisar de mais controlo sobre os valores de telemetria que envia um dispositivo, pode utilizar um modelo de dispositivo avançado. Modelos de dispositivos avançado ativar suporte de JavaScript para manipular os valores de telemetria enviada. Por exemplo, poderia simular a temperatura interior de um carro parqueado num dia ensolarado frequente – como o aumento de temperatura exterior, a temperatura interior aumenta exponencialmente.

Modelos de dispositivos avançadas permitem-lhe [criar e carregar seus próprios modelos de dispositivo](iot-accelerators-device-simulation-advanced-device.md) que consistem numa definição de dispositivo JSON de ficheiros e ficheiros de JavaScript correspondentes.

Modelos de dispositivos avançadas permitem-lhe:

* Especifique o formato de mensagem enviada do dispositivo, juntamente com os tipos de telemetria.
* Utilize a criação de scripts personalizados para gerar valores de telemetria que mantêm o estado do dispositivo ao longo do tempo.
* Utilize a criação de scripts personalizados para especificar como o dispositivo simulado responde aos métodos.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre o solution accelerator de simulação do dispositivo e as respetivas capacidades. Para começar a utilizar o solution accelerator, avance para o início rápido:

> [!div class="nextstepaction"]
> [Implementar e executar uma simulação de dispositivo do IoT no Azure](quickstart-device-simulation-deploy.md)
