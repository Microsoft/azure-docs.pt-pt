---
title: Compare opções de armazenamento para uso com clusters Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como funcionam com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: bfbe311d9768923eee8c1b0cc4f3b4ec1a7ad69a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186310"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Compare opções de armazenamento para uso com clusters Azure HDInsight

Pode escolher entre alguns serviços de armazenamento Azure diferentes ao criar clusters HDInsight:

* [Armazenamento Azure](./overview-azure-storage.md)
* [Armazenamento do Azure Data Lake Ger2](./overview-data-lake-storage-gen1.md)
* [Armazenamento do Azure Data Lake Ger1](./overview-data-lake-storage-gen2.md)

Este artigo fornece uma visão geral destes tipos de armazenamento e suas características únicas.

## <a name="storage-types-and-features"></a>Tipos e funcionalidades de armazenamento

A tabela que se segue resume os serviços de Armazenamento Azure que são suportados com diferentes versões do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de espaço de nome | Serviços suportados | Níveis de desempenho suportados | Níveis de acesso suportados | Versão HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Armazenamento do Azure Data Lake Ger2| V2 de uso geral | Hierárquico (sistema de ficheiros) | Blobs | Standard | Hot, Cool, Archive | 3.6+ | Todos exceto o Spark 2.1 e 2.2|
|Storage do Azure| V2 de uso geral | Objeto | Blobs | Standard | Hot, Cool, Archive | 3.6+ | Todos |
|Storage do Azure| V1 de uso geral | Objeto | Blobs | Standard | N/D | Todos | Todos |
|Storage do Azure| Armazenamento Blob** | Objeto | Bloco Blob | Standard | Hot, Cool, Archive | Todos | Todos |
|Armazenamento do Azure Data Lake Ger1| N/D | Hierárquico (sistema de ficheiros) | N/D | N/D | N/D | 3.6 Apenas | Todos, exceto HBase |

**Para os clusters HDInsight, apenas as contas de armazenamento secundárias podem ser do tipo BlobStorage e page Blob não é uma opção de armazenamento suportada.

Para obter mais informações sobre os tipos de conta de armazenamento do Azure, consulte a visão geral da conta de [armazenamento do Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre os níveis de acesso ao Armazenamento Azure, consulte o [armazenamento Do Blob: Premium (pré-visualização), Hot, Cool e Archive](../storage/blobs/storage-blob-storage-tiers.md)

Pode criar clusters utilizando combinações de serviços para armazenamento secundário primário e opcional. A tabela que se segue resume as configurações de armazenamento de cluster que são atualmente suportadas no HDInsight:

| Versão HDInsight | Armazenamento Primário | Armazenamento Secundário | Suportado |
|---|---|---|---|
| 3,6 & 4.0 | Propósito Geral V1, Propósito Geral V2 | Objetivo Geral V1, Propósito Geral V2, BlobStorage (Bloco blobs) | Sim |
| 3,6 & 4.0 | Propósito Geral V1, Propósito Geral V2 | Armazenamento do Data Lake Ger2 | Não |
| 3,6 & 4.0 | Data Lake Storage Gen2* | Armazenamento do Data Lake Ger2 | Sim |
| 3,6 & 4.0 | Data Lake Storage Gen2* | Objetivo Geral V1, Propósito Geral V2, BlobStorage (Bloco blobs) | Sim |
| 3,6 & 4.0 | Armazenamento do Data Lake Ger2 | Armazenamento do Data Lake Ger1 | Não |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger1 | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Objetivo Geral V1, Propósito Geral V2, BlobStorage (Bloco blobs) | Sim |
| 3.6 | Armazenamento do Data Lake Ger1 | Armazenamento do Data Lake Ger2 | Não |
| 4,0 | Armazenamento do Data Lake Ger1 | Qualquer | Não |
| 4,0 | Propósito Geral V1, Propósito Geral V2 | Armazenamento do Data Lake Ger1 | Não |

*=Esta pode ser uma ou múltiplas contas de Data Lake Storage Gen2, desde que estejam todas configuradas para usar a mesma identidade gerida para acesso ao cluster.

> [!NOTE]
> O armazenamento primário do Data Lake Gen2 não é suportado para clusters Spark 2.1 ou 2.2.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Armazenamento do Azure](./overview-azure-storage.md)
* [Descrição geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Descrição geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Introdução ao Armazenamento de Lagos De Dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
