---
title: Armazenamentos de dados suportados no Azure Data Share
description: Saiba mais sobre as lojas de dados que são suportadas para utilização no Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812629"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamentos de dados suportados no Azure Data Share

O Azure Data Share fornece partilha de dados aberta e flexível, incluindo a capacidade de partilhar de e para diferentes lojas de dados. Os fornecedores de dados podem partilhar dados de um tipo de loja de dados, e os consumidores de dados podem escolher uma loja de dados para receber os dados. 

Neste artigo, você vai aprender sobre o rico conjunto de lojas de dados Azure que a Azure Data Share suporta. Também vai aprender como os fornecedores de dados e os consumidores de dados podem combinar diferentes lojas de dados. 

## <a name="supported-data-stores"></a>Arquivos de dados suportados 

A tabela seguinte explica as lojas de dados que o Azure Data Share suporta. 

| Arquivo de dados | Partilha com base em instantâneos completos | Partilha com base em instantâneos incrementais | Partilha no lugar 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ |✓ | |
| Armazenamento do Azure Data Lake Ger1 |✓ |✓ | |
| Armazenamento do Azure Data Lake Ger2 |✓ |✓ ||
| Base de Dados SQL do Azure |✓ | | |
| Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics (espaço de trabalho) piscina SQL dedicada |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Matriz de suporte de loja de dados

A Azure Data Share permite que os consumidores de dados escolham uma loja de dados para aceitar dados. Por exemplo, os dados partilhados a partir da Base de Dados Azure SQL podem ser recebidos em Azure Data Lake Storage Gen2, Azure SQL Database ou Azure Synapse Analytics. Quando os clientes configuram uma partilha de dados recetora, podem escolher o formato para receber os dados. 

A tabela seguinte explica as combinações e opções que os consumidores de dados podem escolher quando aceitam e configuram uma partilha de dados. Para obter mais informações, consulte [configurar um mapeamento de conjuntos de dados](how-to-configure-mapping.md).

| Arquivo de dados | Armazenamento de Blobs | Ger1 de Armazenamento do Data Lake | Data Lake Storage Gen2 | Base de Dados SQL | Synapse Analytics (antigo Armazém de Dados SQL) | Synapse Analytics (espaço de trabalho) piscina SQL dedicada | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Armazenamento de Blobs | ✓ || ✓ |||
| Ger1 de Armazenamento do Data Lake | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| Base de Dados SQL | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (antigo Armazém de Dados SQL) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (espaço de trabalho) piscina SQL dedicada | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Partilhar a partir de uma conta de armazenamento
O Azure Data Share suporta a partilha de ficheiros, pastas e sistemas de ficheiros da Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Também suporta a partilha de bolhas, pastas e recipientes do Azure Blob Storage. Pode partilhar blocos, apêndices ou bolhas de página, e são recebidos como bolhas de bloco.

Quando os sistemas de ficheiros, contentores ou pastas são partilhados na partilha baseada em instantâneos, os consumidores de dados podem optar por fazer uma cópia completa dos dados partilhados. Ou podem usar a capacidade de instantâneo incremental para copiar apenas novos ficheiros ou ficheiros atualizados. 

Uma imagem incremental baseia-se no último tempo modificado dos ficheiros. Os ficheiros existentes que têm o mesmo nome que os ficheiros nos dados recebidos são substituídos num instantâneo. Os ficheiros que são eliminados da fonte não são eliminados no alvo. 

Para mais informações, consulte [Partilhar e receber dados do Azure Blob Storage e do Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Partilhar a partir de uma fonte baseada em SQL
A Azure Data Share suporta a partilha de tabelas e vistas da Azure SQL Database e da Azure Synapse Analytics (anteriormente Azure SQL Data Warehouse). Suporta a partilha de mesas da Azure Synapse Analytics (workspace) dedicada à piscina SQL. A partilha da piscina SQL sem servidor da Azure Synapse Analytics (espaço de trabalho) não é suportada atualmente. 

Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Storage Gen2 ou no Azure Blob Storage como um ficheiro CSV ou arquivo parquet. Também podem aceitar dados como tabelas na Base de Dados Azure SQL e na Azure Synapse Analytics.

Quando os consumidores aceitam dados no Azure Data Lake Storage Gen2 ou no Azure Blob Storage, as imagens completas substituem o conteúdo do ficheiro-alvo se o ficheiro já existir. Quando os dados são recebidos numa tabela e a tabela-alvo já não existe, a Azure Data Share cria uma tabela SQL utilizando o esquema de origem. Se uma tabela alvo já existe e tem o mesmo nome, é largada e substituída com o último instantâneo completo. As imagens incrementais não são suportadas atualmente.

Para mais informações, consulte [Partilhar e receber dados da Azure SQL Database e da Azure Synapse Analytics.](how-to-share-from-sql.md)

## <a name="share-from-data-explorer"></a>Partilhar do Data Explorer
O Azure Data Share suporta a capacidade de partilhar bases de dados no local a partir de clusters Azure Data Explorer. Um fornecedor de dados pode partilhar ao nível da base de dados ou do cluster. 

Quando os dados são partilhados ao nível da base de dados, os consumidores de dados podem aceder apenas às bases de dados que o fornecedor de dados partilhou. Quando um fornecedor partilha dados ao nível do cluster, os consumidores de dados podem aceder a todas as bases de dados a partir do cluster do fornecedor, incluindo quaisquer bases de dados futuras que o fornecedor de dados cria.

Para aceder a bases de dados partilhadas, os consumidores de dados precisam do seu próprio cluster Azure Data Explorer. O seu cluster deve estar no mesmo datacenter Azure que o cluster Azure Data Explorer do fornecedor de dados. 

Quando uma relação de partilha é estabelecida, a Azure Data Share cria uma ligação simbólica entre o cluster do fornecedor e o cluster do consumidor. Os dados que são ingeridos no cluster de origem utilizando o modo de lote aparecem no cluster alvo dentro de poucos minutos.

Para mais informações, consulte [Partilhar e receber dados do Azure Data Explorer.](/azure/data-explorer/data-share) 

## <a name="next-steps"></a>Passos seguintes

Para aprender a partilhar dados, continue a partilhar o seu tutorial [de dados.](share-your-data.md)
