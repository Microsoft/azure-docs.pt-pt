---
title: Limites e quotas IoT Plug e Play Preview / Microsoft Docs
description: Compreenda os limites, quotas e estrangulamentos que se aplicam quando utiliza o IoT Plug e play Preview.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337403"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites de pré-visualização ioT plug e play

Este artigo explica os limites, quotas e quotas específicos do IoT Plug e Play que se aplicam na pré-visualização pública. Existem [quotas e estrangulamentos existentes do IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>IoT Hub

Para a pré-visualização pública, aplicam-se os seguintes limites e quotas a um hub IoT:

| Limites, restrições e aceleradores | Valor | Notas |
|-----|-----|-----|
| Número de interfaces que podem ser registadas por hub | 1500 ||
| Tamanho máximo de um nome de componente | 1-64 chars | Personagens permitidos: a-z, A-Z, 0-9 (não como o primeiro personagem) e sublinham (não como o primeiro ou último personagem). |
| Tamanho máximo de um nome de propriedade | 1-64 chars | Personagens permitidos: a-z, A-Z, 0-9 (não como o primeiro personagem) e sublinham (não como o primeiro ou último personagem). |
| Tamanho máximo de um valor de propriedade | O mesmo que a [propriedade](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) de linguagem de definição de gémeos digitais | 5 níveis de profundidade e pode não ser uma matriz ou qualquer esquema complexo que contenha uma matriz |
| Tamanho máximo de um nome de comando | 1-64 chars | Personagens permitidos: a-z, A-Z, 0-9 (não como o primeiro personagem) e sublinham (não como o primeiro ou último personagem).|
| Tamanho duplo do dispositivo | O mesmo [que os limites do hub IoT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Biblioteca Parser

A biblioteca parser segue os limites que se aplicam à [Linguagem de Definição de Gémeos Digitais.](https://github.com/Azure/opendigitaltwins-dtdl)

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é rever a [arquitetura IoT Plug and Play](concepts-architecture.md).
