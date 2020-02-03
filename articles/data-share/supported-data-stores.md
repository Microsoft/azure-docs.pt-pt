---
title: Armazenamentos de dados com suporte no compartilhamento de dados do Azure
description: Saiba mais sobre os armazenamentos de dados com suporte para usar o compartilhamento de dados do Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 5d4b1282b0a08657aea6f8a13aae7ed1fe49079b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964214"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamentos de dados com suporte no compartilhamento de dados do Azure

O compartilhamento de dados do Azure fornece compartilhamento de dados aberto e flexível, incluindo a capacidade de compartilhar de e para armazenamentos de dados diferentes. Os provedores de dados podem compartilhar dados de um tipo de armazenamento de dados, e seus consumidores de dados podem escolher em qual armazenamento de dados receber dados. 

Neste artigo, você aprenderá sobre o rico conjunto de armazenamentos de dados do Azure que têm suporte no compartilhamento de dados do Azure. Você também pode encontrar informações sobre as combinações de armazenamentos de dados que podem ser aproveitadas por provedores de dados e consumidores de dados. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quais armazenamentos de dados têm suporte no compartilhamento de dados do Azure? 

A tabela abaixo detalha as fontes de dados com suporte para o compartilhamento de dados do Azure. 

| Arquivo de dados | Compartilhamento baseado em instantâneo | Partilha no local 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ | |
| Armazenamento Ger1 do Azure Data Lake |✓ | |
| Ger2 de Armazenamento do Azure Data Lake |✓ ||
| Base de Dados SQL do Azure |Pré-visualização pública | |
| Análise do Azure Synapse (anteriormente Azure SQL DW) |Pré-visualização pública | |
| Explorador de Dados do Azure | |Pré-visualização pública |

## <a name="data-store-support-matrix"></a>Matriz de suporte do armazenamento de dados

O compartilhamento de dados do Azure oferece flexibilidade aos consumidores de dados ao decidir sobre um armazenamento de dados para aceitar dados no. Por exemplo, os dados que estão sendo compartilhados do banco de dados SQL do Azure podem ser recebidos em Azure Data Lake Store Gen2, banco de dados SQL do Azure ou Azure Synapse Analytics. Os clientes podem escolher em qual formato receber dados ao configurar um compartilhamento de dados recebido. 

A tabela abaixo detalha combinações diferentes e escolhas que os consumidores de dados têm ao aceitar e configurar seu compartilhamento de dados. Para obter mais informações sobre como configurar mapeamentos de conjunto de dados, consulte [como configurar mapeamentos de conjunto](how-to-configure-mapping.md)de dados.

|  | Armazenamento de Blobs do Azure | Armazenamento Ger1 do Azure Data Lake | Ger2 de Armazenamento do Azure Data Lake | Base de Dados SQL do Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure | ✓ || ✓|
| Armazenamento Ger1 do Azure Data Lake | ✓ | | ✓|
| Ger2 de Armazenamento do Azure Data Lake | ✓ | | ✓|
| Base de Dados SQL do Azure | ✓ | | ✓| ✓| ✓|
| Análise do Azure Synapse (anteriormente Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Partilhar de uma conta de armazenamento
A Azure Data Share suporta a partilha de ficheiros, pastas e sistemas de ficheiros do Lago De dados Azure Gen1 e do Lago de Dados Azure Gen2. Também suporta a partilha de bolhas, pastas e contentores do Armazenamento De Blob Azure. Quando as pastas são partilhadas na partilha baseada em instantâneos, o consumidor de dados pode optar por fazer uma cópia completa dos dados de partilha, ou alavancar a capacidade incremental de instantâneo para copiar apenas ficheiros novos ou atualizados. Os ficheiros existentes com o mesmo nome serão substituídos.

## <a name="share-from-a-sql-based-source"></a>Partilhar de uma fonte baseada em SQL
A Azure Data Share suporta a partilha de tabelas ou vistas da Azure SQL Database e da Azure Synapse Analytics (anteriormente Azure SQL DW). O consumidor de dados pode optar por aceitar os dados em Azure Data Lake Storage Gen2 ou Azure Blob Storage como csv ou arquivo parquet. O instantâneo completo substitui o conteúdo do ficheiro alvo. Alternativamente, o consumidor de dados pode aceitar os dados numa tabela SQL. Se a tabela SQL alvo não estiver disponível do lado do consumidor de dados, a Azure Data Share cria a tabela SQL com o esquema de origem. O instantâneo completo aparacom o conteúdo da tabela de origem para a tabela SQL alvo. O instantâneo incremental não é suportado atualmente.

## <a name="share-from-azure-data-explorer"></a>Partilhar do Azure Data Explorer
A Azure Data Share suporta a capacidade de partilhar bases de dados em vigor a partir de clusters Do Azure Data Explorer. O fornecedor de dados pode partilhar na base de dados ou no nível do cluster. Quando partilhados ao nível da base de dados, o consumidor de dados só poderá aceder à(s) base de dados específica partilhada pelo fornecedor de dados. Quando partilhado ao nível do cluster, o consumidor de dados pode aceder a todas as bases de dados do cluster do fornecedor, incluindo quaisquer futuras bases de dados criadas pelo fornecedor de dados.

Para aceder a bases de dados partilhadas, o consumidor de dados precisa de ter o seu próprio cluster Azure Data Explorer. O cluster Azure Data Explorer do consumidor de dados precisa de se localizar no mesmo centro de dados do Azure que o cluster Azure Data Explorer do fornecedor de dados. Ao estabelecer relações de partilha, a Azure Data Share cria uma ligação simbólica entre o fornecedor e os clusters do Operador De dados Azure.

O Azure Data Explorer suporta dois modos de ingestão de dados: lote e streaming. Os dados recebidos do lote na base de dados partilhada aparecerão entre alguns segundos a alguns minutos do lado do consumidor de dados. Os dados recebidos do streaming podem demorar até 24 horas a aparecer do lado dos consumidores de dados. 

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .
