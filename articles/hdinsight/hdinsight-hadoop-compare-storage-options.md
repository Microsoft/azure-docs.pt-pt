---
title: Compare as opções de armazenamento para utilização com clusters Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como funcionam com a Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945855"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Compare as opções de armazenamento para utilização com clusters Azure HDInsight

Pode escolher entre alguns serviços de armazenamento Azure diferentes ao criar clusters HDInsight:

* [Armazenamento Azure Blob com HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 com HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 com HDInsight](./overview-data-lake-storage-gen1.md)

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
|Storage do Azure| Blob de Bloco| Objeto | Blob de Bloco | Premium | N/D| 3.6+ | Apenas HBase com escritas aceleradas|
|Armazenamento do Azure Data Lake Ger2| Blob de Bloco| Hierárquico (sistema de ficheiros) | Blob de Bloco | Premium | N/D| 3.6+ | Apenas HBase com escritas aceleradas|

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
| 4.0 | Ger1 de Armazenamento do Data Lake | Qualquer | No |
| 4.0 | Final geral V1, Finalidade Geral V2 | Ger1 de Armazenamento do Data Lake | No |

*=Isto pode ser um ou vários Data Lake Storage Gen2, desde que todos estejam configurados para usar a mesma identidade gerida para o acesso ao cluster.

> [!NOTE]
> O armazenamento primário da Gen2 de armazenamento de dados não é suportado para clusters Spark 2.1 ou 2.2.

## <a name="data-replication"></a>Replicação de dados

A Azure HDInsight não armazena os dados do cliente. Os principais meios de armazenamento de um cluster são as suas contas de armazenamento associadas. Pode anexar o seu cluster a uma conta de armazenamento existente ou criar uma nova conta de armazenamento durante o processo de criação do cluster. Se uma nova conta for criada, será criada como uma conta de armazenamento localmente redundante (LRS) e irá satisfazer os requisitos de residência de dados na região, incluindo os especificados no [Trust Center](https://azuredatacentermap.azurewebsites.net).

Pode validar que o HDInsight está devidamente configurado para armazenar dados numa única região, garantindo que a conta de armazenamento associada ao seu HDInsight é LRS ou outra opção de armazenamento mencionada no [Trust Center.](https://azuredatacentermap.azurewebsites.net)
 
## <a name="next-steps"></a>Próximos passos

* [Visão geral do armazenamento Azure em HDInsight](./overview-azure-storage.md)
* [Visão geral do Azure Data Lake Storage Gen1 em HDInsight](./overview-data-lake-storage-gen1.md)
* [Visão geral do Azure Data Lake Storage Gen2 em HDInsight](./overview-data-lake-storage-gen2.md)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
