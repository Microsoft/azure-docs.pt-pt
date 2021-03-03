---
title: Visão geral do conector de visão geral do conector
description: Este artigo descreve as diferentes lojas de dados e funcionalidades suportadas em Purview
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695775"
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
||[Exemplo gerido pela base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Azure Synapse Analytics (anteriormente SQL DW)](register-scan-azure-synapse-analytics.md)|Sim| Sim| Não| Sim| Sim| Sim|
|Base de Dados|[Oracle DB](register-scan-oracle-source.md)|Sim| Sim| Não| Não| Não| Sim|
||[SQL Server](register-scan-on-premises-sql-server.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[Teradata](register-scan-teradata-source.md)|Sim| Sim| Não| Não| Não| Sim|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Sim| Sim| Não| Não| Não| Sim|
|Serviços e aplicações|[SAP ECC](register-scan-sapecc-source.md)|Sim| Sim| Não| Sim| Sim| Sim|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Sim| Sim| Não| Sim| Sim| Sim|

## <a name="next-steps"></a>Próximos passos

- [Registar e digitalizar fonte de armazenamento Azure Blob](register-scan-azure-blob-storage-source.md)