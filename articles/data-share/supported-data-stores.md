---
title: Armazenamentos de dados suportados no Azure Data Share
description: Saiba mais sobre as lojas de dados que são suportadas para a utilização do Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f3ecf8ef22d3f1d66b7148b809475a830c7e9f13
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318585"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamentos de dados suportados no Azure Data Share

O Azure Data Share fornece partilha de dados aberta e flexível, incluindo a capacidade de partilhar de e para diferentes lojas de dados. Os fornecedores de dados podem partilhar dados de um tipo de loja de dados, e os seus consumidores de dados podem escolher em que datas armazenam os dados para receber dados. 

Neste artigo, você vai aprender sobre o rico conjunto de lojas de dados Azure que são suportados em Azure Data Share. Também pode encontrar informações sobre as combinações de lojas de dados que podem ser alavancadas por fornecedores de dados e consumidores de dados. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quais as lojas de dados suportadas no Azure Data Share? 

A tabela abaixo detalha as fontes de dados suportadas para a Azure Data Share. 

| Arquivo de dados | Partilha baseada em instantâneos | Partilha no local 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ | |
| Armazenamento do Azure Data Lake Ger1 |✓ | |
| Armazenamento do Azure Data Lake Ger2 |✓ ||
| Base de Dados SQL do Azure |✓ | |
| Azure Synapse Analytics (anteriormente Azure SQL DW) |✓ | |
| Azure Data Explorer | |✓ |

## <a name="data-store-support-matrix"></a>Matriz de suporte de loja de dados

O Azure Data Share oferece flexibilidade aos consumidores de dados ao decidir em uma loja de dados para aceitar dados. Por exemplo, os dados que estão a ser partilhados a partir da Base de Dados Azure SQL podem ser recebidos na Azure Data Lake Store Gen2, na Base de Dados Azure SQL ou na Azure Synapse Analytics. Os clientes podem escolher em que formato receber dados ao configurar uma partilha de dados recebida. 

A tabela abaixo detalha diferentes combinações e escolhas que os consumidores de dados têm ao aceitar e configurar a sua partilha de dados. Para obter mais informações sobre como configurar os mapeamentos do conjunto de dados, consulte [como configurar os mapeamentos do conjunto de dados](how-to-configure-mapping.md).

| Arquivo de dados | Armazenamento de Blobs do Azure | Armazenamento do Azure Data Lake Ger1 | Armazenamento do Azure Data Lake Ger2 | Base de Dados SQL do Azure | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure | ✓ || ✓ ||
| Armazenamento do Azure Data Lake Ger1 | ✓ | | ✓ ||
| Armazenamento do Azure Data Lake Ger2 | ✓ | | ✓ ||
| Base de Dados SQL do Azure | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (anteriormente Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Partilhar a partir de uma conta de armazenamento
O Azure Data Share suporta a partilha de ficheiros, pastas e sistemas de ficheiros da Azure Data Lake Gen1 e da Azure Data Lake Gen2. Também suporta a partilha de bolhas, pastas e contentores do Azure Blob Storage. Apenas a bolha de blocos está atualmente suportada. Quando os sistemas de ficheiros, contentores ou pastas são partilhados na partilha baseada em instantâneos, o consumidor de dados pode optar por fazer uma cópia completa dos dados de partilha ou aproveitar a capacidade incremental de instantâneo para copiar apenas ficheiros novos ou atualizados. O instantâneo incremental baseia-se no último tempo modificado dos ficheiros. Os ficheiros existentes com o mesmo nome serão substituídos.

Consulte a [Partilha e receba dados do Azure Blob Storage e do Azure Data Lake Storage](how-to-share-from-storage.md) para mais detalhes.

## <a name="share-from-a-sql-based-source"></a>Partilhar a partir de uma fonte baseada em SQL
A Azure Data Share suporta a partilha de tabelas ou vistas da Azure SQL Database e da Azure Synapse Analytics (anteriormente Azure SQL DW). Os consumidores de dados podem optar por aceitar os dados em Azure Data Lake Storage Gen2 ou Azure Blob Storage como ficheiro csv ou parquet, bem como na Base de Dados Azure SQL e Azure Synapse Analytics como tabelas.

Ao aceitar dados no Azure Data Lake Store Gen2 ou no Azure Blob Storage, as imagens completas substituem o conteúdo do ficheiro-alvo se já existirem.
Quando os dados são recebidos em tabela e se a tabela-alvo já não existir, a Azure Data Share cria a tabela SQL com o esquema de origem. Se já existir uma tabela-alvo com o mesmo nome, será largada e substituída com o último instantâneo completo. As imagens incrementais não são suportadas atualmente.

Consulte a [Partilhar e receber dados da Azure SQL Database e da Azure Synapse Analytics](how-to-share-from-sql.md) para obter mais detalhes.

## <a name="share-from-azure-data-explorer"></a>Partilhar a partir do Azure Data Explorer
O Azure Data Share suporta a capacidade de partilhar bases de dados no local a partir de clusters Azure Data Explorer. O fornecedor de dados pode partilhar na base de dados ou no nível de cluster. Quando partilhado ao nível da base de dados, o consumidor de dados só poderá aceder à(s) base de dados específica partilhada pelo fornecedor de dados. Quando partilhado a nível de cluster, o consumidor de dados pode aceder a todas as bases de dados do cluster do fornecedor, incluindo quaisquer bases de dados futuras criadas pelo fornecedor de dados.

Para aceder a bases de dados partilhadas, o consumidor de dados precisa de ter o seu próprio cluster Azure Data Explorer. O cluster Azure Data Explorer do consumidor de dados precisa de se localizar no mesmo centro de dados Azure que o cluster Azure Data Explorer do fornecedor de dados. Ao estabelecer a relação de partilha, a Azure Data Share cria uma ligação simbólica entre os clusters Azure Data Explorer do fornecedor e do consumidor. Os dados ingeridos utilizando o modo de lote no cluster Azure Data Explorer de origem aparecerão no cluster alvo dentro de alguns segundos a poucos minutos.

Consulte a [Partilhar e receber dados do Azure Data Explorer](/azure/data-explorer/data-share) para obter mais detalhes. 

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)
