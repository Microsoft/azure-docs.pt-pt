---
title: IoT Plug and Play architecture Microsoft Docs
description: Como construtor de soluções, compreenda os principais elementos arquitetónicos do IoT Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f2d9407d9c7172839027e2650486695244f67889
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352345"
---
# <a name="iot-plug-and-play-preview-architecture"></a>IoT Plug e Play Preview arquitetura

O IoT Plug e o Play Preview permitem aos construtores de soluções integrar dispositivos inteligentes com as suas soluções sem qualquer configuração manual. No núcleo do IoT Plug and Play, está um _modelo_ de dispositivo que descreve as capacidades de um dispositivo para uma aplicação IoT Plug e Play. Este modelo é estruturado como um conjunto de interfaces que definem:

- _Propriedades_ que representam o estado apenas de leitura ou de um dispositivo ou de outra entidade. Por exemplo, um número de série do dispositivo pode ser uma propriedade apenas de leitura e uma temperatura-alvo num termóstato pode ser uma propriedade writável.
- _Telemetria_ que são os dados emitidos por um dispositivo, se os dados são um fluxo regular de leituras de sensores, um erro ocasional ou uma mensagem de informação.
- _Comandos_ que descrevem uma função ou operação que pode ser feita num dispositivo. Por exemplo, um comando poderia reiniciar um portal ou tirar uma fotografia usando uma câmara remota.

Cada modelo e interface tem um ID único.

O seguinte diagrama mostra os elementos-chave de uma solução IoT Plug and Play:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug e Play arquitetura":::

## <a name="model-repository"></a>Repositório de modelos

O [repositório de modelos](./concepts-model-repository.md) é uma loja para definições de modelo e interface. Define modelos e interfaces utilizando a [Linguagem de Definição de Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

A UI web permite-lhe gerir os modelos e interfaces.

O repositório de modelos utiliza o RBAC para permitir limitar o acesso às definições de interface.

## <a name="devices"></a>Dispositivos

Um construtor de dispositivos implementa o código para executar num dispositivo inteligente IoT utilizando um dos [SDKs do dispositivo Azure IoT](./libraries-sdks.md). Os SDKs do dispositivo ajudam o construtor do dispositivo a:

- Ligue-se de forma segura a um hub IoT.
- Registe o dispositivo com o seu hub IoT e anuncie o ID do modelo que identifica a coleção de interfaces que o dispositivo implementa.
- Atualizar as propriedades definidas nas interfaces DTDL que o dispositivo implementa. Estas propriedades são implementadas usando gémeos digitais que gerem a sincronização com o seu hub IoT.
- Adicione os controladores para os comandos definidos nas interfaces DTDL que o dispositivo implementa.
- Envie telemetria para o centro de IoT.

## <a name="iot-hub"></a>IoT Hub

[O IoT Hub](../iot-hub/about-iot-hub.md) é um serviço hospedado na nuvem que funciona como um centro de mensagens central para a comunicação bidis entre a sua solução IoT e os dispositivos que gere.

Um hub IoT:

- Disponibiliza o ID do modelo implementado por um dispositivo para uma solução de backend.
- Mantém o gémeo digital associado a cada dispositivo Plug and Play ligado ao hub.
- Encaminha os fluxos de telemetria para outros serviços para processamento ou armazenamento.
- Rotas digitais twin eventos de mudança para outros serviços para permitir a monitorização do dispositivo.

## <a name="backend-solution"></a>Solução backend

Uma solução backend monitoriza e controla dispositivos conectados interagindo com gémeos digitais no hub IoT. Utilize um dos SDKs de serviço para implementar a sua solução de backend. Para compreender as capacidades de um dispositivo conectado, a solução recua:

1. Recupera o ID do modelo o dispositivo registado no hub IoT.
1. Utiliza o ID do modelo para recuperar as definições de interface de qualquer repositório de modelos.
1. Utiliza o analisador de modelos para extrair informações das definições de interface.

A solução backend pode utilizar a informação das definições de interface para:

- Leia os valores de propriedade reportados pelos dispositivos.
- Atualizar propriedades writáveis num dispositivo.
- Comandos de chamada implementados por um dispositivo.
- Compreenda o formato de telemetria enviado por um dispositivo.

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma visão geral da arquitetura de uma solução IoT Plug and Play, os próximos passos são para saber mais sobre:

- [O repositório modelo](./concepts-model-repository.md)
- [Processo de descoberta de modelos](./concepts-model-discovery.md)
- [Desenvolvimento para IoT Plug e Play](./concepts-developer-guide.md)
