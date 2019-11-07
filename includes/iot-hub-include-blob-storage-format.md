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
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612145"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser gravados no armazenamento de BLOBs no formato [Apache Avro](https://avro.apache.org/) , que é o padrão, ou JSON (visualização). 
>    
> A capacidade de codificar o formato JSON está em visualização em todas as regiões nas quais o Hub IoT está disponível, exceto leste dos EUA, oeste dos EUA e Europa Ocidental. O formato de codificação só pode ser definido no momento em que o ponto de extremidade do armazenamento de BLOBs é configurado. O formato não pode ser alterado para um ponto de extremidade que já foi configurado. Ao usar a codificação JSON, você deve definir o contentType como JSON e o contentEncoding como UTF-8 nas propriedades do sistema de mensagens. 
>
> Para obter informações mais detalhadas sobre como usar um ponto de extremidade de armazenamento de BLOBs, consulte [diretrizes sobre roteamento para armazenamento](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>