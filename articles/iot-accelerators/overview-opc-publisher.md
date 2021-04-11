---
title: O que é APC Publisher - Azure | Microsoft Docs
description: Este artigo fornece uma visão geral das características da OPC Publisher. Permite-lhe publicar dados de telemetria JSON codificados utilizando uma carga útil JSON, para o Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105647014"
---
# <a name="what-is-opc-publisher"></a>O que é a Editora OPC?

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O OPC Publisher é uma implementação de referência que demonstra como:

- Ligue-se aos servidores UA OPC existentes.
- Publique dados de telemetria codificados JSON a partir de servidores OPC UA em OPC UA Pub/Sub formato, utilizando uma carga útil JSON, para Azure IoT Hub.

Pode utilizar qualquer um dos protocolos de transporte que o cliente SDK do Azure IoT Hub suporta: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um *cliente* UA OPC para ligar aos servidores OPC UA existentes que tem na sua rede.
- Um *servidor* OPC UA na porta 6222 que pode usar para gerir o que é publicado e oferece métodos diretos IoT Hub para fazer o mesmo.

Você pode baixar a implementação de referência da [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) a partir do GitHub.

A aplicação é implementada utilizando a tecnologia .NET Core e pode funcionar em qualquer plataforma suportada por .NET Core.

A OPC Publisher implementa a lógica de reação para estabelecer ligações a pontos finais que não respondam a um certo número de pedidos de manter vivo. Por exemplo, se um servidor UA OPC deixar de responder devido a uma falha de energia.

Para cada intervalo de publicação distinto para um servidor OPC UA, a aplicação cria uma subscrição separada sobre a qual todos os nós com este intervalo de publicação são atualizados.

A OPC Publisher suporta o lote dos dados enviados para o IoT Hub para reduzir a carga da rede. Este lote envia um pacote para IoT Hub apenas se o tamanho do pacote configurado for atingido.

Esta aplicação utiliza a stack de referência OPC Foundation OPC UA como pacotes NuGet. Consulte [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) os termos de licenciamento.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o que é a Editora OPC, o próximo passo sugerido é aprender a:

[Configurar o Publicador OPC](howto-opc-publisher-configure.md)