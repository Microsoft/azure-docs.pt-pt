---
title: Introdução ao IoT Plug and Play ! Microsoft Docs
description: Saiba mais sobre ioT Plug e Play. O IoT Plug and Play baseia-se numa linguagem de modelação aberta que permite que dispositivos IoT inteligentes declarem as suas capacidades. Os dispositivos IoT apresentam essa declaração, chamada modelo de dispositivo, quando se ligam a soluções em nuvem. A solução em nuvem pode então compreender automaticamente o dispositivo e começar a interagir com ele, tudo sem escrever qualquer código.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 3a5d0ba92ab976f9ea3aa2722f92865f06d5a70e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045045"
---
# <a name="what-is-iot-plug-and-play"></a>O que é o IoT Plug and Play?

O IoT Plug and Play permite aos construtores de soluções integrar dispositivos inteligentes com as suas soluções sem qualquer configuração manual. No cerne do IoT Plug and Play, está um _modelo_ de dispositivo que um dispositivo utiliza para anunciar as suas capacidades a uma aplicação IoT Plug e Play. Este modelo é estruturado como um conjunto de elementos que definem:

- _Propriedades_ que representam o estado apenas de leitura ou de um dispositivo ou de outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade apenas de leitura e uma temperatura-alvo num termóstato pode ser uma propriedade writável.
- _Telemetria_ que são os dados emitidos por um dispositivo, se os dados são um fluxo regular de leituras de sensores, um erro ocasional ou uma mensagem de informação.
- _Comandos_ que descrevem uma função ou operação que pode ser feita num dispositivo. Por exemplo, um comando poderia reiniciar um portal ou tirar uma fotografia usando uma câmara remota.

Pode agrupar estes elementos em interfaces para reutilizar os modelos para facilitar a colaboração e acelerar o desenvolvimento.

Para que o IoT Plug and Play funcione com [as Gémeas Digitais Azure,](../digital-twins/overview.md)define modelos e interfaces utilizando a [Linguagem de Definição de Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). O IoT Plug and Play e o DTDL estão abertos à comunidade, e a Microsoft congratula-se com a colaboração com clientes, parceiros e indústria. Ambos são baseados em padrões W3C abertos, tais como JSON-LD e RDF, o que permite uma adoção mais fácil entre serviços e ferramentas.

Não há custos adicionais para a utilização de IoT Plug e Play e DTDL. As tarifas padrão para [o Azure IoT Hub](../iot-hub/about-iot-hub.md) e outros serviços Azure permanecem as mesmas.

Este artigo descreve:

- Os papéis típicos associados a um projeto que usa IoT Plug e Play.
- Como utilizar dispositivos IoT Plug e Play na sua aplicação.
- Como desenvolver uma aplicação de dispositivo IoT que suporta ioT Plug e Play.

## <a name="user-roles"></a>Funções de utilizador

IoT Plug and Play é útil para dois tipos de desenvolvedores:

- Um _construtor de soluções_ é responsável pelo desenvolvimento de uma solução IoT utilizando o Azure IoT Hub e outros recursos Azure, e para identificar dispositivos IoT para integrar.
- Um _construtor de dispositivos_ cria o código que funciona num dispositivo ligado à sua solução.

## <a name="use-iot-plug-and-play-devices"></a>Utilize dispositivos IoT Plug e Play

Como construtor de soluções, pode desenvolver uma solução IoT hospedada em nuvem que utiliza dispositivos IoT Plug e Play. Use [o IoT Hub](../iot-hub/about-iot-hub.md) - um serviço de nuvem gerido, que funciona como um centro de mensagens para uma comunicação segura e bidcional entre a sua aplicação IoT e os seus dispositivos.

Quando ligar um dispositivo IoT Plug and Play a um hub IoT, pode utilizar a ferramenta [exploradora Azure IoT](./howto-use-iot-explorer.md) para visualizar a telemetria, propriedades e comandos definidos nas interfaces que compõem o modelo.

Se tiver sensores existentes ligados a um gateway Windows ou Linux, pode utilizar a [ponte IoT Plug e Play](./concepts-iot-pnp-bridge.md), para ligar estes sensores e criar dispositivos IoT Plug e Play sem a necessidade de escrever software/firmware do dispositivo (para [protocolos suportados).](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)

## <a name="develop-an-iot-device-application"></a>Desenvolver uma aplicação de dispositivo IoT

Como construtor de dispositivos, pode desenvolver um produto de hardware IoT que suporta ioT Plug e Play. O processo inclui três passos-chave:

1. Defina o modelo do dispositivo. É autor de um conjunto de ficheiros JSON que definem as capacidades do seu dispositivo utilizando o [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Um modelo descreve uma entidade completa, como um produto físico, e define o conjunto de interfaces implementadas por essa entidade. As interfaces são contratos partilhados que identificam exclusivamente a telemetria, propriedades e comandos suportados por um dispositivo. As interfaces podem ser reutilizadas em diferentes modelos.

1. O software ou firmware do dispositivo de autor de forma a que a sua telemetria, propriedades e comandos sigam as convenções IoT Plug and Play. Se estiver a ligar os sensores existentes ligados a um portal Windows ou Linux, a [ponte IoT Plug e Play](./concepts-iot-pnp-bridge.md) pode simplificar este passo.

1. O dispositivo anuncia o ID do modelo como parte da ligação MQTT. O Azure IoT SDK inclui novas construções para fornecer o ID do modelo na hora da ligação.

> [!Important]
> Os dispositivos IoT Plug e Play devem utilizar MQTT ou MQTT sobre WebSockets. Outros protocolos, como AMQP ou HTTP, não são válidos para implementar dispositivos IoT Plug e Play.

## <a name="device-certification"></a>Certificação de dispositivos

O [programa de certificação ioT Plug and Play](howto-certify-device.md) verifica que um dispositivo cumpre os requisitos de certificação IoT Plug and Play. Pode adicionar um dispositivo certificado ao catálogo de [dispositivos Azure IoT certificados.](https://aka.ms/devicecatalog)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral do IoT Plug and Play, o próximo passo sugerido é experimentar um dos arranques rápidos:

- [Ligue um dispositivo ao Hub IoT (C)](./quickstart-connect-device-c.md)
- [Interagir com um dispositivo a partir da sua solução (Node.js)](./quickstart-service-node.md)