---
title: Limites e quotas IoT Plug e Play Preview / Microsoft Docs
description: Compreenda os limites, quotas e estrangulamentos que se aplicam quando utiliza o Plug IoT e a Pré-visualização.
author: miagdp
ms.author: miag
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cce99b7d9de09134fd01afb36c41bce3966e8536
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518175"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites de pré-visualização de plug e reprodução ioT

Este artigo explica os limites, quotas e estrangulamentos específicos do IoT Plug e play que se aplicam na pré-visualização pública. Existem quotas existentes do [Hub IoT e estrangulamentos](../iot-hub/iot-hub-devguide-quotas-throttling.md) que também se aplicam.

## <a name="iot-hub"></a>IoT Hub

Para a pré-visualização pública, os seguintes limites e quotas aplicam-se a um hub IoT:

| Limites, restrições e aceleradores | Valor | Notas |
|-----|-----|-----|
| Número de modelos de capacidade do dispositivo (DCMs) ou interfaces que podem ser registadas por hub | 1500 ||
| Número máximo de interfaces que podem ser registadas por dispositivo | 40 ||
| Número máximo de DCMs que podem ser registados por dispositivo | 1 ||
| Tamanho máximo da interface/ficheiro DCM | 512 KB ||
| Tamanho máximo de um nome de interface | 256 chars ||
| Tamanho máximo de um nome de propriedade  | 64 bytes, 7 níveis de profundidade (e `$iotin`o primeiro nível é reservado para) | Caracteres permitidos: a-z, A-Z, 0-9 (não como o primeiro personagem), e sublinham. |
| Tamanho máximo de um valor patrimonial | 512 bytes ||
| Tamanho máximo de um nome de comando | 100 bytes ||
| Tamanho gémeo do dispositivo | O mesmo que [os limites do Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Resolução API apela através do SKU (independentemente das unidades) | 100 pedidos/segundo ||

## <a name="model-repository"></a>Repositório modelo

Para a pré-visualização pública, aplicam-se os seguintes limites e quotas a um repositório modelo:

| Limites, restrições e aceleradores | Valor |
|-----|-----|
| Número de repositórios de modelos de empresa por inquilino do Diretório Ativo Azure | 1 |
| Número de chaves de autorização por repositório modelo | 10  |
| Número de modelos (DCMs ou interfaces) por repositório de modelos da empresa| 1500  |
| Número de modelos (DCMs ou interfaces) no repositório de modelos públicos por inquilino do Diretório Ativo Azure| 1500  |
| Número de DCMs ou interfaces sendo eliminados num repositório de modelo seletiva | 10 consultas por segundo (QPS)|
| Número de repositórios modelo criados/atualizados por um inquilino| 1 QPS |
| Número de chaves de autorização criadas/atualizadas/eliminadas num repositório modelo | 1 QPS|
| Número de DCMs criados num repositório modelo da empresa | 10 QPS |
| Número de interfaces criadas num repositório modelo da empresa | 10 QPS|
| Número de DCMs criados no repositório de modelos públicos | 10 QPS|
| Número de interfaces criadas no repositório de modelopúblico | 10 QPS|

## <a name="parser-library"></a>Biblioteca Parser

A biblioteca de parser segue os limites aplicáveis à Linguagem de [Definição Gémea Digital.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)

## <a name="next-steps"></a>Passos seguintes

Um próximo passo sugerido é aprender a [conectar-se e interagir com um dispositivo IoT Plug and Play](./howto-develop-solution.md).
