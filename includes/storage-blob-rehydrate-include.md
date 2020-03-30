---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780250"
---
Para ler os dados no armazenamento de arquivo, tem de, primeiro, alterar a camada do blob para frequente ou esporádica. Este processo é conhecido como reidratação e pode levar horas para ser concluído. Recomendamos grandes tamanhos de bolha para um ótimo desempenho de reidratação. “Reidratar” vários pequenos blobs em simultâneo pode provocar mais atrasos. Existem atualmente duas prioridades de reidratação, High (pré-visualização) e Standard, que podem ser definidas através da propriedade opcional *x-ms-rehydrate-priority* em uma operação [set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) ou [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Prioridade padrão**: O pedido de reidratação será processado no despacho que foi recebido e pode demorar até 15 horas.
* **Alta prioridade (pré-visualização)**: O pedido de reidratação será priorizado sobre os pedidos standard e poderá terminar em menos de 1 hora. A alta prioridade pode demorar mais de 1 hora, dependendo do tamanho da bolha e da procura atual. Os pedidos de alta prioridade são garantidos para serem priorizados em relação aos pedidos prioritários da Standard.

> [!NOTE]
> A prioridade padrão é a opção de reidratação padrão para o arquivo. Alta prioridade é uma opção mais rápida que custará mais do que a reidratação prioritária Standard e é geralmente reservada para uso em situações de restauração de dados de emergência.

Uma vez iniciado um pedido de reidratação, não pode ser cancelado. Durante a reidratação, pode verificar a propriedade blob *Archive Status* para confirmar se o nível mudou. Consoante a camada de destino, o estado mostra “rehydrate-pending-to-hot” (“reidratação para frequenet pendente”) ou “rehydrate-pending-to-cool” (“reidratação para esporádica pendente). Após a conclusão, a propriedade do estado de arquivo é removida, e a propriedade de blob *Access Tier* reflete o novo nível quente ou fresco.
