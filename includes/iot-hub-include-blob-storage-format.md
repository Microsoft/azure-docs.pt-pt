---
title: ficheiro de inclusão
description: ficheiro de inclusão
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "73612145"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser escritos para o armazenamento de bolhas no formato [Apache Avro,](https://avro.apache.org/) que é o padrão, ou JSON (pré-visualização). 
>    
> A capacidade de codificar o formato JSON está em pré-visualização em todas as regiões em que o IoT Hub está disponível, exceto os EUA, Eua Ocidental e Europa Ocidental. O formato de codificação só pode ser definido no momento em que o ponto final de armazenamento do blob estiver configurado. O formato não pode ser alterado para um ponto final que já foi criado. Ao utilizar a codificação JSON, deve definir o conteúdoType para JSON e o conteúdoEncoding para UTF-8 nas propriedades do sistema de mensagem. 
>
> Para obter informações mais detalhadas sobre a utilização de um ponto final de armazenamento de bolhas, consulte [orientações sobre o encaminhamento para armazenamento](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>