---
title: Glossário de termos - IoT Plug and Play / Microsoft Docs
description: Conceitos - um glossário de termos comuns relativos ao IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d44866e2d04ab1bab5d2eca01374350a7d73a0ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577344"
---
# <a name="glossary-of-terms-for-iot-plug-and-play"></a>Glossário de termos para IoT Plug and Play

Definições de termos comuns como usados nos artigos IoT Plug and Play.

## <a name="azure-iot-explorer-tool"></a>Ferramenta exploradora Azure IoT

O explorador Azure IoT é uma ferramenta gráfica que pode utilizar para interagir e testar os seus [dispositivos IoT Plug and Play](#iot-plug-and-play-device). Depois de instalar a ferramenta na sua máquina local, pode usá-la para:

- Ver os dispositivos ligados ao seu [hub IoT](#azure-iot-hub).
- Ligue-se a um dispositivo IoT Plug and Play.
- Ver os [componentes](#component)do dispositivo .
- Veja a [telemetria](#telemetry) que o dispositivo envia.
- Trabalhar com [as propriedades do](#properties)dispositivo.
- Comandos do dispositivo [de chamada](#commands).

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT é um serviço gerido, alojado na cloud, que atua como um centro de mensagens para assegurar a comunicação bidirecional entre a aplicação IoT e os dispositivos que esta gere. [Os dispositivos IoT Plug e Play](#iot-plug-and-play-device) podem ligar-se a um hub IoT. Uma solução IoT utiliza um hub IoT para permitir:

- Dispositivos para enviar telemetria para uma solução baseada na nuvem.
- Uma solução baseada em nuvem para gerir dispositivos conectados.

## <a name="azure-iot-device-sdk"></a>Dispositivo Azure IoT SDK

Existem SDKs de dispositivo para vários idiomas que pode usar para construir aplicações de clientes ioT plug e play. Utilizar **dispositivosCliente** para dispositivos e **MódulosCliente** para módulos e módulos IoT Edge.

## <a name="commands"></a>Comandos

Os comandos definidos numa [interface](#interface) representam métodos que podem ser executados no [gémeo digital](#digital-twin). Por exemplo, uma ordem para reiniciar um dispositivo.

## <a name="component"></a>Componente

Os componentes permitem construir uma [interface](#interface) de modelo como conjunto de outras interfaces. Um [modelo de dispositivo](#device-model) pode combinar várias interfaces como componentes. Por exemplo, um modelo pode incluir um componente do interruptor e o componente do termóstato. Vários componentes de um modelo também podem usar o mesmo tipo de interface. Por exemplo, um modelo pode incluir dois componentes do termóstato.

## <a name="connection-string"></a>Cadeia de ligação

Uma cadeia de ligação encapsula as informações necessárias para ligar a um ponto final. Uma cadeia de ligação normalmente inclui o endereço do ponto final e informações de segurança, mas os formatos de cadeia de ligação variam entre os serviços. Existem dois tipos de cadeia de ligação associadas ao serviço IoT Hub:

- As cordas de ligação do dispositivo permitem que [os dispositivos IoT Plug e Play](#iot-plug-and-play-device) se conectem aos pontos finais virados para o dispositivo num hub IoT. O código do cliente num dispositivo utiliza a cadeia de ligação para estabelecer uma ligação segura com um hub IoT.
- As cordas de ligação IoT Hub permitem que soluções e ferramentas de back-end se conectem de forma segura aos pontos finais voltados para o serviço num hub IoT. Estas soluções e ferramentas gerem o hub IoT e os dispositivos que lhe estão ligados.

## <a name="default-component"></a>Componente predefinido

Todos os [modelos do dispositivo](#device-model) têm um componente predefinido. Um modelo de dispositivo simples tem apenas um componente padrão - tal modelo também é conhecido como um dispositivo sem componente. Um modelo mais complexo tem vários componentes aninhados por baixo do componente padrão.

## <a name="device-certification"></a>Certificação de dispositivos

O programa de certificação ioT Plug and Play verifica que um dispositivo cumpre os requisitos de certificação IoT Plug and Play. Pode adicionar um dispositivo certificado ao catálogo de [dispositivos Azure IoT certificados.](https://aka.ms/devicecatalog)

## <a name="device-model"></a>Modelo do dispositivo

Um modelo de dispositivo descreve um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) e define os [componentes](#component) que compõem o dispositivo. Um modelo de dispositivo simples não tem componentes separados e contém uma definição para uma única interface. A ferramenta exploradora Azure IoT mostra um modelo simples como tendo um único [componente predefinido](#default-component).

Um modelo de dispositivo mais complexo inclui vários componentes. Um modelo de dispositivo normalmente corresponde a um dispositivo físico, produto ou SKU. Utiliza a versão 2 da [Definição de Gémeos Digitais](#digital-twins-definition-language) para definir um modelo de dispositivo.

## <a name="device-builder"></a>Construtor de dispositivos

Um construtor de [dispositivos](#interface) utiliza um modelo e interfaces de [dispositivo](#device-model) ao implementar código para ser executado num [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Os construtores de dispositivos normalmente usam um dos [SDKs do dispositivo Azure IoT](#azure-iot-device-sdk) para implementar o cliente do dispositivo, mas isso não é necessário.

## <a name="device-modeling"></a>Modelação do dispositivo

Um [construtor de dispositivos](#device-builder) ou [construtor de módulos](#module-builder)utiliza a [Linguagem de Definição de Gémeos Digitais](#digital-twins-definition-language) para modelar as capacidades de um dispositivo [IoT Plug and Play](#iot-plug-and-play-device). Um [construtor de soluções](#solution-builder) pode configurar uma solução IoT do modelo.

## <a name="digital-twin"></a>Gémeo digital

Um gémeo digital é um modelo de um [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Um gémeo digital é modelado utilizando a [Linguagem de Definição de Gémeos Digitais.](#digital-twins-definition-language) Você pode usar o [dispositivo Azure IoT SDKs](#azure-iot-device-sdk) para interagir com gémeos digitais no tempo de execução. Por exemplo, pode definir um valor de propriedade num twin digital num dispositivo e o SDK comunica esta alteração à sua solução IoT na nuvem.

## <a name="digital-twin-change-events"></a>Eventos de alteração de duplos digitais

Quando um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) está ligado a um hub [IoT,](#azure-iot-hub)o hub pode utilizar a sua capacidade de encaminhamento para enviar notificações de alterações digitais de gémeos. Por exemplo, sempre que um valor [de propriedade](#properties) muda num dispositivo, o IoT Hub pode enviar uma notificação para um ponto final, como um hub de Evento.

## <a name="digital-twins-definition-language"></a>Linguagem de definição de gémeos digitais

Um idioma para descrever modelos e interfaces para [dispositivos IoT Plug e Play](#iot-plug-and-play-device). Utilize a versão 2 da [Definição de Gémeos Digitais](https://github.com/Azure/opendigitaltwins-dtdl) para descrever as capacidades de um gémeo [digital](#digital-twin) e permitir que a plataforma IoT e as soluções IoT alavancam a semântica da entidade.

## <a name="digital-twin-route"></a>Rota digital twin

Uma rota criada num [hub IoT](#azure-iot-hub) para entregar [eventos digitais de mudança de gémeos](#digital-twin-change-events) para e ponto final como um hub de Evento.

## <a name="interface"></a>Interface

Uma interface descreve as capacidades relacionadas que são implementadas por um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) ou por um gémeo [digital.](#digital-twin) Pode reutilizar interfaces em diferentes [modelos de dispositivos.](#device-model) Quando uma interface é utilizada num modelo de dispositivo, define um [componente](#component) do dispositivo. Um simples dispositivo contém apenas uma interface padrão.

## <a name="iot-hub-query-language"></a>IoT Hub linguagem de consulta

A linguagem de consulta IoT Hub é usada para vários fins. Por exemplo, pode utilizar o idioma para procurar dispositivos registados no seu hub IoT ou refinar o comportamento [de encaminhamento digital de dois.](#digital-twin-route)

## <a name="iot-plug-and-play-bridge"></a>Bridge do IoT Plug and Play

IoT Plug and Play bridge é uma aplicação de código aberto que permite que sensores e periféricos existentes ligados aos gateways Windows ou Linux se conectem como [dispositivos IoT Plug e Play](#iot-plug-and-play-device).

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play dispositivo

Um dispositivo IoT Plug and Play é tipicamente um dispositivo de computação autónomo e de pequena escala que recolhe dados ou controla outros dispositivos, e que executa software ou firmware que implementa um [modelo de dispositivo.](#device-model)  Por exemplo, um dispositivo IoT Plug and Play pode ser um dispositivo de monitorização ambiental, ou um controlador para um sistema de irrigação de agricultura inteligente. Um dispositivo IoT Plug and Play pode ser implementado diretamente ou como um módulo IoT Edge. Pode escrever uma solução IoT hospedada em nuvem para comandar, controlar e receber dados de dispositivos IoT Plug e Play.

## <a name="iot-plug-and-play-conventions"></a>Convenções do IoT Plug and Play

Espera-se que [os dispositivos](#iot-plug-and-play-device) IoT Plug e Play sigam um conjunto de convenções quando trocam [dados](concepts-convention.md) com uma solução.

## <a name="model-id"></a>ID do Modelo

Quando um dispositivo IoT Plug and Play se conecta a um Hub IoT, envia o **ID modelo** do modelo [DTDL](#digital-twins-definition-language) que implementa. Isto permite encontrar a solução para encontrar o modelo do dispositivo.

## <a name="model-repository"></a>Repositório de modelos

Um [repositório modelo](concepts-model-repository.md) armazena modelos e interfaces de [dispositivos.](#interface) [device models](#device-model)

## <a name="model-repository-rest-api"></a>Modelo repositório REST API

Uma API para gerir e interagir com o repositório de modelos. Por exemplo, pode utilizar a API para adicionar e procurar [modelos de dispositivos.](#device-model)

## <a name="module-builder"></a>Construtor de módulos

Um construtor de [módulos](#interface) utiliza um modelo de [dispositivo](#device-model) e interfaces ao implementar código para ser executado num [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Os construtores de módulos implementam o código como módulo ou módulo IoT Edge para implementar no tempo de funcionaamento do IoT Edge num dispositivo.

## <a name="properties"></a>Propriedades

As propriedades são campos de dados definidos numa [interface](#interface) que representam algum estado de um gémeo digital. Pode declarar propriedades apenas para leitura ou legitável. As propriedades apenas de leitura, como o número de série, são definidas por código em execução no [dispositivo IoT Plug e Play.](#iot-plug-and-play-device)  As propriedades writable, como um limiar de alarme, são tipicamente definidas a partir da solução IoT baseada na nuvem.

## <a name="shared-access-signature"></a>Assinatura de acesso partilhado

As assinaturas de acesso partilhado são um mecanismo de autenticação baseado em hashes ou URIs seguros SHA-256. A autenticação de assinatura de acesso partilhado tem dois componentes: uma política de acesso partilhado e uma assinatura de acesso partilhado (muitas vezes chamada de símbolo). Um [dispositivo IoT Plug and Play](#iot-plug-and-play-device) utiliza uma assinatura de acesso partilhado para autenticar com um hub [IoT](#azure-iot-hub).

## <a name="solution-builder"></a>Construtor de soluções

Um construtor de soluções cria a solução traseira. Um construtor de soluções normalmente trabalha com recursos Azure, como [IoT Hub](#azure-iot-hub) e [repositórios de modelos.](#model-repository)

## <a name="telemetry"></a>Telemetria

Os campos de telemetria definidos numa [interface](#interface) representam medições. Estas medições são tipicamente valores como leituras de sensores que são enviadas pelo [dispositivo IoT Plug e Play](#iot-plug-and-play-device) como um fluxo de dados.
