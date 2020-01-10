---
title: Armazenamentos de dados com suporte no compartilhamento de dados do Azure
description: Saiba mais sobre os armazenamentos de dados com suporte para usar o compartilhamento de dados do Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 56103ed89d2e7813fd60bc50ecca7271f5421a4a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438694"
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
| Explorador de Dados do Azure | |[Visualização limitada](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Matriz de suporte do armazenamento de dados

O compartilhamento de dados do Azure oferece flexibilidade aos consumidores de dados ao decidir sobre um armazenamento de dados para aceitar dados no. Por exemplo, os dados que estão sendo compartilhados do banco de dados SQL do Azure podem ser recebidos em Azure Data Lake Store Gen2, banco de dados SQL do Azure ou Azure Synapse Analytics. Os clientes podem escolher em qual formato receber dados ao configurar um compartilhamento de dados recebido. 

A tabela abaixo detalha combinações diferentes e escolhas que os consumidores de dados têm ao aceitar e configurar seu compartilhamento de dados. Para obter mais informações sobre como configurar mapeamentos de conjunto de dados, consulte [como configurar mapeamentos de conjunto](how-to-configure-mapping.md)de dados.

|  | Armazenamento de Blobs do Azure | Gen1 SQL do Azure Data Lake | Gen2 SQL do Azure Data Lake | Base de Dados SQL do Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ ||✓|
| Armazenamento Ger1 do Azure Data Lake |✓ | |✓|
| Ger2 de Armazenamento do Azure Data Lake |✓ | |✓|
| Base de Dados SQL do Azure |✓ | |✓|✓|✓|
| Azure Synapse Analytics |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .
