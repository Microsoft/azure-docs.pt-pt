---
title: Tecnologias e soluções de Internet das Coisas (IoT) do Azure
description: Descreve a coleção de tecnologias e serviços que você pode usar para criar uma solução de IoT do Azure.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046065"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Tecnologias e serviços do Azure para a criação de soluções de IoT

As tecnologias e os serviços do Azure IoT fornecem opções para criar uma ampla variedade de soluções de IoT que habilitam a transformação digital para sua organização. Pode, por exemplo:

- Utilize a [Azure IoT Central,](https://apps.azureiotcentral.com)uma plataforma de aplicação IoT gerida, para construir e implementar uma solução IoT segura e de nível empresarial. IoT Central apresenta uma coleção de modelos de aplicativos específicos do setor, como varejo e saúde, para acelerar o processo de desenvolvimento da solução.
- Alargar a base de código de código de código de código de código de código de código de código para um acelerador de [solução](https://www.azureiotsolutions.com) Azure IoT para implementar um cenário ioT comum, como monitorização remota ou manutenção preditiva.
- Utilize serviços de plataforma Azure IoT, como [o Azure IoT Hub](../iot-hub/about-iot-hub.md) e os [SDKs do dispositivo Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) para construir uma solução IoT personalizada do zero.

![Tecnologias, serviços e soluções do Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

A plataforma de [aplicações IoT Central](https://apps.azureiotcentral.com) reduz os encargos e os custos de desenvolvimento, gestão e manutenção de soluções IoT de nível empresarial. A interface do usuário da Web personalizável do IoT Central no permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e seus dados durante todo o ciclo de vida. A superfície de API dentro de IoT Central fornece acesso programático para configurar e interagir com sua solução de IoT.

O Azure IoT Central é uma plataforma de aplicativo totalmente gerenciada que você pode usar para criar soluções personalizadas de IoT. IoT Central usa modelos de aplicativo para criar soluções. Há modelos para soluções genéricas e para setores específicos, como energia, saúde, governo e varejo. IoT Central modelos de aplicativo permitem que você implante um aplicativo IoT Central em minutos que você pode personalizar com temas, painéis e exibições.

Escolha dispositivos do catálogo de [dispositivos Azure Certified para IoT](https://catalog.azureiotsolutions.com) para se ligar rapidamente à sua solução. Use a interface do usuário da Web do IoT Central para monitorar e gerenciar seus dispositivos para mantê-los íntegros e conectados. Use conectores e APIs para integrar seu aplicativo IoT Central com outros aplicativos de negócios.

Como uma plataforma de aplicativo totalmente gerenciada, IoT Central tem um modelo de preços simples e previsível.

## <a name="azure-iot-solution-accelerators"></a>Aceleradores de soluções do Azure IoT

Os aceleradores de [soluções Azure IoT](https://www.azureiotsolutions.com) são uma coleção de soluções personalizáveis de nível empresarial. Você pode implantar essas soluções como elas estão ou desenvolver uma solução de IoT personalizada usando o código-fonte Java ou .NET de código aberto.

Os aceleradores de solução do Azure IoT fornecem um alto nível de controle sobre sua solução de IoT. Os aceleradores de solução incluem soluções predefinidas para cenários comuns de IoT que você pode implantar em sua assinatura do Azure em minutos. Os cenários incluem:

  - Monitorização remota
  - Fábrica ligada
  - Manutenção preditiva
  - Simulação de dispositivo

A base de código-fonte aberto para todos os aceleradores de solução está disponível no GitHub. Baixe o código para personalizar um Solution Accelerator para atender aos seus requisitos específicos de IoT.

Os aceleradores de solução usam os serviços do Azure, como o Hub IoT do Azure e o armazenamento do Azure que você deve gerenciar em sua assinatura do Azure.

## <a name="custom-solutions"></a>Soluções personalizadas

Para criar uma solução de IoT do zero ou estender uma solução criada usando IoT Central ou um Solution Accelerator, use um ou mais dos seguintes serviços e tecnologias de IoT do Azure:

### <a name="devices"></a>Dispositivos

Desenvolva os seus dispositivos IoT utilizando um dos Kits de [Arranque Azure IoT](https://catalog.azureiotsolutions.com/kits) ou escolha um dispositivo para utilizar no catálogo de [dispositivos Azure Certified para ioT](https://catalog.azureiotsolutions.com). Implemente o seu código incorporado utilizando os [SDKs](../iot-hub/iot-hub-devguide-sdks.md)do dispositivo de código aberto . Os SDKs de dispositivo dão suporte a vários sistemas operacionais, como Linux, Windows e sistemas operacionais em tempo real. Existem SDKs para várias linguagens de programação, tais como [C,](https://github.com/Azure/azure-iot-sdk-c) [Node.js,](https://github.com/Azure/azure-iot-sdk-node) [Java,](https://github.com/Azure/azure-iot-sdk-java) [.NET](https://github.com/Azure/azure-iot-sdk-csharp)e [Python.](https://github.com/Azure/azure-iot-sdk-python)

Pode simplificar ainda mais a forma como cria o código incorporado para os seus dispositivos utilizando o serviço [IoT Plug e Play Preview.](../iot-pnp/overview-iot-plug-and-play.md) O IoT Plug and Play permite que os desenvolvedores de soluções integrem dispositivos a suas soluções sem escrever nenhum código embutido. No centro do IoT Plug and Play, está um esquema de modelo de capacidade de _dispositivo_ que descreve as capacidades do dispositivo. Use o modelo de funcionalidade do dispositivo para gerar o código de dispositivo incorporado e configurar uma solução baseada em nuvem, como um aplicativo IoT Central.

[O Azure IoT Edge](../iot-edge/about-iot-edge.md) permite-lhe descarregar partes da sua carga de trabalho IoT dos seus serviços de nuvem Azure para os seus dispositivos. IoT Edge pode reduzir a latência em sua solução, reduzir a quantidade de dados que seus dispositivos trocam com a nuvem e habilitar cenários offline. Você pode gerenciar dispositivos IoT Edge de IoT Central e alguns aceleradores de solução.

[A Esfera Azure](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) é uma plataforma de aplicação segura e de alto nível com funcionalidades de comunicação e segurança incorporadas para dispositivos ligados à Internet. Ele inclui uma unidade protegida do Microcontroller, um sistema operacional personalizado baseado em Linux e um serviço de segurança baseado em nuvem que fornece segurança contínua e renovável.

### <a name="cloud-connectivity"></a>Conectividade de nuvem

O serviço [Azure IoT Hub](../iot-hub/about-iot-hub.md) permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução baseada na nuvem. O Serviço de Provisionamento de [Dispositivos Azure IoT Hub](../iot-dps/about-iot-dps.md) é um serviço de ajuda para o IoT Hub. O serviço fornece provisionamento de dispositivos sem toque e Just-in-time para o Hub IoT correto sem a necessidade de intervenção humana. Esses recursos permitem que os clientes provisionem milhões de dispositivos de maneira segura e escalonável.

O Hub IoT é um componente fundamental dos aceleradores de solução e você pode usá-lo para atender aos desafios de implementação de IoT, como:

* Conectividade e gestão de dispositivos de volume elevado.
* Ingestão de telemetria de volume elevado.
* Comando e controlo de dispositivos.
* Imposição de segurança do dispositivo.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>A transição da lacuna entre os mundos físicos e digitais

[Azure Digital Twins](../digital-twins/about-digital-twins.md) é um serviço IoT que lhe permite modelar um ambiente físico. Ele usa um grafo de inteligência espacial para modelar as relações entre pessoas, espaços e dispositivos. Ao corelacionar dados em mundos digitais e físicos, você pode criar soluções com reconhecimento contextual.

A IOT central usa o gêmeos digital para sincronizar dispositivos e dados no mundo real com os modelos digitais que permitem aos usuários monitorar e gerenciar esses dispositivos conectados.

### <a name="data-and-analytics"></a>Dados e análises

Os dispositivos IoT normalmente geram grandes quantidades de dados de série temporal, como leituras de temperatura de sensores. A [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) pode ligar-se a um hub IoT, ler o fluxo de telemetria dos seus dispositivos, armazenar esses dados e permitir-lhe consultar e visualizar os mesmos.

[O Azure Maps](/azure/azure-maps) é uma coleção de serviços geoespaciais que utilizam dados de mapeamento frescos para fornecer um contexto geográfico preciso às aplicações web e móveis. Pode utilizar um Rest API, um controlo JavaScript baseado na Web ou um SDK Android para construir as suas aplicações.

## <a name="next-steps"></a>Passos seguintes

Para uma experiência prática, experimente um dos guias de início rápido:

- [Criar uma aplicação Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- [Envie telemetria de um dispositivo para um hub IoT](../iot-hub/quickstart-send-telemetry-cli.md)
- [Tentar uma solução de monitorização remota com base na cloud](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
