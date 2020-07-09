---
title: Compare as opções de armazenamento para utilização com clusters Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como funcionam com a Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82610705"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Compare as opções de armazenamento para utilização com clusters Azure HDInsight

Pode escolher entre alguns serviços de armazenamento Azure diferentes ao criar clusters HDInsight:

* [Armazenamento do Azure](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md) (Armazenamento do Azure Data Lake Gen2)
* [Armazenamento do Azure Data Lake Ger1](./overview-data-lake-storage-gen1.md)

Este artigo fornece uma visão geral destes tipos de armazenamento e suas características únicas.

## <a name="storage-types-and-features"></a>Tipos e funcionalidades de armazenamento

A tabela a seguir resume os serviços de Armazenamento Azure que são suportados com diferentes versões de HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de espaço de nome | Serviços suportados | Níveis de desempenho suportados | Níveis de acesso suportados | Versão HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Armazenamento do Azure Data Lake Ger2| V2 de uso geral | Hierárquico (sistema de ficheiros) | Blobs | Standard | Quente, Fresco, Arquivo | 3.6+ | Todos exceto Spark 2.1 e 2.2|
|Storage do Azure| V2 de uso geral | Objeto | Blobs | Standard | Quente, Fresco, Arquivo | 3.6+ | Todos |
|Storage do Azure| V1 de uso geral | Objeto | Blobs | Standard | N/D | Todos | Todos |
|Storage do Azure| Blob Storage** | Objeto | Blob de Bloco | Standard | Quente, Fresco, Arquivo | Todos | Todos |
|Armazenamento do Azure Data Lake Ger1| N/D | Hierárquico (sistema de ficheiros) | N/D | N/D | N/D | 3.6 Apenas | Todos, exceto HBase |

**Para os clusters HDInsight, apenas as contas de armazenamento secundário podem ser do tipo BlobStorage e Page Blob não é uma opção de armazenamento suportado.

Para obter mais informações sobre os tipos de conta de armazenamento Azure, consulte [a visão geral da conta de armazenamento Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre os níveis de acesso ao armazenamento Azure Storage, consulte [o armazenamento Azure Blob: Premium (pré-visualização), Hot, Cool e Archive](../storage/blobs/storage-blob-storage-tiers.md)

Pode criar clusters utilizando combinações de serviços para armazenamento secundário primário e opcional. A tabela seguinte resume as configurações de armazenamento de cluster que são atualmente suportadas em HDInsight:

| Versão HDInsight | Armazenamento Primário | Armazenamento Secundário | Suportado |
|---|---|---|---|
| 3.6 & 4.0 | Final geral V1, Finalidade Geral V2 | Final geral V1, Finalidade Geral V2, BlobStorage (Blobs Block) | Yes |
| 3.6 & 4.0 | Final geral V1, Finalidade Geral V2 | Data Lake Storage Gen2 | No |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Yes |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Final geral V1, Finalidade Geral V2, BlobStorage (Blobs Block) | Yes |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Ger1 de Armazenamento do Data Lake | No |
| 3.6 | Ger1 de Armazenamento do Data Lake | Ger1 de Armazenamento do Data Lake | Yes |
| 3.6 | Ger1 de Armazenamento do Data Lake | Final geral V1, Finalidade Geral V2, BlobStorage (Blobs Block) | Yes |
| 3.6 | Ger1 de Armazenamento do Data Lake | Data Lake Storage Gen2 | No |
| 4,0 | Ger1 de Armazenamento do Data Lake | Qualquer | No |
| 4,0 | Final geral V1, Finalidade Geral V2 | Ger1 de Armazenamento do Data Lake | No |

*=Isto pode ser uma ou múltiplas contas de Data Lake Storage Gen2, desde que todas estejam configuradas para usar a mesma identidade gerida para acesso ao cluster.

> [!NOTE]
> O armazenamento primário da Gen2 de armazenamento de dados não é suportado para clusters Spark 2.1 ou 2.2.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Armazenamento do Azure](./overview-azure-storage.md)
* [Descrição geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Descrição geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
