---
title: Visão geral do conector de visão geral do conector
description: Este artigo descreve as diferentes lojas de dados e funcionalidades suportadas em Purview
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 26efa840bacd3ee542816cf861cf2ef586b0582f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780217"
---
# <a name="supported-data-stores"></a>Arquivos de dados suportados

A Purview suporta as seguintes lojas de dados. Clique em cada loja de dados para saber as capacidades suportadas e as configurações correspondentes em detalhes.

## <a name="purview-data-sources"></a>Fontes de dados de competência

|**Categoria**|  **Loja de Dados**  |**Extração de metadados**|**Digitalização completa**|**Digitalização incremental**|**Digitalização de âmbito**|**Classificação**|**Linhagem**|
|---|---|---|---|---|---|---|---|
| Azure | [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md)| Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md) (Armazenamento do Azure Data Lake Gen2)|Sim| Sim| Sim| Sim| Sim| Sim|
||[Base de Dados SQL do Azure](register-scan-azure-sql-database.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Instância Gerida da Base de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Azure Synapse Analytics (anteriormente SQL DW)](register-scan-azure-synapse-analytics.md)|Sim| Sim| Não| Sim| Sim| Sim|
|Base de Dados|[SQL Server](register-scan-on-premises-sql-server.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Teradata (Pré-visualização)](register-scan-teradata-source.md)|Sim| Sim| Não| Não| Não| Sim|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Sim| Sim| Não| Não| Não| Sim|

## <a name="next-steps"></a>Próximos passos

- [Registar e digitalizar fonte de armazenamento Azure Blob](register-scan-azure-blob-storage-source.md)