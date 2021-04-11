---
title: Microsoft OPC Publisher
description: Este artigo fornece uma visão geral do módulo OPC Publisher Edge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800538"
---
# <a name="what-is-the-opc-publisher"></a>O que é a Editora OPC?

O OPC Publisher é um produto Microsoft totalmente suportado, desenvolvido a céu aberto, que faz a ponte entre os ativos industriais e a nuvem Microsoft Azure. Fá-lo ligando-o aos ativos ativados pela OPC ou ao software de conectividade industrial e publica dados de telemetria ao Azure IoT Hub em vários formatos, incluindo o formato padrão IEC62541 OPC UA PubSub (a partir da versão 2.6).

Funciona no Azure IoT Edge como módulo ou em simples Docker como um recipiente. Uma vez que aproveita o tempo de funcionamento da plataforma cruzada .NET, também funciona de forma nativa no Linux e windows 10.

O OPC Publisher é uma implementação de referência que demonstra como:

- Ligue-se aos servidores UA OPC existentes.
- Publique dados de telemetria codificados JSON a partir de servidores OPC UA em OPC UA Pub/Sub formato, utilizando uma carga útil JSON, para Azure IoT Hub.

Pode utilizar qualquer um dos protocolos de transporte que o cliente SDK do Azure IoT Hub suporta: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um *cliente* UA OPC para ligar aos servidores OPC UA existentes que tem na sua rede.
- Um *servidor* OPC UA na porta 6222 que pode usar para gerir o que é publicado e oferece métodos diretos IoT Hub para fazer o mesmo.

Você pode baixar a implementação de referência da [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) a partir do GitHub.

A aplicação é implementada utilizando a tecnologia .NET Core e pode funcionar em qualquer plataforma suportada por .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>O que faz a Editora OPC?

A OPC Publisher implementa a lógica de reação para estabelecer ligações a pontos finais que não respondam a um certo número de pedidos de manter vivo. Por exemplo, se um servidor UA OPC deixar de responder devido a uma falha de energia.

Para cada intervalo de publicação distinto para um servidor OPC UA, a aplicação cria uma subscrição separada sobre a qual todos os nós com este intervalo de publicação são atualizados.

A OPC Publisher suporta o lote dos dados enviados para o IoT Hub para reduzir a carga da rede. Este lote envia um pacote para IoT Hub apenas se o tamanho do pacote configurado for atingido.

Esta aplicação utiliza a stack de referência OPC Foundation OPC UA como pacotes NuGet. Consulte [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) os termos de licenciamento.

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu o que é a Editora OPC, pode começar por implantá-la:

> [!div class="nextstepaction"]
> [Implementar editor OPC em modo autónomo](tutorial-publisher-deploy-opc-publisher-standalone.md)
