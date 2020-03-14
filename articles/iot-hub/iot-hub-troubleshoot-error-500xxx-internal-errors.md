---
title: Resolução de problemas Azure IoT Hub 500xxx Erros internos
description: Entenda como corrigir 500xxx erros internos
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271047"
---
# <a name="500xxx-internal-errors"></a>Erros Internos 500xxx

Este artigo descreve as causas e soluções para **500xxx erros internos.**

## <a name="symptoms"></a>Sintomas

O seu pedido ao IoT Hub falha com um erro que começa com 500 e/ou algum tipo de "erro do servidor". Algumas possibilidades são:

* **500001 ServerError**: IoT Hub encontrou um problema do lado do servidor.

* **500008 GenericTimeout**: IoT Hub não conseguiu completar o pedido de ligação antes de sair.

* **ServiçoIndisponível (sem código**de erro) : IoT Hub encontrou um erro interno.

* **InternalServerError (sem código**de erro) : IoT Hub encontrou um erro interno.

## <a name="cause"></a>Causa

Pode haver uma série de causas para uma resposta de erro de 500xxx. Em todos os casos, a questão é provavelmente transitória. Enquanto a equipa do IoT Hub trabalha arduamente para manter [o SLA,](https://azure.microsoft.com/support/legal/sla/iot-hub/)pequenos subconjuntos de nós do IoT Hub podem ocasionalmente experimentar falhas transitórias. Quando o seu dispositivo tenta ligar-se a um nó que está com problemas, recebe este erro.

## <a name="solution"></a>Solução

Para mitigar 500xxx erros, emita uma nova tentativa do dispositivo. Para [gerir automaticamente as repetições,](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)certifique-se de que utiliza a versão mais recente dos [SDKs Azure IoT](./iot-hub-devguide-sdks.md). Para obter as melhores práticas de manuseamento e retentativas transitórias de avarias, consulte o [manuseamento](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)transitório de falhas .  Se o problema persistir, verifique a [Saúde](./iot-hub-monitor-resource-health.md#use-azure-resource-health) dos Recursos e o [Estado Do Azure](https://status.azure.com/) para ver se o IoT Hub tem um problema conhecido. Também pode utilizar a [função de failover manual](./tutorial-manual-failover.md). Se não houver problemas conhecidos e a questão continuar, [o apoio de contacto](https://azure.microsoft.com/support/options/) para uma investigação mais aprofundada.
