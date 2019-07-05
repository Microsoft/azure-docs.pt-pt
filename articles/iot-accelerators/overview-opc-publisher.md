---
title: O que é o publicador OPC - Azure | Documentos da Microsoft
description: Descrição geral de publicador OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99b3f6b4495ae5981322c90fce251b9b0a18bb5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450913"
---
# <a name="what-is-opc-publisher"></a>O que é o publicador OPC?

O publicador OPC é uma implementação de referência que demonstra como:

- Ligar a servidores de OPC UA existentes.
- Publicar dados de telemetria JSON codificado dos servidores OPC UA no formato de OPC UA Pub/Sub, com um payload JSON, hub IoT do Azure.

Pode utilizar qualquer um dos protocolos de transporte que o SDK de cliente do IoT Hub do Azure suporta: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um OPC UA *cliente* para conexão com servidores OPC UA existentes que tem na sua rede.
- Um OPC UA *servidor* na porta 62222 que pode utilizar para gerir o que é publicado e oferece o IoT Hub direcionar os métodos para fazer o mesmo.

Pode baixar o [implementação de referência de publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) do GitHub.

A aplicação é implementada usando a tecnologia do .NET Core e pode ser executado em qualquer plataforma suportada pelo .NET Core.

O publicador OPC implementa a lógica de repetição para estabelecer ligações a pontos de extremidade que não responderem a um determinado número de pedidos de keep alive. Por exemplo, se um servidor OPC UA para de responder devido a uma queda de energia.

Para cada intervalo de publicação distinto para um servidor OPC UA, o aplicativo cria uma subscrição separada, através do qual todos os nós com este intervalo de publicação são atualizados.

O publicador OPC suporta a criação de batches dos dados enviados para o IoT Hub para reduzir a carga de rede. Este processamento em lote envia um pacote para o IoT Hub apenas se o tamanho do pacote configurado for atingido.

Esta aplicação utiliza a pilha de referência do OPC Foundation OPC UA como pacotes de NuGet. Ver [ https://opcfoundation.org/license/redistributables/1.3/ ](https://opcfoundation.org/license/redistributables/1.3/) para os termos de licenciamento.

### <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu o que é o publicador OPC, o passo seguinte sugerido é saber como [configurar o publicador OPC](howto-opc-publisher-configure.md).
