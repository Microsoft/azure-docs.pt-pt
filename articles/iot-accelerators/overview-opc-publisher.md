---
title: O que é o editor OPC-Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos do editor OPC. Ele permite que você publique dados de telemetria JSON codificados usando uma carga JSON, para o Hub IoT do Azure.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824782"
---
# <a name="what-is-opc-publisher"></a>O que é o editor OPC?

O editor OPC é uma implementação de referência que demonstra como:

- Conecte-se a servidores OPC UA existentes.
- Publicar dados de telemetria codificados JSON de servidores OPC UA no formato OPC UA pub/sub, usando uma carga JSON, para o Hub IoT do Azure.

Você pode usar qualquer um dos protocolos de transporte para os quais o SDK do cliente do Hub IoT do Azure dá suporte: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um *cliente* OPC UA para se conectar a servidores OPC UA existentes que você tem em sua rede.
- Um *servidor* OPC UA na porta 62222 que você pode usar para gerenciar o que está publicado e oferece métodos diretos do Hub IOT para fazer o mesmo.

Você pode baixar a [implementação de referência do editor OPC](https://github.com/Azure/iot-edge-opc-publisher) do github.

O aplicativo é implementado usando a tecnologia .NET Core e pode ser executado em qualquer plataforma com suporte no .NET Core.

O editor OPC implementa a lógica de repetição para estabelecer conexões com pontos de extremidade que não respondem a um determinado número de solicitações Keep Alive. Por exemplo, se um servidor OPC UA parar de responder devido a uma interrupção de energia.

Para cada intervalo de publicação distinto para um servidor OPC UA, o aplicativo cria uma assinatura separada sobre a qual todos os nós com esse intervalo de publicação são atualizados.

O editor OPC dá suporte ao envio em lote dos dados enviados ao Hub IoT para reduzir a carga de rede. Esse envio em lote enviará um pacote ao Hub IoT somente se o tamanho do pacote configurado for atingido.

Este aplicativo usa a pilha de referência OPC do OPC Foundation para pacotes NuGet. Consulte [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) para os termos de licenciamento.

### <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu o que é o editor OPC, a próxima etapa sugerida é aprender a [Configurar o editor OPC](howto-opc-publisher-configure.md).
