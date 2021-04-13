---
title: Glossário de termos Azure IoT | Microsoft Docs
description: Guia do desenvolvedor - um glossário que explica alguns dos termos comuns usados nos artigos Azure IoT.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: a45b8db1f17ead8e55bac2b2bf82aa44cb7f080e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304704"
---
# <a name="glossary-of-iot-terms"></a>Glossário de termos IoT

Este artigo enumera alguns dos termos comuns utilizados nos artigos IoT.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Protocolo avançado de fila de mensagens

[O Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) é um dos protocolos de mensagens que o [IoT Hub](#iot-hub) suporta para comunicar com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [Enviar e receber mensagens com o IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="allocation-policy"></a>Política de atribuição

No [Serviço de Provisionamento de Dispositivos,](#device-provisioning-service)a política de atribuição determina como o serviço atribui dispositivos aos [hubs IoT ligados](#linked-iot-hub).

### <a name="attestation-mechanism"></a>Mecanismo de atestação

No [Serviço de Provisionamento de Dispositivos,](#device-provisioning-service)o mecanismo de atestação é o método utilizado para confirmar a identidade de um dispositivo. O mecanismo de atestação está configurado numa [inscrição.](#enrollment)

Os mecanismos de atestação incluem certificados X.509, Módulos de Plataforma Fidedigna e chaves simétricas.

### <a name="automatic-deployment"></a>Implantação automática

No IoT Edge, uma implementação automática configura um conjunto de dispositivos IoT Edge para executar um conjunto de módulos IoT Edge. Cada implementação garante continuamente que todos os dispositivos que correspondam à sua condição alvo estão a executar o conjunto especificado de módulos, mesmo quando novos dispositivos são criados ou modificados para corresponder à condição alvo. Cada dispositivo IoT Edge só recebe a maior prioridade de implementação cuja condição de alvo cumpre. Saiba mais sobre [a implementação automática IoT Edge](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Configuração automática do dispositivo

A sua solução traseira pode usar [configurações automáticas](../iot-hub/iot-hub-automatic-device-management.md) do dispositivo para atribuir as propriedades desejadas a um conjunto de [gémeos](#device-twin) do dispositivo e reportar o estado usando métricas do sistema e métricas personalizadas.

### <a name="automatic-device-management"></a>Gestão automática de dispositivos

A gestão automática de dispositivos no Azure IoT Hub automatiza muitas das tarefas repetitivas e complexas de gestão de grandes frotas de dispositivos ao longo de todo o seu ciclo de vida. Com a Gestão Automática de Dispositivos, pode direcionar um conjunto de dispositivos com base nas suas propriedades, definir uma configuração desejada e deixar que os dispositivos de atualização do IoT Hub sempre que eles entram no âmbito.  Consiste em [configurações automáticas](../iot-hub/iot-hub-automatic-device-management.md) de dispositivos e [implementações automáticas IoT Edge](../iot-edge/how-to-deploy-at-scale.md).

### <a name="azure-digital-twins"></a>Azure Digital Twins

A Azure Digital Twins é uma plataforma como um serviço (PaaS) que oferece para criar representações digitais de coisas do mundo real, lugares, processos de negócios e pessoas. Construa gráficos de conhecimento que representem ambientes inteiros, e use-os para obter insights para impulsionar melhores produtos, otimizar operações e custos, e criar experiências inovadoras para o cliente. Para saber mais, consulte [a Azure Digital Twins.](../digital-twins/index.yml)

### <a name="azure-digital-twins-instance"></a>Exemplo de Gémeos Digitais Azure

Uma única instância do serviço Azure Digital Twins na subscrição de um cliente. Enquanto [a Azure Digital Twins](#azure-digital-twins) se refere ao serviço Azure como um todo, a sua **instância** Azure Digital Twins é o seu recurso individual Azure Digital Twins.

### <a name="azure-iot-device-sdks"></a>SDKs de dispositivo Azure IoT

Existem _SDKs de dispositivos_ disponíveis para vários idiomas que permitem criar aplicações de [dispositivos](#device-app) que interagem com um hub IoT. Os tutoriais do IoT Hub mostram-lhe como usar estes SDKs deste dispositivo. Pode encontrar o código-fonte e mais informações sobre os SDKs do dispositivo neste [repositório](https://github.com/Azure/azure-iot-sdks)GitHub .

### <a name="azure-iot-explorer"></a>Explorador Azure IoT

O [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) é utilizado para visualizar a telemetria que o dispositivo está a enviar, trabalhar com as propriedades do dispositivo e chamar comandos. Também pode utilizar o explorador para interagir e testar os seus dispositivos, e para gerir [dispositivos IoT Plug e Play](#iot-plug-and-play-device).

### <a name="azure-iot-service-sdks"></a>SDKs de serviço Azure IoT

Existem _SDKs_ de serviço disponíveis para vários idiomas que lhe permitem criar [aplicações de back-end](#back-end-app) que interagem com um hub IoT. Os tutoriais IoT Hub mostram-lhe como usar estes SDKs de serviço. Pode encontrar o código fonte e mais informações sobre os SDKs de serviço neste [repositório](https://github.com/Azure/azure-iot-sdks)GitHub .

### <a name="azure-iot-tools"></a>Ferramentas do Azure IoT

O [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) é uma extensão de código de estúdio visual de código aberto de plataforma aberta que o ajuda a gerir o Azure IoT Hub e dispositivos em CÓDIGO VS. Com a Azure IoT Tools, os desenvolvedores de IoT poderiam desenvolver o projeto IoT em VS Code com facilidade.

## <a name="b"></a>B

### <a name="back-end-app"></a>App back-end

No contexto do [IoT Hub,](#iot-hub)uma aplicação back-end é uma aplicação que se conecta a um dos pontos finais voltados para o serviço num hub IoT. Por exemplo, uma aplicação de back-end pode recuperar mensagens [de dispositivo para nuvem](#device-to-cloud) ou gerir o registo de [identidade](#identity-registry). Normalmente, uma aplicação back-end corre na nuvem, mas em muitos dos tutoriais as aplicações de back-end são aplicações de consola que estão a ser executados na sua máquina de desenvolvimento local.

### <a name="built-in-endpoints"></a>Pontos finais embutidos

Um tipo de [ponto final](#endpoint) que é incorporado no Hub IoT. Cada hub IoT inclui um [ponto final](../iot-hub/iot-hub-devguide-endpoints.md) incorporado que é compatível com o Event Hub. Pode utilizar qualquer mecanismo que funcione com os Centros de Eventos para ler mensagens dispositivo-a-nuvem a partir deste ponto final.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Gateway de nuvens

Um gateway de nuvem permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub). Um gateway de nuvens está hospedado na nuvem em contraste com um [gateway de campo](#field-gateway) que corre localmente para os seus dispositivos. Um caso de utilização típico de um gateway em nuvem é implementar a tradução de protocolos para os seus dispositivos.

### <a name="cloud-to-device"></a>Nuvem-para-dispositivo

Refere-se a mensagens enviadas de um hub IoT para um dispositivo conectado. Muitas vezes, estas mensagens são comandos que instruem o dispositivo a tomar uma ação. Para mais informações, consulte [Enviar e receber mensagens com o IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Comandos

No IoT Plug and Play, os comandos definidos numa [interface](#interface) representam métodos que podem ser executados no [twin digital](#digital-twin). Por exemplo, uma ordem para reiniciar um dispositivo.

### <a name="component"></a>Componente

No IoT Plug and Play e no Azure Digital Twins, os componentes permitem construir uma [interface](#interface) de modelo como conjunto de outras interfaces. Um [modelo de dispositivo](#device-model) pode combinar várias interfaces como componentes. Por exemplo, um modelo pode incluir um componente do interruptor e o componente do termóstato. Vários componentes de um modelo também podem usar o mesmo tipo de interface. Por exemplo, um modelo pode incluir dois componentes do termóstato.

### <a name="configuration"></a>Configuração

No contexto da [configuração automática](../iot-hub/iot-hub-automatic-device-management.md)do dispositivo, uma configuração dentro do IoT Hub define a configuração desejada para um conjunto de dispositivos gémeos e fornece um conjunto de métricas para reportar o estado e o progresso.

### <a name="connection-string"></a>Cadeia de ligação

Utiliza cadeias de ligação no código da aplicação para encapsular as informações necessárias para se ligar a um ponto final. Uma cadeia de ligação normalmente inclui o endereço do ponto final e informações de segurança, mas os formatos de cadeia de ligação variam entre os serviços. Existem dois tipos de cadeia de ligação associadas ao serviço IoT Hub:

- *As cordas de ligação* do dispositivo permitem que os dispositivos se conectem aos pontos finais virados para o dispositivo num hub IoT.

- *As cadeias de ligação IoT Hub* permitem que as aplicações de back-end se conectem aos pontos finais voltados para o serviço num hub IoT.

### <a name="custom-endpoints"></a>Pontos finais personalizados

Pode criar [pontos finais personalizados](../iot-hub/iot-hub-devguide-endpoints.md) num hub IoT para entregar mensagens despachadas por uma [regra de encaminhamento](#routing-rules). Os pontos finais personalizados ligam-se diretamente a um hub de Eventos, a uma fila de autocarros de serviço ou a um tópico de Service Bus.

### <a name="custom-gateway"></a>Porta de entrada personalizada

Um gateway permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub). Você pode usar Azure IoT Edge para construir gateways personalizados que implementam lógica personalizada para lidar com mensagens, conversões de protocolo personalizado e outros processamentos no limite.

## <a name="d"></a>D

### <a name="data-point-message"></a>Mensagem de ponto de dados

Uma mensagem de ponto de dados é uma mensagem [de dispositivo para nuvem](#device-to-cloud) que contém dados de [telemetria,](#telemetry) como velocidade do vento ou temperatura.

### <a name="default-component"></a>Componente predefinido

No IoT Plug and Play, todos os [modelos do dispositivo](#device-model) têm um componente predefinido. Um modelo de dispositivo simples tem apenas um componente padrão - tal modelo também é conhecido como um dispositivo sem componente. Um modelo mais complexo tem vários componentes aninhados por baixo do componente padrão.

### <a name="deployment-manifest"></a>Manifesto de implementação

No [IoT Edge,](#iot-edge)o manifesto de implantação é um documento JSON que contém as informações a copiar num ou mais módulos de dispositivos IoT Edge twin(s) para implantar um conjunto de módulos, rotas e propriedades associadas do módulo desejado.

### <a name="desired-configuration"></a>Configuração desejada

No contexto de um [dispositivo gémeo,](../iot-hub/iot-hub-devguide-device-twins.md)a configuração desejada refere-se ao conjunto completo de propriedades e metadados no dispositivo twin que devem ser sincronizados com o dispositivo.

### <a name="desired-properties"></a>Propriedades desejadas

No contexto de um [dispositivo gémeo,](../iot-hub/iot-hub-devguide-device-twins.md)as propriedades desejadas são uma subsecção do dispositivo twin que é usada com [propriedades relatadas](#reported-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades desejadas só podem ser definidas por uma [aplicação de back-end](#back-end-app) e são observadas pela aplicação do [dispositivo.](#device-app)

### <a name="device"></a>Dispositivo

No contexto do IoT, um dispositivo é tipicamente um dispositivo de computação autónomo e de pequena escala que pode recolher dados ou controlar outros dispositivos. Por exemplo, um dispositivo pode ser um dispositivo de monitorização ambiental, ou um controlador para os sistemas de rega e ventilação numa estufa. O catálogo de [dispositivos](https://catalog.azureiotsolutions.com/) fornece uma lista de dispositivos de hardware certificados para trabalhar com [o IoT Hub](#iot-hub).

### <a name="device-app"></a>Aplicação do dispositivo

Uma aplicação de dispositivo funciona no seu [dispositivo](#device) e trata da comunicação com o seu [hub IoT](#iot-hub). Normalmente, utiliza-se um dos [SDKs do dispositivo Azure IoT](#azure-iot-device-sdks) quando implementa uma aplicação para dispositivos. Em muitos dos tutoriais de IoT, você usa um [dispositivo simulado](#simulated-device) para conveniência.

### <a name="device-builder"></a>Construtor de dispositivos

Um construtor de [dispositivos](#interface) utiliza um modelo e interfaces de [dispositivo](#device-model) ao implementar código para ser executado num [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Os construtores de dispositivos normalmente usam um dos [SDKs do dispositivo Azure IoT](#azure-iot-device-sdks) para implementar o cliente do dispositivo.

### <a name="device-certification"></a>Certificação de dispositivos

O programa de certificação ioT Plug and Play verifica que um dispositivo cumpre os requisitos de certificação IoT Plug and Play. Pode adicionar um dispositivo certificado ao catálogo de [dispositivos Azure IoT certificados.](https://aka.ms/devicecatalog)

### <a name="device-condition"></a>Condição do dispositivo

Consulte as informações do estado do dispositivo, como o método de conectividade atualmente em uso, conforme reportado por uma [aplicação do dispositivo](#device-app). [As aplicações do dispositivo](#device-app) também podem reportar as suas capacidades. Pode consultar informações sobre a condição e a capacidade utilizando gémeos do dispositivo.

### <a name="device-data"></a>Dados do dispositivo

Os dados do dispositivo referem-se aos dados por dispositivo armazenados no [registo de identidade](#identity-registry)do IoT Hub . É possível importar e exportar estes dados.

### <a name="device-identity"></a>Identidade do dispositivo

A identidade do dispositivo (ou ID do dispositivo) é o identificador único atribuído a todos os dispositivos registados no [registo de identidade](#identity-registry)do IoT Hub .

### <a name="device-management"></a>Gestão de dispositivos

A gestão do dispositivo engloba todo o ciclo de vida associado à gestão dos dispositivos na sua solução IoT, incluindo planeamento, provisionamento, configuração, monitorização e aposentação.

### <a name="device-management-patterns"></a>Padrões da gestão de dispositivos

[O hub IoT](#iot-hub) permite padrões comuns de gestão de dispositivos, incluindo reiniciar, executar resets de fábrica e realizar atualizações de firmware nos seus dispositivos.

### <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo é um tipo de [modelo](#model) que utiliza a [Linguagem de Definição de Gémeos Digitais](#digital-twins-definition-language-dtdl) para descrever as capacidades de um dispositivo IoT Plug and Play. Um modelo de dispositivo simples utiliza uma única interface para descrever as capacidades do dispositivo. Um modelo de dispositivo mais complexo inclui vários componentes, cada um dos quais descreve um conjunto de capacidades. Para saber mais, consulte [componentes IoT Plug e Play nos modelos](../iot-pnp/concepts-components.md).

### <a name="device-modeling"></a>Modelação do dispositivo

Um [construtor de dispositivos](#device-builder) ou [construtor de módulos](#module-builder)utiliza a [Linguagem de Definição de Gémeos Digitais](#digital-twins-definition-language-dtdl) para modelar as capacidades de um dispositivo [IoT Plug and Play](#iot-plug-and-play-device). Um [construtor de soluções](#solution-builder) pode configurar uma solução IoT do modelo.

### <a name="device-provisioning"></a>Provisão de dispositivos

O fornecimento de dispositivos é o processo de adição dos [dados iniciais](#device-data) do dispositivo às lojas na sua solução. Para permitir a ligação de um novo dispositivo ao seu hub, deve adicionar um ID do dispositivo e chaves ao [registo de identidade](#identity-registry)do IoT Hub . Como parte do processo de provisionamento, poderá ser necessário inicializar dados específicos do dispositivo noutras lojas de soluções.

### <a name="device-provisioning-service"></a>Serviço de Aprovisionamento do Dispositivos

O IoT Hub Device Provisioning Service (DPS) é um serviço de ajuda para [o IoT Hub](#iot-hub) que utiliza para configurar o fornecimento de dispositivos de toque zero a um hub IoT especificado. Com o DPS, podes providenciar milhões de dispositivos de forma segura e escalável.

### <a name="device-rest-api"></a>API de repouso do dispositivo

Pode utilizar a [API do Device REST](/rest/api/iothub/device) a partir de um dispositivo para enviar mensagens dispositivo-a-nuvem para um hub IoT e receber mensagens [nuvem-dispositivo](#cloud-to-device) a partir de um hub IoT. Normalmente, deve utilizar um dos [SDKs](#azure-iot-device-sdks) de dispositivo de nível superior, como mostrado nos tutoriais do IoT Hub.

### <a name="device-twin"></a>Dispositivo duplo

Um dispositivo gémeo é o documento JSON que armazena informações do estado do dispositivo, tais como metadados, configurações e condições. O IoT Hub persiste num dispositivo gémeo para cada dispositivo que fornece no seu hub IoT. Os gémeos do dispositivo permitem sincronizar as condições e configurações do dispositivo entre o dispositivo e a parte traseira da solução. Pode consultar os gémeos do dispositivo para localizar dispositivos específicos e para o estado das operações de longa duração.

### <a name="device-to-cloud"></a>Dispositivo-a-nuvem

Refere-se a mensagens enviadas de um dispositivo ligado ao [IoT Hub](#iot-hub). Estas mensagens podem ser [ponto de dados](#data-point-message) ou mensagens [interativas.](#interactive-message) Para mais informações, consulte [Enviar e receber mensagens com o IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Gémeo digital

Um gémeo digital é uma coleção de dados digitais que representa um objeto físico. As mudanças no objeto físico refletem-se no gémeo digital. Em alguns cenários, pode usar o gémeo digital para manipular o objeto físico. O [serviço Azure Digital Twins](../digital-twins/index.yml) usa [modelos expressos](#model) na [Linguagem de Definição de Gémeos Digitais (DTDL)](#digital-twins-definition-language-dtdl) para representar gémeos digitais de dispositivos físicos ou conceitos de negócios abstratos de alto nível, permitindo uma ampla gama de soluções digitais de twin baseadas na nuvem. Um dispositivo [IoT Plug and Play](../iot-pnp/index.yml) tem um twin digital, descrito por um modelo de [dispositivo](#device-model)DTDL .

### <a name="digital-twin-change-events"></a>Eventos de alteração de duplos digitais

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) está ligado a um hub IoT, o hub pode usar a sua capacidade de encaminhamento para enviar notificações de alterações digitais de gémeos. Por exemplo, sempre que um valor [de propriedade](#properties) muda num dispositivo, o IoT Hub pode enviar uma notificação para um ponto final, como um hub de Evento.

### <a name="digital-twin-route"></a>Rota digital twin

Uma rota criada num hub IoT para entregar [eventos digitais de mudança de gémeos](#digital-twin-change-events) a um ponto final como um hub de Eventos.

### <a name="digital-twins-definition-language-dtdl"></a>Linguagem Digital Twins Definition (DTDL)

Uma linguagem JSON-LD para descrever [modelos](#model) e [interfaces](#interface) para [dispositivos IoT Plug e Play](#iot-plug-and-play-device) e entidades [Azure Digital Twins.](../digital-twins/index.yml) Utilize a versão 2 da [Definição de Gémeos Digitais](https://github.com/Azure/opendigitaltwins-dtdl) para descrever as capacidades de um gémeo [digital](#digital-twin) e permitir que a plataforma IoT e as soluções IoT utilizem a semântica da entidade. A linguagem de definição de gémeos digitais é frequentemente abreviada como DTDL.

### <a name="direct-method"></a>Método direto

Um [método direto](../iot-hub/iot-hub-devguide-direct-methods.md) é uma forma de desencadear um método para executar num dispositivo invocando uma API no seu hub IoT.

### <a name="downstream-services"></a>Serviços a jusante

Um termo relativo descrevendo serviços que recebem dados do contexto atual. Por exemplo, se estiver a pensar no contexto de Azure Digital Twins, a [Time Series Insights](../time-series-insights/index.yml) seria considerada um serviço a jusante se configurasse os seus dados para fluir de Azure Digital Twins para Time Series Insights.

## <a name="e"></a>E

### <a name="endpoint"></a>Ponto final

Uma representação nomeada de um serviço de encaminhamento de dados que pode receber dados de outros serviços.

Um hub IoT expõe vários [pontos finais](../iot-hub/iot-hub-devguide-endpoints.md) que permitem que as suas aplicações se conectem ao hub IoT. Existem pontos finais virados para dispositivos que permitem aos dispositivos realizar operações como o envio de mensagens [de dispositivo para nuvem](#device-to-cloud) e receber mensagens [cloud-to-device.](#cloud-to-device) Existem pontos finais de gestão virados para o serviço que permitem que [as aplicações de back-end](#back-end-app) realizem operações como a gestão [da identidade](#device-identity) do dispositivo e a gestão de twins de dispositivos. Existem [pontos finais incorporados voltados](#built-in-endpoints) para o serviço para a leitura de mensagens dispositivo-a-nuvem. Pode criar [pontos finais personalizados](#custom-endpoints) para receber mensagens dispositivo-a-nuvem despachadas por uma [regra de encaminhamento](#routing-rules).

### <a name="enrollment"></a>Inscrição

No [Serviço de Provisionamento de Dispositivos,](#device-provisioning-service)uma inscrição é o registo de dispositivos individuais ou grupos de dispositivos que podem registar-se com um [hub IoT linked](#linked-iot-hub) através de autoprovisionamento.

### <a name="enrollment-group"></a>Grupo de inscrições

No [Serviço de Provisionamento de Dispositivos,](#device-provisioning-service)um grupo de inscrições identifica um grupo de dispositivos que partilham um mecanismo de [atestado](#attestation-mechanism)de chave X.509 ou simétrico .

### <a name="event-handlers"></a>Processadores de eventos

Isto pode referir-se a qualquer processo que seja desencadeado pela chegada de um evento e faça alguma ação de processamento. Uma forma de criar manipuladores de eventos é adicionando o código de processamento de eventos a uma função Azure, e enviando dados através dele usando [pontos finais](#endpoint) e [encaminhamento de eventos](#event-routing).

### <a name="event-hub-compatible-endpoint"></a>Ponto final compatível com o Centro de Eventos

Para ler mensagens de dispositivo para nuvem enviadas para o seu hub IoT, pode [ligar-se](#device-to-cloud) a um ponto final no seu hub e utilizar qualquer método compatível com o Event Hub para ler essas mensagens. Os métodos compatíveis com o Centro de Eventos incluem a utilização dos [SDKs do Event Hubs](../event-hubs/event-hubs-programming-guide.md) e [do Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md)

### <a name="event-routing"></a>Encaminhamento de eventos

O processo de envio de eventos e os seus dados de um dispositivo ou serviço para o [ponto final](#endpoint) de outro. 

No Iot Hub, pode definir [regras de encaminhamento](#routing-rules) para descrever como as mensagens devem ser enviadas. No Azure Digital Twins, as rotas de eventos são entidades que são criadas para o efeito. As rotas de eventos Azure Digital Twins podem conter filtros para limitar que tipos de eventos são enviados para cada ponto final.

## <a name="f"></a>F

### <a name="field-gateway"></a>Porta de entrada de campo

Um gateway de campo permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub) e é normalmente implantado localmente com os seus dispositivos. Para mais informações, veja [o que é Azure IoT Hub?](../iot-hub/about-iot-hub.md)

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Um gateway permite a conectividade para dispositivos que não podem ligar-se diretamente ao [IoT Hub](#iot-hub). Consulte também [o Field Gateway,](#field-gateway) [Cloud Gateway](#cloud-gateway)e Custom [Gateway](#custom-gateway).

### <a name="gateway-device"></a>Dispositivo gateway

Um dispositivo é um exemplo de um gateway de [campo.](#field-gateway) Um dispositivo de gateway pode ser [um dispositivo](#device) IoT padrão ou um [dispositivo IoT Edge](#iot-edge-device).

Um dispositivo de gateway permite a conectividade para dispositivos a jusante que não podem ligar-se diretamente ao [IoT Hub](#iot-hub).

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Módulo de segurança de hardware

Um módulo de segurança de hardware (HSM) é utilizado para armazenamento seguro e baseado em hardware de segredos de dispositivos. É a forma mais segura de armazenamento secreto para um dispositivo. Tanto os certificados X.509 como as teclas simétricas podem ser armazenados num HSM. No [serviço de provisionamento de dispositivos,](#device-provisioning-service)um [mecanismo de atestado](#attestation-mechanism) pode utilizar um HSM.

## <a name="i"></a>I

### <a name="id-scope"></a>Âmbito de ID

O âmbito de ID é um valor único atribuído a uma instância do Serviço de Provisionamento de [Dispositivos (DPS)](#device-provisioning-service) quando é criado.

As aplicações IoT Central utilizam as instâncias DPS e disponibilizam o ID Scope através da IoT Central UI.

### <a name="identity-registry"></a>Registo de identidade

O [registo de identidade](../iot-hub/iot-hub-devguide-identity-registry.md) é o componente incorporado de um hub IoT que armazena informações sobre os dispositivos individuais autorizados a ligar-se a um hub IoT.

### <a name="individual-enrollment"></a>Inscrição individual

No [Serviço de Provisionamento de Dispositivos,](#device-provisioning-service)uma inscrição individual identifica um único dispositivo que utiliza um certificado de folha X.509 ou uma chave simétrica como [mecanismo de atestação](#attestation-mechanism).

### <a name="interactive-message"></a>Mensagem interativa

Uma mensagem interativa é uma mensagem [nuvem-para-dispositivo](#cloud-to-device) que desencadeia uma ação imediata na parte traseira da solução. Por exemplo, um dispositivo pode enviar um alarme sobre uma falha que deve ser automaticamente iniciada num sistema CRM.

### <a name="interface"></a>Interface

No IoT Plug and Play, uma interface descreve as capacidades relacionadas que são implementadas por um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) ou por [gémeos digitais.](#digital-twin) Pode reutilizar interfaces em diferentes [modelos de dispositivos.](#device-model) Quando uma interface é utilizada num modelo de dispositivo, define um [componente](#component) do dispositivo. Um simples dispositivo contém apenas uma interface padrão.

Em Azure Digital Twins, *a interface* pode ser usada para se referir ao item de código de nível superior numa definição de modelo [DTDL.](#digital-twins-definition-language-dtdl)

### <a name="iot-edge"></a>IoT Edge

O Azure IoT Edge permite a implantação em nuvem de serviços Azure e código específico de solução para dispositivos no local. [Os dispositivos IoT Edge](#iot-edge-device) podem agregar dados de outros dispositivos para realizar computação e análise antes de enviar os dados para a nuvem. Para saber mais, consulte [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>Agente IoT Edge

A parte do tempo de funcionamento do IoT Edge responsável pela implantação e monitorização de módulos.

### <a name="iot-edge-device"></a>Dispositivo do IoT Edge

Um dispositivo IoT Edge utiliza módulos IoT Edge contentorizados para executar [serviços](#modules) Azure, serviços de terceiros ou o seu próprio código. No dispositivo IoT Edge, o [tempo de execução IoT Edge](#iot-edge-runtime) gere os módulos. Pode monitorizar e gerir remotamente um dispositivo IoT Edge a partir da nuvem.

### <a name="iot-edge-hub"></a>Hub IoT Edge

A parte do tempo de funcionação do IoT Edge responsável pelas comunicações do módulo para módulos, a montante (em direção ao IoT Hub) e a jusante (longe do IoT Hub).

### <a name="iot-edge-runtime"></a>Runtime do IoT Edge

O tempo de execução do IoT Edge inclui tudo o que a Microsoft distribui para ser instalado num dispositivo IoT Edge. Inclui o agente Edge, o edge hub e o daemon de segurança IoT Edge.

### <a name="iot-extension-for-azure-cli"></a>Extensão IoT para Azure CLI

[A extensão IoT para Azure CLI](https://github.com/Azure/azure-iot-cli-extension) é uma ferramenta de linha de comando transversal. A ferramenta permite-lhe gerir os seus dispositivos no [registo de identidade,](#identity-registry)enviar e receber mensagens e ficheiros dos seus dispositivos e monitorizar as operações do seu hub IoT.

### <a name="iot-hub"></a>IoT Hub

O IoT Hub é um serviço Azure totalmente gerido que permite comunicações bidirecionais fiáveis e seguras entre milhões de dispositivos e uma solução traseira. Para mais informações, veja [o que é Azure IoT Hub?](../iot-hub/about-iot-hub.md) Utilizando a sua subscrição Azure, pode criar hubs IoT para lidar com as suas cargas de trabalho de mensagens IoT.

### <a name="iot-hub-metrics"></a>Métricas IoT Hub

As métricas do IoT Hub dão-lhe dados sobre o estado dos hubs IoT na sua subscrição Azure. As métricas IoT Hub permitem avaliar a saúde geral do serviço e os dispositivos que lhe estão ligados. As métricas do IoT Hub podem ajudá-lo a ver o que se passa com o seu hub IoT e investigar problemas de causa de raiz sem precisar de contactar o suporte do Azure. Para saber mais, consulte [o Monitor IoT Hub.](../iot-hub/monitor-iot-hub.md)

### <a name="iot-hub-query-language"></a>IoT Hub linguagem de consulta

A [linguagem de consulta IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) é uma linguagem semelhante ao SQL que lhe permite consultar o seu [Job,](#job)gémeos digitais e gémeos de dispositivos.

### <a name="iot-hub-resource-rest-api"></a>IoT Hub Resource REST API

Pode utilizar a [API IoT Hub Resource REST](/rest/api/iothub/iothubresource) para gerir os hubs IoT na sua subscrição Azure, realizando operações como criar, atualizar e eliminar centros.

### <a name="iot-plug-and-play-bridge"></a>Bridge do IoT Plug and Play

IoT Plug and Play bridge é uma aplicação de código aberto que permite que sensores e periféricos existentes ligados aos gateways Windows ou Linux se conectem como [dispositivos IoT Plug e Play](#iot-plug-and-play-device).

### <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Espera-se que [os dispositivos](#iot-plug-and-play-device) IoT Plug e Play sigam um conjunto de convenções quando trocam dados com uma solução.

### <a name="iot-plug-and-play-device"></a>IoT Plug and Play dispositivo

Um dispositivo IoT Plug and Play é tipicamente um dispositivo de computação autónomo e de pequena escala que recolhe dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de dispositivo.](#device-model)  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitorização ambiental, ou um controlador para um sistema de irrigação de agricultura inteligente. Um dispositivo IoT Plug and Play pode ser implementado diretamente ou como um módulo IoT Edge. Pode escrever uma solução IoT hospedada em nuvem para comandar, controlar e receber dados de dispositivos IoT Plug e Play.

### <a name="iot-solution-accelerators"></a>Aceleradores de soluções IoT

Aceleradores de solução Azure IoT embalam vários serviços Azure em soluções. Estas soluções permitem-lhe começar rapidamente com implementações de ponta a ponta de cenários comuns de IoT. Para mais informações, veja quais são os aceleradores de [solução Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>Tarefa

A sua solução back-end pode usar [empregos](../iot-hub/iot-hub-devguide-jobs.md) para agendar e rastrear atividades num conjunto de dispositivos registados no seu hub IoT. As atividades incluem a atualização das [propriedades duplas desejadas](#desired-properties)do dispositivo, a atualização de [etiquetas gémeas](#tags)do dispositivo e a invocação de [métodos diretos.](#direct-method) [O IoT Hub](#iot-hub) também utiliza para [importar e exportar](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) do registo de [identidade.](#identity-registry)

## <a name="l"></a>L

### <a name="leaf-device"></a>Dispositivo de folha

No [IoT Edge,](#iot-edge)um dispositivo de folha é um dispositivo sem dispositivo a jusante.

### <a name="lifecycle-event"></a>Evento de ciclo de vida

Em Azure Digital Twins, este tipo de evento é disparado quando um item de dados - como um gémeo digital, uma relação ou um manipulador de eventos - é criado ou eliminado a partir da sua instância Azure Digital Twins.

### <a name="linked-iot-hub"></a>Hub IoT ligado

O [Serviço de Provisionamento de Dispositivos (DPS)](#device-provisioning-service)pode fornecer dispositivos aos hubs IoT que lhe tenham sido associados. A ligação de um hub IoT a uma instância DPS permite ao serviço registar um ID do dispositivo e definir a configuração inicial no twin do dispositivo.

## <a name="m"></a>M

### <a name="model"></a>Modelação

Um modelo define um tipo de entidade no seu ambiente físico, incluindo as suas propriedades, telemetrias, componentes e, por vezes, outras informações. Os modelos são usados para criar [gémeos digitais](#digital-twin) que representam objetos físicos específicos deste tipo. Os modelos estão escritos na [Linguagem de Definição de Gémeos Digitais.](#digital-twins-definition-language-dtdl)

No [serviço Azure Digital Twins,](../digital-twins/index.yml)os modelos podem definir dispositivos ou conceitos de negócio abstratos de alto nível. No [IoT Plug and Play,](../iot-pnp/index.yml)os [modelos de dispositivos](#device-model) são utilizados para descrever especificamente os dispositivos.

### <a name="model-id"></a>ID do Modelo

Quando um dispositivo IoT Plug and Play se conecta a um Hub IoT, envia o **ID modelo** do modelo [DTDL](#digital-twins-definition-language-dtdl) que implementa. Este ID permite encontrar a solução para encontrar o modelo do dispositivo.

### <a name="model-repository"></a>Repositório de modelos

Um repositório modelo armazena [modelos](#device-model) e interfaces de [dispositivos.](#interface)

### <a name="model-repository-rest-api"></a>Modelo repositório REST API

Uma API para gerir e interagir com o repositório de modelos. Por exemplo, pode utilizar a API para adicionar e procurar [modelos de dispositivos.](#device-model)

### <a name="module-builder"></a>Construtor de módulos

Um construtor de [módulos](#interface) utiliza um modelo de [dispositivo](#device-model) e interfaces ao implementar código para ser executado num [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Os construtores de módulos implementam o código como módulo ou módulo IoT Edge para implementar no tempo de funcionaamento do IoT Edge num dispositivo.

### <a name="module-identity"></a>Identidade do módulo

A identidade do módulo é o identificador único atribuído a cada módulo que pertence a um dispositivo. A identidade do módulo também está registada no [registo de identidade.](#identity-registry)

O módulo identifica detalhes das credenciais de segurança que o módulo utiliza para autenticar com o [Hub IoT](#iot-hub) ou, no caso de um módulo IoT Edge para o [hub IoT Edge](#iot-edge-hub).

### <a name="module-image"></a>Imagem do módulo

A imagem de estiva que o [tempo de execução IoT Edge](#iot-edge-runtime) usa para instantaneaizar instâncias de módulos.

### <a name="module-twin"></a>Módulo gémeo

Semelhante ao dispositivo twin, um módulo twin é o documento JSON que armazena informações do estado do módulo, tais como metadados, configurações e condições. O IoT Hub persiste num módulo gémeo para cada identidade de módulo que fornece sob uma identidade do dispositivo no seu hub IoT. Os gémeos módulos permitem sincronizar as condições e configurações do módulo entre o módulo e a parte traseira da solução. Pode consultar os gémeos do módulo para localizar módulos específicos e consultar o estado das operações de longa duração.

### <a name="modules"></a>Módulos

Do lado do dispositivo, os SDKs do dispositivo IoT Hub permitem criar [módulos](../iot-hub/iot-hub-devguide-module-twins.md) onde cada um abre uma ligação independente ao IoT Hub. Esta funcionalidade permite-lhe utilizar espaços de nome separados para diferentes componentes do seu dispositivo.

A identidade do módulo e o módulo gémeo fornecem as mesmas capacidades que a identidade do [dispositivo](#device-identity) e [o dispositivo gémeo,](#device-twin) mas com uma granularidade mais fina. Esta granularidade mais fina permite que dispositivos capazes, tais como dispositivos baseados no sistema operativo ou dispositivos de firmware que gerem vários componentes, isolem a configuração e as condições para cada um desses componentes.

No [IoT Edge,](#iot-edge)um módulo é um recipiente Docker que pode ser implantado em dispositivos IoT Edge. Executa uma tarefa específica, como ingerir uma mensagem a partir de um dispositivo, transformar uma mensagem ou enviar uma mensagem para um hub IoT. Comunica com outros módulos e envia dados para o [tempo de funcionação do IoT Edge.](#iot-edge-runtime)

### <a name="mqtt"></a>MQTT

[O MQTT](https://mqtt.org/) é um dos protocolos de mensagens que o [IoT Hub](#iot-hub) suporta para comunicar com dispositivos. Para obter mais informações sobre os protocolos de mensagens que o IoT Hub suporta, consulte [Enviar e receber mensagens com o IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="ontology"></a>Ontologia

Um conjunto de modelos para um determinado domínio, como imobiliário, cidades inteligentes, sistemas IoT, redes de energia, e muito mais. As ontologias são frequentemente usadas como esquemas para gráficos de conhecimento como os de [Azure Digital Twins,](#azure-digital-twins)porque fornecem um ponto de partida baseado nos padrões da indústria e nas melhores práticas.

Para mais informações sobre ontologias, veja [o que é uma ontologia?](../digital-twins/concepts-ontologies.md)

### <a name="operations-monitoring"></a>Monitorização de operações

A [monitorização das operações](../iot-hub/iot-hub-operations-monitoring.md) do IoT Hub permite-lhe monitorizar o estado das operações no seu hub IoT em tempo real. [O IoT Hub](#iot-hub) acompanha eventos em várias categorias de operações. Pode optar pelo envio de eventos de uma ou mais categorias para um ponto final do IoT Hub para processamento. Pode monitorizar os dados por erros ou configurar um processamento mais complexo com base em padrões de dados.

## <a name="p"></a>P

### <a name="physical-device"></a>Dispositivo físico

Um dispositivo físico é um dispositivo real, como um Raspberry Pi que se conecta a um hub IoT. Por conveniência, muitos dos tutoriais do IoT Hub usam [dispositivos simulados](#simulated-device) para que possa executar amostras na sua máquina local.

### <a name="primary-and-secondary-keys"></a>Chaves primárias e secundárias

Quando se liga a um ponto final virado para o dispositivo ou virado para o serviço num hub IoT, a sua cadeia de [ligação](#connection-string) inclui a chave para lhe dar acesso. Quando adiciona um dispositivo ao [registo de identidade](#identity-registry) ou adiciona uma política de acesso [partilhado](#shared-access-policy) ao seu hub, o serviço gera uma chave primária e secundária. Ter duas teclas permite-lhe rolar de uma chave para outra quando atualiza uma chave sem perder o acesso ao hub IoT.

### <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos numa [interface](#interface) que representam algum estado persistente de um [gémeo digital.](#digital-twin) Pode declarar propriedades apenas para leitura ou legitável. As propriedades apenas de leitura, como o número de série, são definidas por código em execução no [dispositivo IoT Plug e Play.](#iot-plug-and-play-device) As propriedades writable, como um limiar de alarme, são tipicamente definidas a partir da solução IoT baseada na nuvem.

### <a name="property-change-event"></a>Evento de mudança de propriedade

Um evento que resulta de uma mudança de propriedade num [gémeo digital.](#digital-twin)

### <a name="protocol-gateway"></a>Gateway de protocolo

Um portal de protocolo é normalmente implantado na nuvem e fornece serviços de tradução protocolar para dispositivos que se ligam ao [IoT Hub](#iot-hub). Para mais informações, veja [o que é Azure IoT Hub?](../iot-hub/about-iot-hub.md)

## <a name="r"></a>R

### <a name="registration"></a>Registo

Um registo é o registo de um dispositivo no [registo de identidade](#identity-registry)IoT Hub . Pode registar-se ou dispositivo diretamente, ou utilizar o [Serviço de Provisionamento de Dispositivos](#device-provisioning-service) para automatizar o registo do dispositivo.

### <a name="registration-id"></a>ID de inscrição

O ID de registo é utilizado para identificar de forma única um [registo](#registration) de dispositivos com o [Serviço de Provisionamento de Dispositivos.](#device-provisioning-service) O ID de registo pode ter o mesmo valor que a identidade do [Dispositivo.](#device-identity)

### <a name="relationship"></a>Relação

No serviço [Azure Digital Twins,](../digital-twins/index.yml) as relações são usadas para ligar [gémeos digitais](#digital-twin) a gráficos de conhecimento que representam digitalmente todo o seu ambiente físico. Os tipos de relacionamentos que os seus gémeos podem ter são definidos como parte das definições de [modelo](#model) das gémeas - o modelo [DTDL](#digital-twins-definition-language-dtdl) para um determinado tipo de gémeos inclui informações sobre as relações que pode ter com outros gémeos.

### <a name="reported-configuration"></a>Configuração reportada

No contexto de um [dispositivo gémeo,](../iot-hub/iot-hub-devguide-device-twins.md)a configuração reportada refere-se ao conjunto completo de propriedades e metadados no twin do dispositivo que devem ser reportados à extremidade traseira da solução.

### <a name="reported-properties"></a>Propriedades reportadas

No contexto de um [dispositivo gémeo,](../iot-hub/iot-hub-devguide-device-twins.md)as propriedades relatadas são uma subsecção do dispositivo twin utilizado com [propriedades desejadas](#desired-properties) para sincronizar a configuração ou condição do dispositivo. As propriedades reportadas só podem ser definidas pela [aplicação](#device-app) do dispositivo e podem ser lidas e consultadas por uma [aplicação de back-end](#back-end-app).

### <a name="retry-policy"></a>Política de Repetição

Utilize uma política de repetição para lidar com [erros transitórios](/azure/architecture/best-practices/transient-faults) quando se liga a um serviço de nuvem.

### <a name="routing-rules"></a>Regras de encaminhamento

Configura [as regras de encaminhamento](../iot-hub/iot-hub-devguide-messages-read-custom.md) no seu hub IoT para encaminhar mensagens dispositivo-a-nuvem para um [ponto final incorporado](#built-in-endpoints) ou para [pontos finais personalizados](#custom-endpoints) para processamento através da sua solução back end.

## <a name="s"></a>S

### <a name="sasl-plain"></a>PLANÍCIE SASL

SASL PLAIN é um protocolo que o protocolo AMQP usa para transferir fichas de segurança.

### <a name="service-operations-endpoint"></a>Ponto final das operações de serviço

Um [ponto final](#endpoint) para gerir as definições de serviço utilizadas por um administrador de serviço. Por exemplo, no [Serviço de Provisionamento de Dispositivos](#device-provisioning-service) utiliza-se o ponto final do serviço para gerir as matrículas.

### <a name="service-rest-api"></a>API REST do serviço

Pode utilizar a [API de assistência](/rest/api/iothub/service/configuration) rest a partir da parte traseira da solução para gerir os seus dispositivos. A API permite-lhe recuperar e atualizar propriedades [gémeas](#device-twin) do dispositivo, invocar [métodos diretos](#direct-method)e agendar [trabalhos.](#job) Normalmente, deve utilizar um dos [SDKs](#azure-iot-service-sdks) de serviço de alto nível, como mostrado nos tutoriais do IoT Hub.

### <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma política de acesso partilhado define as permissões concedidas a qualquer pessoa que tenha uma [chave primária ou secundária](#primary-and-secondary-keys) válida associada a essa política. Pode gerir as políticas de acesso partilhado e as chaves para o seu hub no portal.

### <a name="shared-access-signature"></a>Assinatura de acesso partilhado

As Assinaturas de Acesso Partilhado (SAS) são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. A autenticação SAS tem dois componentes: uma _Política de Acesso Partilhado_ e uma Assinatura de Acesso _Partilhado_ (muitas vezes chamada de símbolo). Um dispositivo utiliza SAS para autenticar com um hub IoT. [As aplicações back-end](#back-end-app) também usam SAS para autenticar com os pontos finais voltados para o serviço num hub IoT. Normalmente, você inclui o token SAS na [cadeia de conexão](#connection-string) que uma aplicação usa para estabelecer uma ligação a um hub IoT.

### <a name="simulated-device"></a>Dispositivo simulado

Por conveniência, muitos dos tutoriais do IoT Hub usam dispositivos simulados para que possa executar amostras na sua máquina local. Em contraste, um [dispositivo físico](#physical-device) é um dispositivo real, como um Raspberry Pi que se conecta a um hub IoT.

### <a name="solution"></a>Solução

Uma _solução_ pode referir-se a uma solução visual Studio que inclui um ou mais projetos. Uma _solução_ também pode referir-se a uma solução IoT que inclui elementos como dispositivos, [aplicações para dispositivos,](#device-app)um hub IoT, outros serviços Azure e [aplicações de back-end.](#back-end-app)

### <a name="solution-builder"></a>Construtor de soluções

Um construtor de soluções cria a solução traseira. Um construtor de soluções normalmente trabalha com recursos Azure, como IoT Hub e [repositórios de modelos.](#model-repository)

### <a name="system-properties"></a>Propriedades do sistema

No contexto de um [dispositivo gémeo,](../iot-hub/iot-hub-devguide-device-twins.md)as propriedades do sistema são apenas de leitura e incluem informações sobre o uso do dispositivo, como o último tempo de atividade e estado de ligação.

## <a name="t"></a>T

### <a name="tags"></a>Etiquetas

No contexto de um [dispositivo gémeo,](../iot-hub/iot-hub-devguide-device-twins.md)as etiquetas são metadados do dispositivo armazenados e recuperados pela solução traseira sob a forma de um documento JSON. As etiquetas não são visíveis para aplicações num dispositivo.

### <a name="target-condition"></a>Condição do alvo

Numa implementação IoT Edge, a condição alvo seleciona os dispositivos-alvo da implantação, por exemplo **tag.environment = prod**. A condição alvo é continuamente avaliada para incluir quaisquer novos dispositivos que satisfaçam os requisitos ou removam dispositivos que já não o façam.

### <a name="telemetry"></a>Telemetria

Os dispositivos recolhem dados de telemetria, como velocidade do vento ou temperatura, e utilizam mensagens de ponto de dados para enviar a telemetria para um hub IoT.

No IoT Plug and Play e no Azure Digital Twins, os campos de telemetria definidos numa [interface](#interface) representam medições. Estas medições são tipicamente valores como leituras de sensores que são enviadas por dispositivos, como [ioT plug e dispositivos Play](#iot-plug-and-play-device), como um fluxo de dados.

Ao contrário das [propriedades,](#properties)a telemetria não é armazenada num [gémeo digital;](#digital-twin) é um fluxo de eventos de dados ligados ao tempo que precisam de ser tratados à medida que ocorrem.

### <a name="telemetry-event"></a>Evento de telemetria

Um evento que indica a chegada de dados de telemetria.

### <a name="token-service"></a>Serviço Token

Pode utilizar um serviço de fichas para implementar um mecanismo de autenticação para os seus dispositivos. Utiliza uma [política de acesso compartilhado](#shared-access-policy) IoT Hub com permissões **DeviceConnect** para criar fichas com âmbito de *dispositivo.* Estes tokens permitem que um dispositivo se conecte ao seu hub IoT. Um dispositivo utiliza um mecanismo de autenticação personalizado para autenticar com o serviço token. Se o dispositivo autenticar com sucesso, o serviço token emite um token SAS para o dispositivo utilizar para aceder ao seu hub IoT.

### <a name="twin-graph-or-digital-twin-graph"></a>Gráfico duplo (ou gráfico digital de dois gráficos)

No serviço [Azure Digital Twins,](../digital-twins/index.yml) pode conectar [gémeos digitais](#digital-twin) com [relações](#relationship) para criar gráficos de conhecimento que representam digitalmente todo o seu ambiente físico. Uma única [instância Azure Digital Twins](#azure-digital-twins-instance) pode hospedar muitos gráficos desligados, ou um único gráfico interligado.

### <a name="twin-queries"></a>Consultas gémeas

[As consultas de módulo e módulo usam](../iot-hub/iot-hub-devguide-query-language.md) a linguagem de consulta IoT Hub semelhante ao SQL para obter informações dos gémeos do seu dispositivo ou gémeos módulos. Você pode usar o mesmo idioma de consulta IoT Hub para obter informações sobre um [Trabalho](#job) em execução no seu hub IoT.

### <a name="twin-synchronization"></a>Sincronização gémea

A sincronização gémea utiliza as [propriedades desejadas](#desired-properties) no seu dispositivo gémeos ou gémeos módulos para configurar os seus dispositivos ou módulos e recuperar [propriedades relatadas](#reported-properties) dos mesmos para armazenar no gémeo.

## <a name="u"></a>U

### <a name="upstream-services"></a>Serviços a montante

Um termo relativo descrevendo serviços que alimentam dados no contexto atual. Por exemplo, se estiver a pensar no contexto das Gémeas Digitais Azure, o IoT Hub é considerado um serviço a montante porque os dados fluem do IoT Hub para o Azure Digital Twins.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>Certificado de cliente X.509

Um dispositivo pode utilizar um certificado X.509 para autenticar com [o IoT Hub](#iot-hub). A utilização de um certificado X.509 é uma alternativa à utilização de um [token SAS](#shared-access-signature).
