---
title: Camadas de desempenho de armazenamento de blob de blocos do Azure-armazenamento do Azure
description: Níveis de desempenho para o armazenamento de BLOBs do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063261"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Camadas de desempenho de armazenamento de blob de blocos do Azure

À medida que as empresas implantam aplicativos nativos de nuvem sensíveis ao desempenho, é importante ter opções de armazenamento de dados econômico em diferentes níveis de desempenho.

O armazenamento de blobs de blocos do Azure oferece dois níveis de desempenho diferentes:

- Premium: otimizado para altas taxas de transação e latência de armazenamento consistente de um dígito
- Standard: otimizado para alta capacidade e alta taxa de transferência

As seguintes considerações se aplicam a diferentes níveis de desempenho:

- O desempenho padrão está disponível em todas as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=storage). O desempenho Premium está disponível em [regiões selecionadas](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- O desempenho premium fornece preços otimizados para aplicativos com altas taxas de transação para ajudar a [reduzir o custo total de armazenamento](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) para essas cargas de trabalho.
- Para obter o desempenho premium para BLOBs de bloco, você deve usar o tipo de conta BlockBlobStorage.
- O desempenho padrão está disponível com as contas de armazenamento Uso Geral v1, Uso Geral V2 e BLOB.
- O desempenho premium e Standard dão suporte a [blobs de blocos de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Os blobs de blocos de alta taxa de transferência estão disponíveis para desempenho premium em mais de 256 KiB. Os blobs de blocos de alta taxa de transferência estão disponíveis para desempenho padrão em mais de 4 blocos Put de MiB ou colocam tamanhos de BLOB.
- O desempenho Premium está atualmente disponível somente com LRS (armazenamento com redundância local).

## <a name="premium-performance"></a>Desempenho premium

O armazenamento de blobs de blocos de desempenho premium disponibiliza os dados por meio de hardware de alto desempenho. Os dados são armazenados em SSDs (unidades de estado sólido) que são otimizados para baixa latência. O SSDs fornece maior taxa de transferência em comparação com os discos rígidos tradicionais.

O armazenamento de blobs de blocos de desempenho Premium é ideal para cargas de trabalho que exigem tempos de resposta rápidos e consistentes. É melhor para cargas de trabalho que executam muitas transações pequenas.

## <a name="standard-performance"></a>Desempenho padrão

O desempenho padrão dá suporte a diferentes [níveis de acesso](storage-blob-storage-tiers.md) para armazenar dados da maneira mais econômica. Ele é otimizado para alta capacidade e alta taxa de transferência em conjuntos de dados grandes.

## <a name="blob-lifecycle-management"></a>Gerenciamento do ciclo de vida do blob

O gerenciamento do ciclo de vida do armazenamento de BLOBs oferece uma política avançada baseada em regras:

- Premium-expirar dados no final de seu ciclo de vida
- Os dados de transição padrão para a melhor camada de acesso e expiram os dados no final de seu ciclo de vida

Para saber mais, consulte [gerenciar o ciclo de vida do armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md).

Os dados armazenados em uma conta de armazenamento de blob de blocos Premium não podem ser movidos entre as camadas quente, fria e arquivo morto. No entanto, você pode copiar blobs de uma conta de armazenamento de blobs de blocos para a camada de acesso quente em uma conta *diferente* . Use o [bloco Put da API de URL](/rest/api/storageservices/put-block-from-url) ou [AzCopy V10](../common/storage-use-azcopy-v10.md) para copiar dados para uma conta diferente. O **bloco Put da API de URL copia de** forma síncrona os dados no servidor. A chamada é concluída somente depois que todos os dados são movidos do local do servidor original para o local de destino.

## <a name="next-steps"></a>Passos seguintes

Avaliar a quente, a frio e o arquivo em contas de armazenamento de BLOBs e GPv2

- [Verificar a disponibilidade das camadas frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)
- [Gerir o ciclo de vida do Armazenamento de blobs do Azure](storage-lifecycle-management-concepts.md)
- [Saiba mais sobre os dados de blob reidratar da camada de arquivo](storage-blob-rehydration.md)
- [Avaliar a utilização das suas contas de armazenamento atuais ao ativar as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)
- [Verificar os preços das camadas de armazenamento frequente, esporádica e de arquivo nas contas de armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
- [Verificar os preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
