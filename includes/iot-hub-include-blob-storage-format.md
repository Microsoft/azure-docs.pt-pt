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
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184882"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Os dados podem ser escritos para o blob storage em ambos os [Apache Avro](https://avro.apache.org/) formato, que é o predefinido, ou JSON (pré-visualização). 
>    
> A capacidade de codificar o formato JSON está em pré-visualização em todas as regiões em que o IoT Hub está disponível, exceto E.U.A. leste, E.U.A. oeste e Europa Ocidental. O formato de codificação só pode ser definido no momento que o ponto final de armazenamento de Blobs está configurado. Não é possível alterar o formato para um ponto final que já tenha sido configurado. Ao usar a codificação de JSON, tem de definir o contentType para JSON e contentEncoding para UTF-8, nas propriedades do sistema de mensagem. 
>
> Para obter mais informações sobre como utilizar um ponto de extremidade de armazenamento de BLOBs, veja [documentação de orientação sobre o encaminhamento para o armazenamento de BLOBs](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>