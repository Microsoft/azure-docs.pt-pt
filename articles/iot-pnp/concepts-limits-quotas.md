---
title: Limites e quotas IoT Plug e Play Preview / Microsoft Docs
description: Compreenda os limites, quotas e estrangulamentos que se aplicam quando utiliza o IoT Plug e play Preview.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80518175"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites de pré-visualização ioT plug e play

Este artigo explica os limites, quotas e quotas específicos do IoT Plug e Play que se aplicam na pré-visualização pública. Existem [quotas e estrangulamentos existentes do IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>Hub IoT

Para a pré-visualização pública, aplicam-se os seguintes limites e quotas a um hub IoT:

| Limites, restrições e aceleradores | Valor | Notas |
|-----|-----|-----|
| Número de modelos de capacidade do dispositivo (DCMs) ou interfaces que podem ser registados por hub | 1500 ||
| Número máximo de interfaces que podem ser registadas por dispositivo | 40 ||
| Número máximo de DCMs que podem ser registados por dispositivo | 1 ||
| Tamanho máximo do ficheiro interface/DCM | 512 KB ||
| Tamanho máximo de um nome de interface | 256 chars ||
| Tamanho máximo de um nome de propriedade  | 64 bytes, 7 níveis de profundidade (e o primeiro nível está reservado `$iotin` para) | Personagens permitidos: a-z, A-Z, 0-9 (não como o primeiro personagem) e sublinham. |
| Tamanho máximo de um valor de propriedade | 512 bytes ||
| Tamanho máximo de um nome de comando | 100 bytes ||
| Tamanho duplo do dispositivo | O mesmo [que os limites do hub IoT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| A API de resolução chama através do SKU (independentemente das unidades) | 100 pedidos/segundo ||

## <a name="model-repository"></a>Repositório de Modelos

Para a pré-visualização pública, aplicam-se os seguintes limites e quotas a um repositório modelo:

| Limites, restrições e aceleradores | Valor |
|-----|-----|
| Número de repositórios de modelo de empresa por inquilino do Azure Ative Directory | 1 |
| Número de chaves de autorização por repositório de modelo | 10  |
| Número de modelos (DCMs ou interfaces) por repositório de modelos de empresa| 1500  |
| Número de modelos (DCMs ou interfaces) no repositório de modelos públicos por inquilino do Azure Ative Directory| 1500  |
| Número de DCMs ou interfaces a serem eliminados num repositório de modelos da empresa | 10 consultas por segundo (QPS)|
| Número de repositórios de modelos criados/atualizados por um inquilino| 1 QPS |
| Número de chaves de autorização criadas/atualizadas/eliminadas num repositório de modelos | 1 QPS|
| Número de DCMs a ser criado num repositório de modelos de empresa | 10 QPS |
| Número de interfaces a ser criado num repositório de modelos da empresa | 10 QPS|
| Número de DCMs criados no repositório de modelos públicos | 10 QPS|
| Número de interfaces a ser criadas no repositório de modelos públicos | 10 QPS|

## <a name="parser-library"></a>Biblioteca Parser

A biblioteca parser segue os limites que se aplicam à [Linguagem de Definição Digital Twin.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)

## <a name="next-steps"></a>Passos seguintes

Um próximo passo sugerido é aprender a [conectar-se e interagir com um dispositivo IoT Plug and Play](./howto-develop-solution.md).
