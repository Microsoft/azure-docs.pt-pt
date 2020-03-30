---
title: Lojas de dados suportadas em Partilha de Dados Do Azure
description: Conheça as lojas de dados que são suportadas para utilização da Partilha de Dados do Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501803"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Lojas de dados suportadas em Partilha de Dados Do Azure

A Azure Data Share fornece uma partilha de dados aberta e flexível, incluindo a capacidade de partilhar de e para diferentes lojas de dados. Os fornecedores de dados podem partilhar dados de um tipo de loja de dados, e os seus consumidores de dados podem escolher em que loja de dados receber dados. 

Neste artigo, você vai aprender sobre o rico conjunto de lojas de dados Azure que são suportadas na Azure Data Share. Também pode encontrar informações sobre as combinações de lojas de dados que podem ser alavancadas por fornecedores de dados e consumidores de dados. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Que lojas de dados são suportadas na Partilha de Dados do Azure? 

A tabela abaixo detalha as fontes de dados suportadas para a Partilha de Dados do Azure. 

| Arquivo de dados | Partilha baseada em instantâneos | Partilha no local 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ | |
| Armazenamento do Azure Data Lake Ger1 |✓ | |
| Armazenamento do Azure Data Lake Ger2 |✓ ||
| Base de Dados SQL do Azure |Pré-visualização pública | |
| Azure Synapse Analytics (anteriormente Azure SQL DW) |Pré-visualização pública | |
| Azure Data Explorer | |Pré-visualização pública |

## <a name="data-store-support-matrix"></a>Matriz de suporte da loja de dados

A Azure Data Share oferece aos consumidores de dados flexibilidade na decisão de uma loja de dados para aceitar dados. Por exemplo, os dados partilhados a partir da Base de Dados Azure SQL podem ser recebidos na Azure Data Lake Store Gen2, Azure SQL Database ou Azure Synapse Analytics. Os clientes podem escolher em que formato receber dados ao configurar uma parte de dados recebida. 

A tabela abaixo detalha diferentes combinações e escolhas que os consumidores de dados têm ao aceitar e configurar a sua partilha de dados. Para obter mais informações sobre como configurar mapeamentos de conjuntos de dados, consulte [como configurar mapeamentos](how-to-configure-mapping.md)de conjuntos de dados .

|  | Armazenamento de Blobs do Azure | Armazenamento do Azure Data Lake Ger1 | Armazenamento do Azure Data Lake Ger2 | Base de Dados SQL do Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure | ✓ || ✓|
| Armazenamento do Azure Data Lake Ger1 | ✓ | | ✓|
| Armazenamento do Azure Data Lake Ger2 | ✓ | | ✓|
| Base de Dados SQL do Azure | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (anteriormente Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Partilhar de uma conta de armazenamento
A Azure Data Share suporta a partilha de ficheiros, pastas e sistemas de ficheiros do Lago De dados Azure Gen1 e do Lago de Dados Azure Gen2. Também suporta a partilha de bolhas, pastas e contentores do Armazenamento De Blob Azure. Atualmente, apenas a bolha de bloco é suportada. Quando as pastas são partilhadas na partilha baseada em instantâneos, o consumidor de dados pode optar por fazer uma cópia completa dos dados de partilha, ou alavancar a capacidade incremental de instantâneo para copiar apenas ficheiros novos ou atualizados. Os ficheiros existentes com o mesmo nome serão substituídos.

## <a name="share-from-a-sql-based-source"></a>Partilhar de uma fonte baseada em SQL
A Azure Data Share suporta a partilha de tabelas ou vistas da Azure SQL Database e da Azure Synapse Analytics (anteriormente Azure SQL DW). Os consumidores de dados podem optar por aceitar os dados na Azure Data Lake Store Gen2 ou no Azure Blob Storage como ficheiro csv ou parquet. Note que, por padrão, os formatos de ficheiro são csv. O consumidor de dados pode optar por receber os dados em formato parquet, se assim o desejar. Isto pode ser feito nas definições de mapeamento do conjunto de dados ao receber os dados. 

Ao aceitar dados na Azure Data Lake Store Gen2 ou no Azure Blob Storage, as imagens completas sobreporem o conteúdo do ficheiro alvo. 

Um consumidor de dados pode optar por receber dados numa tabela à sua escolha. Neste cenário, se a tabela-alvo já não existir, a Azure Data Share cria a tabela SQL com o esquema de origem. Se uma tabela de destino já existir com o mesmo nome, será largada e substituída com o mais recente instantâneo completo. Ao mapear a tabela de destino, pode especificar-se um esquema alternativo e um nome de mesa. Os instantâneos incrementais não são atualmente suportados. 

A partilha de fontes baseadas em SQL tem pré-requisitos relacionados com regras e permissões de firewall. Consulte a secção de pré-requisitos do tutorial de [partilha](share-your-data.md) para obter mais detalhes.

## <a name="share-from-azure-data-explorer"></a>Partilhar do Azure Data Explorer
A Azure Data Share suporta a capacidade de partilhar bases de dados em vigor a partir de clusters Do Azure Data Explorer. O fornecedor de dados pode partilhar na base de dados ou no nível do cluster. Quando partilhados ao nível da base de dados, o consumidor de dados só poderá aceder à(s) base de dados específica partilhada pelo fornecedor de dados. Quando partilhado ao nível do cluster, o consumidor de dados pode aceder a todas as bases de dados do cluster do fornecedor, incluindo quaisquer futuras bases de dados criadas pelo fornecedor de dados.

Para aceder a bases de dados partilhadas, o consumidor de dados precisa de ter o seu próprio cluster Azure Data Explorer. O cluster Azure Data Explorer do consumidor de dados precisa de se localizar no mesmo centro de dados do Azure que o cluster Azure Data Explorer do fornecedor de dados. Ao estabelecer relações de partilha, a Azure Data Share cria uma ligação simbólica entre o fornecedor e os clusters do Operador De dados Azure.

O Azure Data Explorer suporta dois modos de ingestão de dados: lote e streaming. Os dados recebidos do lote na base de dados partilhada aparecerão entre alguns segundos a alguns minutos do lado do consumidor de dados. Os dados recebidos do streaming podem demorar até 24 horas a aparecer do lado dos consumidores de dados. 

## <a name="next-steps"></a>Passos seguintes

Para aprender a começar a partilhar dados, continue a partilhar o seu tutorial de [dados.](share-your-data.md)
