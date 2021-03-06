---
title: Matriz de suporte para backup Azure Blobs
description: Fornece um resumo das definições e limitações de suporte ao fazer o backup de Azure Blobs (em pré-visualização)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561904"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Matriz de suporte para backup Azure Blobs (na pré-visualização)

Este artigo resume a disponibilidade regional, cenários apoiados e limitações de backup operacional de bolhas.

## <a name="supported-regions"></a>Regiões suportadas

Atualmente, o apoio operacional para blobs está disponível nas seguintes regiões: Austrália Central, Austrália Oriental, Brasil Sul, Canadá Central, Índia Central, Eua Central, Leste dos EUA 2, Alemanha Central, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul, Europa do Norte, Centro Sul dos EUA, Sudeste Asiático, Suíça Norte, EAU Norte, Reino Unido, Reino Unido, Oeste dos EUA, West Central , West US, West US 2

## <a name="limitations"></a>Limitações

A cópia de segurança operacional das bolhas utiliza a restauração do ponto de mancha no tempo, a versão blob, a eliminação suave para bolhas, a alteração do feed para bolhas e a eliminação do bloqueio para fornecer uma solução de backup local. Assim, as limitações que se aplicam a estas capacidades também se aplicam ao backup operacional.

**Cenários apoiados:** A cópia de segurança operacional suporta blobs de bloco apenas em contas de armazenamento v2 de uso geral padrão. As contas da ADLS Gen2 não são suportadas. Além disso, quaisquer bolhas de página, bolhas de apêndice e bolhas premium na sua conta de armazenamento não serão restauradas e apenas bolhas de blocos serão restauradas.

**Outras limitações:**

- Se tiver apagado um recipiente durante o período de retenção, esse recipiente não será restaurado com a operação de restauro pontual. Se tentar restaurar uma gama de bolhas que incluam bolhas num recipiente apagado, a operação de restauro pontual falhará. Para obter mais informações sobre a proteção dos recipientes contra a eliminação, consulte [a eliminação suave para recipientes (pré-visualização)](../storage/blobs/soft-delete-container-overview.md).
- Se uma bolha se moveu entre os níveis quentes e frescos no período entre o momento presente e o ponto de restauro, a bolha é restaurada para o seu nível anterior. Restaurar bolhas de blocos no nível de arquivo não é suportado. Por exemplo, se uma bolha no nível quente foi movida para o nível de arquivo há dois dias, e uma operação de restauro restaura a um ponto há três dias, a bolha não é restaurada para o nível quente. Para restaurar uma bolha arquivada, desloque-a primeiro para fora do nível de arquivo. Para obter mais informações, consulte [os dados do blob rehidrata do nível de arquivo.](../storage/blobs/storage-blob-rehydration.md)
- Um bloco que foi carregado através de [Put Block](/rest/api/storageservices/put-block) ou Put Block [de URL](/rest/api/storageservices/put-block-from-url), mas não cometido através da Put [Block List](/rest/api/storageservices/put-block-list), não faz parte de uma bolha e por isso não é restaurado como parte de uma operação de restauro.
- Uma bolha com um arrendamento ativo não pode ser restaurada. Se uma bolha com um arrendamento ativo for incluída na gama de bolhas para restaurar, a operação de restauro falhará automaticamente. Quebre quaisquer locações ativas antes de iniciar a operação de restauro.
- As imagens não são criadas ou eliminadas como parte de uma operação de restauro. Apenas a bolha da base é restaurada ao seu estado anterior.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do backup operacional para Azure Blobs (em pré-visualização)](blob-backup-overview.md)