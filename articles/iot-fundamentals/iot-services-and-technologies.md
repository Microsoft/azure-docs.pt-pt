---
title: Tecnologias e soluções de Internet das Coisas (IoT) do Azure
description: Descreve a coleção de tecnologias e serviços que você pode usar para criar uma solução de IoT do Azure.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/09/2018
ms.author: robinsh
ms.openlocfilehash: cf707a0ddc3d5b46c7be7944a911ee03f00fc064
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457194"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Tecnologias e serviços do Azure para a criação de soluções de IoT

As tecnologias e os serviços do Azure IoT fornecem opções para criar uma ampla variedade de soluções de IoT que habilitam a transformação digital para sua organização. Por exemplo, pode:

- Use o [Azure IOT central](https://apps.azureiotcentral.com), uma plataforma de aplicativo IOT gerenciada, para criar e implantar uma solução de IOT de nível empresarial segura. IoT Central apresenta uma coleção de modelos de aplicativos específicos do setor, como varejo e saúde, para acelerar o processo de desenvolvimento da solução.
- Estenda a base de código de software livre para um [acelerador de solução](https://www.azureiotsolutions.com) do Azure IOT para implementar um cenário de IOT comum, como monitoramento remoto ou manutenção preditiva.
- Use os serviços da plataforma IoT do Azure, como o [Hub IOT do](../iot-hub/about-iot-hub.md) Azure e os [SDKs do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para criar uma solução de IOT personalizada do zero.

![Tecnologias, serviços e soluções do Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

O [IOT central plataforma de aplicativos](https://apps.azureiotcentral.com) reduz a carga e o custo de desenvolvimento, gerenciamento e manutenção de soluções de IOT de nível empresarial. A interface do usuário da Web personalizável do IoT Central no permite que você monitore as condições do dispositivo, crie regras e gerencie milhões de dispositivos e seus dados durante todo o ciclo de vida. A superfície de API dentro de IoT Central fornece acesso programático para configurar e interagir com sua solução de IoT.

O Azure IoT Central é uma plataforma de aplicativo totalmente gerenciada que você pode usar para criar soluções personalizadas de IoT. IoT Central usa modelos de aplicativo para criar soluções. Há modelos para soluções genéricas e para setores específicos, como energia, saúde, governo e varejo. IoT Central modelos de aplicativo permitem que você implante um aplicativo IoT Central em minutos que você pode personalizar com temas, painéis e exibições.

Escolha dispositivos do [Catálogo de dispositivos Azure Certified para IOT](https://catalog.azureiotsolutions.com) para se conectar rapidamente à sua solução. Use a interface do usuário da Web do IoT Central para monitorar e gerenciar seus dispositivos para mantê-los íntegros e conectados. Use conectores e APIs para integrar seu aplicativo IoT Central com outros aplicativos de negócios.

Como uma plataforma de aplicativo totalmente gerenciada, IoT Central tem um modelo de preços simples e previsível.

## <a name="azure-iot-solution-accelerators"></a>Aceleradores de soluções do Azure IoT

Os [aceleradores de solução do Azure IOT](https://www.azureiotsolutions.com) são uma coleção de soluções personalizáveis de nível empresarial. Você pode implantar essas soluções como elas estão ou desenvolver uma solução de IoT personalizada usando o código-fonte Java ou .NET de código aberto.

Os aceleradores de solução do Azure IoT fornecem um alto nível de controle sobre sua solução de IoT. Os aceleradores de solução incluem soluções predefinidas para cenários comuns de IoT que você pode implantar em sua assinatura do Azure em minutos. Os cenários incluem:

  - Monitorização remota
  - Fábrica interligada
  - Manutenção preditiva
  - Simulação de dispositivo

A base de código-fonte aberto para todos os aceleradores de solução está disponível no GitHub. Baixe o código para personalizar um Solution Accelerator para atender aos seus requisitos específicos de IoT.

Os aceleradores de solução usam os serviços do Azure, como o Hub IoT do Azure e o armazenamento do Azure que você deve gerenciar em sua assinatura do Azure.

## <a name="custom-solutions"></a>Soluções personalizadas

Para criar uma solução de IoT do zero ou estender uma solução criada usando IoT Central ou um Solution Accelerator, use um ou mais dos seguintes serviços e tecnologias de IoT do Azure:

### <a name="devices"></a>Dispositivos

Desenvolva seus dispositivos IoT usando um dos [kits de início do Azure IOT](https://catalog.azureiotsolutions.com/kits) ou escolha um dispositivo para usar no [Catálogo de dispositivos Azure Certified para IOT](https://catalog.azureiotsolutions.com). Implemente seu código inserido usando os SDKs do [dispositivo](../iot-hub/iot-hub-devguide-sdks.md)de software livre. Os SDKs de dispositivo dão suporte a vários sistemas operacionais, como Linux, Windows e sistemas operacionais em tempo real. Há SDKs para várias linguagens de programação, como [C](https://github.com/Azure/azure-iot-sdk-c), [node. js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.net](https://github.com/Azure/azure-iot-sdk-csharp)e [Python](https://github.com/Azure/azure-iot-sdk-python).

Você pode simplificar ainda mais a forma como cria o código inserido para seus dispositivos usando o serviço de [visualização de plug and Play de IOT](../iot-pnp/overview-iot-plug-and-play.md) . O IoT Plug and Play permite que os desenvolvedores de soluções integrem dispositivos a suas soluções sem escrever nenhum código embutido. No núcleo do Plug and Play IoT, é um esquema de _modelo de capacidade de dispositivo_ que descreve os recursos do dispositivo. Use o modelo de funcionalidade do dispositivo para gerar o código de dispositivo incorporado e configurar uma solução baseada em nuvem, como um aplicativo IoT Central.

[Azure IOT Edge](../iot-edge/about-iot-edge.md) permite descarregar partes de sua carga de trabalho de IOT de seus serviços de nuvem do Azure para seus dispositivos. IoT Edge pode reduzir a latência em sua solução, reduzir a quantidade de dados que seus dispositivos trocam com a nuvem e habilitar cenários offline. Você pode gerenciar dispositivos IoT Edge de IoT Central e alguns aceleradores de solução.

O [Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) é uma plataforma de aplicativo segura e de alto nível, com recursos internos de comunicação e segurança para dispositivos conectados à Internet. Ele inclui uma unidade protegida do Microcontroller, um sistema operacional personalizado baseado em Linux e um serviço de segurança baseado em nuvem que fornece segurança contínua e renovável.

### <a name="cloud-connectivity"></a>Conectividade de nuvem

O serviço de [Hub IOT do Azure](../iot-hub/about-iot-hub.md) permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IOT e uma solução baseada em nuvem. O [serviço de provisionamento de dispositivos no Hub IOT do Azure](../iot-dps/about-iot-dps.md) é um serviço auxiliar para o Hub IOT. O serviço fornece provisionamento de dispositivos sem toque e Just-in-time para o Hub IoT correto sem a necessidade de intervenção humana. Esses recursos permitem que os clientes provisionem milhões de dispositivos de maneira segura e escalonável.

O Hub IoT é um componente fundamental dos aceleradores de solução e você pode usá-lo para atender aos desafios de implementação de IoT, como:

* Conectividade e gestão de dispositivos de volume elevado.
* Ingestão de telemetria de volume elevado.
* Comando e controlo de dispositivos.
* Imposição de segurança do dispositivo.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>A transição da lacuna entre os mundos físicos e digitais

O [Azure digital gêmeos](../digital-twins/about-digital-twins.md) é um serviço de IOT que permite modelar um ambiente físico. Ele usa um grafo de inteligência espacial para modelar as relações entre pessoas, espaços e dispositivos. Ao corelacionar dados em mundos digitais e físicos, você pode criar soluções com reconhecimento contextual.

A IOT central usa o gêmeos digital para sincronizar dispositivos e dados no mundo real com os modelos digitais que permitem aos usuários monitorar e gerenciar esses dispositivos conectados.

### <a name="data-and-analytics"></a>Dados e análise

Os dispositivos IoT normalmente geram grandes quantidades de dados de série temporal, como leituras de temperatura de sensores. [Azure Time Series insights](../time-series-insights/time-series-insights-overview.md) pode se conectar a um hub IOT, ler o fluxo de telemetria de seus dispositivos, armazená-los e permitir que você os consulte e visualize-os.

## <a name="next-steps"></a>Passos seguintes

Para uma experiência prática, experimente um dos guias de início rápido:

- [Criar um aplicativo de IoT Central do Azure](../iot-central/core/quick-deploy-iot-central.md)
- [Enviar telemetria de um dispositivo para um hub IoT](../iot-hub/quickstart-send-telemetry-cli.md)
- [Tentar uma solução de monitorização remota com base na cloud](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
