---
title: Tecnologias e soluções de Internet das Coisas (IoT) do Azure
description: Descreve a coleção de tecnologias e serviços que pode utilizar para construir uma solução Azure IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0678b2c36d341b2bc2bd9be755b9a6213acdd462
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206089"
---
# <a name="what-azure-technologies-and-services-can-you-use-to-create-iot-solutions"></a>Que tecnologias e serviços da Azure pode usar para criar soluções IoT?

As tecnologias e serviços Azure IoT fornecem-lhe opções para criar uma grande variedade de soluções IoT que permitem a transformação digital para a sua organização. Pode, por exemplo:

- Utilize [o Azure IoT Central,](https://apps.azureiotcentral.com)uma plataforma de aplicação IoT gerida, para construir e implementar uma solução IoT segura e de nível empresarial. A IoT Central apresenta uma coleção de modelos de aplicações específicos da indústria, como o retalho e os cuidados de saúde, para acelerar o seu processo de desenvolvimento de soluções.
- Alargar a base de código de código de código de código de código de código aberto para um acelerador de [solução](https://www.azureiotsolutions.com) Azure IoT para implementar um cenário IoT comum, como monitorização remota ou manutenção preditiva.
- Utilize serviços da plataforma Azure IoT, como o [Azure IoT Hub](../iot-hub/about-iot-hub.md) e o [dispositivo Azure IoT SDKs](../iot-hub/iot-hub-devguide-sdks.md) para construir uma solução IoT personalizada de raiz.

![Tecnologias, serviços e soluções Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

A [plataforma de aplicação IoT Central](https://apps.azureiotcentral.com) reduz os encargos e custos de desenvolvimento, gestão e manutenção de soluções IoT de nível empresarial. A UI web personalizável da IoT Central permite monitorizar as condições do dispositivo, criar regras e gerir milhões de dispositivos e seus dados ao longo do seu ciclo de vida. A superfície API dentro do IoT Central dá-lhe acesso programático à configuração e interagem com a sua solução IoT.

O Azure IoT Central é uma plataforma de aplicação totalmente gerida que pode usar para criar soluções IoT personalizadas. O IoT Central utiliza modelos de aplicação para criar soluções. Existem modelos para soluções genéricas e para indústrias específicas como a energia, os cuidados de saúde, o governo e o retalho. Os modelos de aplicação IoT Central permitem-lhe implementar uma aplicação IoT Central em minutos que pode depois personalizar com temas, dashboards e vistas.

Escolha os dispositivos do catálogo de [dispositivos Azure Certified for IoT](https://catalog.azureiotsolutions.com) para se ligar rapidamente à sua solução. Utilize a UI web IoT Central para monitorizar e gerir os seus dispositivos para mantê-los saudáveis e conectados. Utilize conectores e APIs para integrar a sua aplicação IoT Central com outras aplicações empresariais.

Como uma plataforma de aplicação totalmente gerida, a IoT Central tem um modelo de preços simples e previsível.

## <a name="azure-iot-solution-accelerators"></a>Aceleradores de soluções do Azure IoT

Os [aceleradores de solução Azure IoT](https://www.azureiotsolutions.com) são uma coleção de soluções de qualidade empresarial personalizáveis. Pode implementar estas soluções tal como estão, ou desenvolver uma solução IoT personalizada utilizando o código de código de código aberto Java ou .NET.

Os aceleradores de solução Azure IoT proporcionam um alto nível de controlo sobre a sua solução IoT. Os aceleradores de solução incluem soluções pré-construídas para cenários IoT comuns que pode implementar na sua subscrição Azure em minutos. Os cenários incluem:

  - Monitorização remota
  - Fábrica ligada
  - Manutenção preditiva
  - Simulação de dispositivo

A base de código de código de código de código de código de código aberta para todos os aceleradores de solução está disponível no GitHub. Faça o download do código para personalizar um acelerador de solução para satisfazer os seus requisitos específicos de IoT.

Os aceleradores de solução utilizam serviços Azure como o Azure IoT Hub e o Azure Storage que deve gerir na sua subscrição Azure.

## <a name="custom-solutions"></a>Soluções personalizadas

Para construir uma solução IoT de raiz, ou alargar uma solução criada com recurso a um acelerador de solução IoT Central, utilize uma ou mais das seguintes tecnologias e serviços Azure IoT:

### <a name="devices"></a>Dispositivos

Desenvolva os seus dispositivos IoT utilizando um dos [Kits de Arranque Azure IoT](https://catalog.azureiotsolutions.com/kits) ou escolha um dispositivo para utilizar a partir do catálogo de [dispositivos Azure Certified for IoT](https://catalog.azureiotsolutions.com). Implemente o seu código incorporado utilizando os [SDKs](../iot-hub/iot-hub-devguide-sdks.md)de dispositivo de código aberto . Os SDKs do dispositivo suportam vários sistemas operativos, tais como Linux, Windows e sistemas operativos em tempo real. Existem SDKs para várias linguagens de programação, tais como [C,](https://github.com/Azure/azure-iot-sdk-c) [Node.js, ](https://github.com/Azure/azure-iot-sdk-node) [Java,](https://github.com/Azure/azure-iot-sdk-java) [.NET](https://github.com/Azure/azure-iot-sdk-csharp)e [Python.](https://github.com/Azure/azure-iot-sdk-python)

Pode simplificar ainda mais a forma como cria o código incorporado para os seus dispositivos utilizando o serviço [IoT Plug and Play.](../iot-pnp/overview-iot-plug-and-play.md) O IoT Plug and Play permite aos desenvolvedores de soluções integrarem os dispositivos com as suas soluções sem escreverem qualquer código incorporado. No núcleo do IoT Plug and Play, encontra-se um esquema _de modelo de capacidade do dispositivo_ que descreve as capacidades do dispositivo. Utilize o modelo de capacidade do dispositivo para gerar o código do dispositivo incorporado e configurar uma solução baseada na nuvem, como uma aplicação IoT Central.

[O Azure IoT Edge](../iot-edge/about-iot-edge.md) permite-lhe descarregar partes da sua carga de trabalho IoT dos seus serviços de nuvem Azure para os seus dispositivos. O IoT Edge pode reduzir a latência na sua solução, reduzir a quantidade de dados que os seus dispositivos trocam com a nuvem e ativar cenários off-line. Pode gerir dispositivos IoT Edge da IoT Central e alguns aceleradores de solução.

[A Azure Sphere](/azure-sphere/product-overview/what-is-azure-sphere) é uma plataforma de aplicação segura e de alto nível com funcionalidades de comunicação e segurança incorporadas para dispositivos ligados à Internet. Inclui uma unidade de microcontrolador segura, um sistema operativo personalizado baseado em Linux e um serviço de segurança baseado na nuvem que fornece segurança contínua e renovável.

### <a name="cloud-connectivity"></a>Conectividade em nuvem

O serviço [Azure IoT Hub](../iot-hub/about-iot-hub.md) permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos IoT e uma solução baseada na nuvem. [O Serviço de Provisionamento de Dispositivos Azure IoT Hub](../iot-dps/about-iot-dps.md) é um serviço de ajuda para o IoT Hub. O serviço fornece o fornecimento de dispositivos de toque zero, just-in-time, para o centro IoT direito sem necessitar de intervenção humana. Estas capacidades permitem aos clientes a provisionar milhões de dispositivos de forma segura e escalável.

O IoT Hub é um componente central dos aceleradores de solução e pode usá-lo para responder a desafios de implementação de IoT, tais como:

* Conectividade e gestão de dispositivos de volume elevado.
* Ingestão de telemetria de volume elevado.
* Comando e controlo de dispositivos.
* Imposição de segurança do dispositivo.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Colmatar o fosso entre os mundos físico e digital

[Azure Digital Twins](../digital-twins/overview.md) é um serviço IoT que lhe permite modelar um ambiente físico. Usa um gráfico de inteligência espacial para modelar as relações entre pessoas, espaços e dispositivos. Ao corelatar dados em todos os mundos digital e físico, pode criar soluções contextualmente conscientes.

A Iot Central utiliza gémeos digitais para sincronizar dispositivos e dados no mundo real com os modelos digitais que permitem aos utilizadores monitorizar e gerir esses dispositivos conectados.

### <a name="data-and-analytics"></a>Dados e análises

Os dispositivos IoT normalmente geram grandes quantidades de dados de séries de tempo, tais como leituras de temperatura dos sensores. [O Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) pode ligar-se a um hub IoT, ler o fluxo de telemetria dos seus dispositivos, armazenar esses dados e permitir-lhe consultar e visualizar.

[O Azure Maps](../azure-maps/index.yml) é uma coleção de serviços geoespaciais que utilizam dados de mapeamento frescos para fornecer contexto geográfico preciso a aplicações web e móveis. Pode utilizar uma API REST, um controlo JavaScript baseado na Web ou um SDK Android para construir as suas aplicações.

## <a name="next-steps"></a>Passos seguintes

Para uma experiência prática, experimente um dos arranques rápidos:

- [Criar uma aplicação Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- [Enviar telemetria de um dispositivo para um hub IoT](../iot-hub/quickstart-send-telemetry-cli.md)
- [Experimentar uma solução de monitorização remota com base na cloud](../iot-accelerators/quickstart-remote-monitoring-deploy.md)