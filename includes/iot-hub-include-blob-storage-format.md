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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73612145"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser escritos para armazenamento blob no formato [Apache Avro,](https://avro.apache.org/) que é o padrão, ou JSON (pré-visualização). 
>    
> A capacidade de codificar o formato JSON está em pré-visualização em todas as regiões em que o IoT Hub está disponível, exceto os EUA Orientais, Os EUA Ocidentais e a Europa Ocidental. O formato de codificação só pode ser definido no momento em que o ponto final de armazenamento blob esteja configurado. O formato não pode ser alterado para um ponto final que já foi criado. Ao utilizar a codificação JSON, deve definir o conteúdoType para JSON e o conteúdoEncoding para UTF-8 nas propriedades do sistema de mensagens. 
>
> Para obter informações mais detalhadas sobre a utilização de um ponto final de armazenamento de bolhas, consulte [a orientação sobre o encaminhamento para o armazenamento](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>