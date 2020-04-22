---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684097"
---
Para ler os dados no armazenamento de arquivo, tem de, primeiro, alterar a camada do blob para frequente ou esporádica. Este processo é conhecido como reidratação e pode levar horas para ser concluído. Recomendamos grandes tamanhos de bolha para um ótimo desempenho de reidratação. “Reidratar” vários pequenos blobs em simultâneo pode provocar mais atrasos. Existem atualmente duas prioridades de rehidratação, High and Standard, que podem ser definidas através da propriedade opcional *x-ms-rehydrate-priority* em uma operação [set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) ou [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Prioridade padrão**: O pedido de reidratação será processado no despacho que foi recebido e pode demorar até 15 horas.
* **Alta prioridade**: O pedido de reidratação será priorizado sobre os pedidos standard e poderá terminar em menos de 1 hora. A alta prioridade pode demorar mais de 1 hora, dependendo do tamanho da bolha e da procura atual. Os pedidos de alta prioridade são garantidos para serem priorizados em relação aos pedidos prioritários da Standard.

> [!NOTE]
> A prioridade padrão é a opção de reidratação padrão para o arquivo. Alta prioridade é uma opção mais rápida que custará mais do que a reidratação prioritária Standard e é geralmente reservada para uso em situações de restauração de dados de emergência.

Uma vez iniciado um pedido de reidratação, não pode ser cancelado. Durante o processo de reidratação, a propriedade blob *x-ms-acesso-nível* continuará a mostrar como arquivo até que a reidratação seja concluída a um nível on-line. Para confirmar o estado de reidratação e o progresso, pode ligar para a [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties) para verificar o *estado x-ms-arquivo* e as propriedades blob *x-ms-rehidratada-prioridade.* O estado do arquivo pode ler "rehidratar-pendente-a-quente" ou "rehidratar-pendente-a-cool" dependendo do nível de destino rehidratado. A prioridade de rehidratação indicará a velocidade de "Alto" ou "Standard". Após a conclusão, o estado de arquivo e as propriedades prioritárias de reidratação são removidas, e a propriedade blob de nível de acesso irá atualizar para refletir o nível quente ou fresco selecionado.
