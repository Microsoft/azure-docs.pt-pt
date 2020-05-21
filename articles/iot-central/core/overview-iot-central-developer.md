---
title: Desenvolvimento de dispositivos para a Central Azure IoT [ Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicações IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral do desenvolvimento de dispositivos para se conectar à sua aplicação IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d29420d14a716e5a45e42dd681faa01f518f713a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666541"
---
# <a name="iot-central-device-development-overview"></a>Visão geral do desenvolvimento do dispositivo Central IoT

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Uma aplicação IoT Central permite-lhe monitorizar e gerir milhões de dispositivos ao longo do seu ciclo de vida. Esta visão geral destina-se aos desenvolvedores de dispositivos que implementam código para executar em dispositivos que se ligam ao IoT Central.

Os dispositivos interagem com uma aplicação IoT Central utilizando os seguintes primitivos:

- _Telemetria_ são dados que um dispositivo envia para a IoT Central. Por exemplo, um fluxo de valores de temperatura a partir de um sensor a bordo.
- _As propriedades_ são valores estatais que um dispositivo reporta à IoT Central. Por exemplo, a versão atual do firmware do dispositivo. Também pode ter propriedades de supreensíveis que a IoT Central pode atualizar no dispositivo.
- _Os comandos_ são chamados da IoT Central para controlar o comportamento de um dispositivo. Por exemplo, a sua aplicação IoT Central pode chamar um comando para reiniciar um dispositivo.

Um construtor de soluções é responsável por configurar dashboards e vistas na IoT Central web UI para visualizar a telemetria, gerir propriedades e chamar comandos.

## <a name="types-of-device"></a>Tipos de dispositivo

As seguintes secções descrevem os principais tipos de dispositivo que pode ligar a uma aplicação IoT Central:

### <a name="standalone-device"></a>Dispositivo autónomo

Um dispositivo autónomo liga-se diretamente à IoT Central. Um dispositivo autónomo normalmente envia telemetria dos seus sensores a bordo ou conectados para a sua aplicação IoT Central. Os dispositivos autónomos também podem reportar valores de propriedade, receber valores de propriedade writáveis e responder a comandos.

### <a name="gateway-device"></a>Dispositivo gateway

Um dispositivo gateway gere um ou mais dispositivos a jusante que se ligam à sua aplicação IoT Central. Usa a IoT Central para configurar as relações entre os dispositivos a jusante e o dispositivo gateway. Para saber mais, consulte Definir um novo tipo de [dispositivo ioT gateway na sua aplicação Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Dispositivo de borda

Um dispositivo de borda liga-se diretamente à IoT Central, mas funciona como um intermediário para outros dispositivos conhecidos como _dispositivos de folhas_. Um dispositivo de borda está tipicamente localizado perto dos dispositivos de folha para os quais está a agir como um intermediário. Os cenários que utilizam dispositivos de borda incluem:

- Ative dispositivos que não podem ligar-se diretamente à IoT Central para se ligar através do dispositivo de borda. Por exemplo, um dispositivo de folha pode usar bluetooth para ligar ao dispositivo de borda, que depois liga através da internet à IoT Central.
- Telemetria agregada antes de ser enviada para a IoT Central. Esta abordagem pode ajudar a reduzir os custos de envio de dados para a IoT Central.
- Controle os dispositivos de folhas localmente para evitar a latência associada à ligação à IoT Central através da internet.

Um dispositivo de borda também pode enviar a sua própria telemetria, reportar as suas propriedades e responder a atualizações e comandos de propriedade supreendíveis.

A IoT Central só vê o dispositivo de borda, não os dispositivos de folhaligados ao dispositivo de borda.

Para saber mais, consulte [Adicionar um dispositivo Azure IoT Edge à sua aplicação Central Azure IoT](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Ligar um dispositivo

A Azure IoT Central utiliza o serviço de provisionamento de [dispositivos Hub Azure IoT (DPS)](../../iot-dps/about-iot-dps.md) para gerir todo o registo e ligação do dispositivo.

A utilização de DPS permite:

- IoT Central para apoiar dispositivos de embarque e ligação em escala.
- Você gera credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Você usa as suas próprias iDs de dispositivo para registar dispositivos na IoT Central. A utilização dos seus próprios IDs de dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Para saber mais, consulte [Fique ligado à Central Azure IoT](./concepts-get-connected.md).

### <a name="security"></a>Segurança

A ligação entre um dispositivo e a sua aplicação IoT Central é assegurada utilizando assinaturas de [acesso partilhados](./concepts-get-connected.md#connect-devices-at-scale-using-sas) ou [certificados X.509](./concepts-get-connected.md#connect-devices-using-x509-certificates)padrão da indústria.

### <a name="communication-protocols"></a>Protocolos de comunicação

Os protocolos de comunicação que um dispositivo pode usar para ligar ao IoT Central incluem MQTT, AMQP e HTTPS. Internamente, a IoT Central utiliza um hub IoT para permitir a conectividade do dispositivo. Para obter mais informações sobre os protocolos de comunicação que o IoT Hub suporta para a conectividade do dispositivo, consulte Escolha um protocolo de [comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementar o dispositivo

Utilize um dos [SDKs do dispositivo Azure IoT](#languages-and-sdks) para implementar o comportamento do seu dispositivo. O código deve:

- Registe o dispositivo com DPS e utilize as informações do DPS para se ligar ao hub ioT interno na sua aplicação IoT Central.
- Envie telemetria no formato que o modelo do dispositivo na IoT Central especifica. A IoT Central usa o modelo do dispositivo para determinar como usar a telemetria para visualizações e análises.
- Sincronizar os valores de propriedade entre o dispositivo e a IoT Central. O modelo do dispositivo especifica os nomes de propriedade e os tipos de dados para que a IoT Central possa exibir a informação.
- Implementar comandos para os comandos especificados no modelo do dispositivo. O modelo do dispositivo especifica os nomes de comando e os parâmetros que o dispositivo deve utilizar.

Para mais informações, consulte Criar e ligar uma aplicação de [cliente Node.js](./tutorial-connect-device-nodejs.md) ou [Criar e ligar uma aplicação de cliente Python](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Línguas e SDKs

Para obter mais informações sobre as línguas suportadas e SDKs, consulte [Compreender e utilizar sDKs do dispositivo Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Próximos passos

Se é um desenvolvedor de dispositivos e quer mergulhar em algum código, o próximo passo sugerido é criar e ligar uma aplicação de [cliente à sua aplicação Azure IoT Central](./tutorial-connect-device-nodejs.md).

Se quiser saber mais sobre a utilização da IoT Central, os próximos passos sugeridos são para experimentar os quickstarts, começando com [a Create a Azure IoT Central](./quick-deploy-iot-central.md)application .
