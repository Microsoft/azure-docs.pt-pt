---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89042990"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser escritos para o armazenamento de bolhas no formato [Apache Avro,](https://avro.apache.org/) que é o padrão, ou JSON (pré-visualização). 
>    
> A capacidade de codificar o formato JSON está em pré-visualização em todas as regiões em que o IoT Hub está disponível, exceto os EUA, Eua Ocidental e Europa Ocidental. O formato de codificação só pode ser definido no momento em que o ponto final de armazenamento do blob estiver configurado. O formato não pode ser alterado para um ponto final que já foi criado. Ao utilizar a codificação JSON, deve definir o conteúdoType para JSON e o conteúdoEncoding para UTF-8 nas propriedades do sistema de mensagem. 
>
> Para obter informações mais detalhadas sobre a utilização de um ponto final de armazenamento de bolhas, consulte [orientações sobre o encaminhamento para armazenamento](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>