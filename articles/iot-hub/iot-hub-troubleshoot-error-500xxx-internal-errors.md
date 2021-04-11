---
title: Resolução de problemas Azure IoT Hub 500xx Erros internos
description: Entenda como corrigir erros internos de 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1bf3c405f988edc2a75a2b54f664f7cbada7b158
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060997"
---
# <a name="500xxx-internal-errors"></a>Erros Internos 500xxx

Este artigo descreve as causas e soluções para **500xxx Erros internos**.

## <a name="symptoms"></a>Sintomas

O seu pedido para o IoT Hub falha com um erro que começa com 500 e/ou algum tipo de "erro do servidor". Algumas possibilidades são:

* **500001 ServerError**: IoT Hub deparou-se com um problema do lado do servidor.

* **500008 GenericTimeout**: IoT Hub não conseguiu completar o pedido de ligação antes de terminar o tempo.

* **ServiçoUn disponível (sem código de erro)**: O IoT Hub encontrou um erro interno.

* **InternalServerError (sem código de erro)**: O IoT Hub encontrou um erro interno.

## <a name="cause"></a>Causa

Pode haver uma série de causas para uma resposta de erro de 500xxx. Em todos os casos, a questão é provavelmente transitória. Enquanto a equipa do IoT Hub trabalha arduamente para manter [o SLA,](https://azure.microsoft.com/support/legal/sla/iot-hub/)pequenos subconjuntos de nós IoT Hub podem ocasionalmente experimentar falhas transitórias. Quando o seu dispositivo tenta ligar-se a um nó que está a ter problemas, recebe este erro.

## <a name="solution"></a>Solução

Para atenuar os erros de 500xxx, emita uma nova mente a partir do dispositivo. Para [gerir automaticamente as retrações,](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)certifique-se de que utiliza a versão mais recente dos [SDKs Azure IoT](./iot-hub-devguide-sdks.md). Para melhores práticas no manuseamento transitório de falhas e retróções, consulte [o manuseamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).  Se o problema persistir, verifique a [Saúde dos Recursos](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) e [o Estado do Azure](https://status.azure.com/) para ver se o IoT Hub tem um problema conhecido. Também pode utilizar a [função de failover manual](./tutorial-manual-failover.md). Se não houver problemas conhecidos e a questão continuar, [contacte o apoio](https://azure.microsoft.com/support/options/) para uma investigação mais aprofundada.
