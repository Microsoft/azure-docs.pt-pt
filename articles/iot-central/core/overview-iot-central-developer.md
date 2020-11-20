---
title: Desenvolvimento de dispositivos para Azure IoT Central Microsoft Docs
description: O Azure IoT Central é uma plataforma de aplicações IoT que simplifica a criação de soluções de IoT. Este artigo fornece uma visão geral do desenvolvimento de dispositivos para ligar à sua aplicação IoT Central. Os dispositivos utilizam a telemetria para enviar dados e propriedades de streaming para reportar o estado do dispositivo. A Iot Central pode definir o estado do dispositivo utilizando propriedades writáveis e chamar comandos num dispositivo.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 82818c8db326889079948cd2b32b2ed0be6ab50d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990759"
---
# <a name="iot-central-device-development-overview"></a>Descrição geral do desenvolvimento de dispositivos do IoT Central

*Este artigo aplica-se aos desenvolvedores de dispositivos.*

Uma aplicação IoT Central permite monitorizar e gerir milhões de dispositivos ao longo do seu ciclo de vida. Esta visão geral destina-se a programadores de dispositivos que implementem código para executar em dispositivos que se ligam à IoT Central.

Os dispositivos interagem com uma aplicação IoT Central utilizando os seguintes primitivos:

- _Telemetria_ são dados que um dispositivo envia para a IoT Central. Por exemplo, um fluxo de valores de temperatura a partir de um sensor de bordo.
- _As propriedades_ são valores estatais que um dispositivo reporta à IoT Central. Por exemplo, a versão atual do firmware do dispositivo. Também pode ter propriedades writable que o IoT Central pode atualizar no dispositivo, como uma temperatura-alvo.
- _Os comandos_ são chamados da IoT Central para controlar o comportamento de um dispositivo. Por exemplo, a sua aplicação IoT Central pode chamar um comando para reiniciar um dispositivo.

Um construtor de soluções é responsável por configurar dashboards e vistas na UI web IoT Central para visualizar telemetria, gerir propriedades e chamar comandos.

## <a name="types-of-device"></a>Tipos de dispositivo

As seguintes secções descrevem os principais tipos de dispositivo que pode ligar a uma aplicação IoT Central:

### <a name="standalone-device"></a>Dispositivo autónomo

Um dispositivo autónomo liga-se diretamente à IoT Central. Um dispositivo autónomo normalmente envia telemetria a partir dos seus sensores a bordo ou ligados à sua aplicação IoT Central. Os dispositivos autónomos também podem reportar valores de propriedade, receber valores de propriedade writable e responder a comandos.

### <a name="gateway-device"></a>Dispositivo gateway

Um dispositivo gateway gere um ou mais dispositivos a jusante que se ligam à sua aplicação IoT Central. Você usa o IoT Central para configurar as relações entre os dispositivos a jusante e o dispositivo gateway. Para saber mais, consulte [Definir um novo tipo de dispositivo de gateway IoT na sua aplicação Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Dispositivo de borda

Um dispositivo de borda liga-se diretamente à IoT Central, mas funciona como um intermediário para outros dispositivos conhecidos como _dispositivos de folha_. Um dispositivo de borda está tipicamente localizado perto dos dispositivos de folha para os quais está a agir como um intermediário. Os cenários que utilizam dispositivos de borda incluem:

- Ativar os dispositivos que não conseguem ligar-se diretamente à IoT Central para se ligarem através do dispositivo de borda. Por exemplo, um dispositivo de folha pode usar bluetooth para ligar ao dispositivo de borda, que depois se conecta através da internet à IoT Central.
- Telemetria agregada antes de ser enviada para a IoT Central. Esta abordagem pode ajudar a reduzir os custos do envio de dados para a IoT Central.
- Controle os dispositivos de folha localmente para evitar a latência associada à ligação à IoT Central através da internet.

Um dispositivo de borda também pode enviar a sua própria telemetria, reportar as suas propriedades, e responder a atualizações e comandos de propriedade writable.

A IoT Central só vê o dispositivo de borda, não os dispositivos de folha ligados ao dispositivo de borda.

Para saber mais, consulte [Adicione um dispositivo Azure IoT Edge à sua aplicação Azure IoT Central](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Ligar um dispositivo

A Azure IoT Central utiliza o [serviço de provisionamento de dispositivos Azure IoT Hub (DPS)](../../iot-dps/about-iot-dps.md) para gerir todo o registo e ligação do dispositivo.

A utilização de DPS permite:

- IoT Central para suportar dispositivos de embarque e ligação à escala.
- Você deve gerar credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Você deve usar os seus próprios IDs de dispositivo para registar dispositivos na IoT Central. A utilização dos iDs do seu próprio dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Para saber mais, consulte [Get connected to Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Segurança

A ligação entre um dispositivo e a sua aplicação IoT Central é assegurada utilizando [assinaturas de acesso partilhado](./concepts-get-connected.md#sas-group-enrollment) ou [certificados X.509 padrão](./concepts-get-connected.md#x509-group-enrollment)da indústria .

### <a name="communication-protocols"></a>Protocolos de comunicação

Os protocolos de comunicação que um dispositivo pode usar para ligar à IoT Central incluem MQTT, AMQP e HTTPS. Internamente, a IoT Central utiliza um hub IoT para permitir a conectividade do dispositivo. Para obter mais informações sobre os protocolos de comunicação que o IoT Hub suporta para a conectividade do dispositivo, consulte [Escolha um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementar o dispositivo

Um modelo de dispositivo IoT Central inclui um _modelo_ que especifica os comportamentos que um dispositivo desse tipo deve implementar. Os comportamentos incluem telemetria, propriedades e comandos.

> [!TIP]
> Pode exportar o modelo da IoT Central como um ficheiro [Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON.

Cada modelo tem um _identificador de modelo duplo_ de dispositivo único (DTMI), como `dtmi:com:example:Thermostat;1` . Quando um dispositivo se liga à IoT Central, envia o DTMI do modelo que implementa. O IoT Central pode então associar o modelo correto do dispositivo ao dispositivo.

[O IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) define um conjunto de convenções que um dispositivo deve seguir quando implementa um modelo DTDL.

Os [SDKs do dispositivo Azure IoT](#languages-and-sdks) incluem suporte para as convenções IoT Plug and Play.

### <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo é definido usando o [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Esta linguagem permite definir:

- A telemetria que o dispositivo envia. A definição inclui o nome e o tipo de dados da telemetria. Por exemplo, um dispositivo envia telemetria de temperatura como um duplo.
- As propriedades que o dispositivo reporta à IoT Central. Uma definição de propriedade inclui o seu nome e tipo de dados. Por exemplo, um dispositivo relata o estado de uma válvula como um Boolean.
- As propriedades que o dispositivo pode receber da IoT Central. Opcionalmente, pode marcar uma propriedade como credível. Por exemplo, a IoT Central envia uma temperatura-alvo como um duplo para um dispositivo.
- O comando a que um dispositivo responde. A definição inclui o nome do comando, e os nomes e tipos de dados de quaisquer parâmetros. Por exemplo, um dispositivo responde a um comando de reinicialização que especifica quantos segundos esperar antes de reiniciar.

Um modelo DTDL pode ser um _modelo sem componentes_ ou _multi-componentes:_

- Modelo sem componentes: Um modelo simples não utiliza componentes embutidos ou em cascata. Toda a telemetria, propriedades e comandos são definidos como um único _componente predefinido_. Por exemplo, consulte o modelo [do termóstato.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)
- Modelo multi-componente. Um modelo mais complexo que inclui dois ou mais componentes. Estes componentes incluem um único componente predefinido e um ou mais componentes aninhados adicionais. Por exemplo, consulte o modelo [do Controlador de Temperatura.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)

Para saber mais, consulte [componentes IoT Plug e Play nos modelos](../../iot-pnp/concepts-components.md)

### <a name="conventions"></a>Convenções

Um dispositivo deve seguir as convenções IoT Plug and Play quando este troca dados com a IoT Central. As convenções incluem:

- Envie o DTMI quando ligar à IoT Central.
- Envie cargas e metadados JSON corretamente formatados para a IoT Central.
- Responda corretamente às propriedades e comandos writable da IoT Central.
- Siga as convenções de nomeação para comandos de componentes.

> [!NOTE]
> Atualmente, a IoT Central não suporta totalmente os tipos de dados DTDL **Array** e **Geoespatial.**

Para saber mais sobre o formato das mensagens JSON que um dispositivo troca com a IoT Central, consulte [telemetria, propriedade e cargas de comando.](concepts-telemetry-properties-commands.md)

Para saber mais sobre as convenções IoT Plug and Play, consulte as [convenções IoT Plug and Play](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>SDKs de dispositivo

Utilize um dos [SDKs do dispositivo Azure IoT](#languages-and-sdks) para implementar o comportamento do seu dispositivo. O código deve:

- Registe o dispositivo com DPS e utilize as informações do DPS para ligar ao hub IoT interno na sua aplicação IoT Central.
- Anuncie o DTMI do modelo que o dispositivo implementa.
- Envie telemetria no formato que o modelo do dispositivo especifica. A IoT Central utiliza o modelo no modelo do dispositivo para determinar como utilizar a telemetria para visualizações e análises.
- Sincronizar os valores de propriedade entre o dispositivo e a IoT Central. O modelo especifica os nomes de propriedade e os tipos de dados para que a IoT Central possa exibir as informações.
- Implementar os controladores para os comandos especificados no modelo. O modelo especifica os nomes de comando e os parâmetros que o dispositivo deve utilizar.

Para obter mais informações sobre o papel dos modelos do dispositivo, consulte [quais são os modelos do dispositivo?](./concepts-device-templates.md)

Para obter algum código de amostra, consulte [Criar e ligue uma aplicação Node.js ao cliente](./tutorial-connect-device-nodejs.md) ou crie e [conecte uma aplicação do cliente Python.](./tutorial-connect-device-python.md)

### <a name="languages-and-sdks"></a>Línguas e SDKs

Para obter mais informações sobre as línguas e SDKs suportados, consulte [Compreender e utilizar SDKs do dispositivo Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Próximos passos

Se é um desenvolvedor de dispositivos e quer mergulhar em algum código, o próximo passo sugerido é [criar e ligar uma aplicação do cliente à sua aplicação Azure IoT Central](./tutorial-connect-device-nodejs.md).

Se quiser saber mais sobre a utilização da IoT Central, os próximos passos sugeridos são experimentar os quickstarts, começando com [a configuração de uma aplicação Azure IoT Central](./quick-deploy-iot-central.md).
