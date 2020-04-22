---
title: O que é OPC Publisher - Azure Microsoft Docs
description: Este artigo fornece uma visão geral das características da OPC Publisher. Permite-lhe publicar dados codificados de telemetria JSON utilizando uma carga útil JSON, para o Hub Azure IoT.
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
ms.openlocfilehash: c77dff4a4f89a78dc0a0d723fbb45fe691246112
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681924"
---
# <a name="what-is-opc-publisher"></a>O que é editora oPC?

OPc Publisher é uma implementação de referência que demonstra como:

- Ligue-se aos servidores OPC UA existentes.
- Publique dados codificados de telemetria da JSON dos servidores OPC UA em formato OPC UA Pub/Sub, utilizando uma carga útil JSON, para o Hub Azure IoT.

Pode utilizar qualquer um dos protocolos de transporte que o cliente SDK do Azure IoT Hub suporta: HTTPS, AMQP e MQTT.

A implementação de referência inclui:

- Um *cliente* da OPC UA para ligar aos servidores UA opc existentes que tem na sua rede.
- Um *servidor* OPC UA na porta 62222 que pode usar para gerir o que é publicado e oferece métodos diretos IoT Hub para fazer o mesmo.

Pode baixar a implementação de referência da [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) a partir do GitHub.

A aplicação é implementada utilizando a tecnologia .NET Core e pode funcionar em qualquer plataforma suportada por .NET Core.

A OPC Publisher implementa uma lógica de nova tentativa para estabelecer ligações a pontos finais que não respondem a um certo número de pedidos de manutenção vivo. Por exemplo, se um servidor OPC UA parar de responder devido a uma falha de energia.

Para cada intervalo de publicação distinto para um servidor OPC UA, a aplicação cria uma subscrição separada sobre a qual todos os nós com este intervalo de publicação são atualizados.

A OPC Publisher suporta o lote dos dados enviados ao IoT Hub para reduzir a carga de rede. Este lote envia um pacote para o IoT Hub apenas se o tamanho do pacote configurado for atingido.

Esta aplicação utiliza a pilha de referência oPC Foundation OPC UA como pacotes NuGet. Consulte [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) os termos de licenciamento.

### <a name="next-steps"></a>Passos seguintes

Agora que já aprendeu o que é a OPC Publisher, o próximo passo sugerido é aprender a configurar a [Editora OPC.](howto-opc-publisher-configure.md)
