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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695775"
---
# <a name="supported-data-stores"></a>Arquivos de dados suportados

A Purview suporta as seguintes lojas de dados. Clique em cada loja de dados para saber as capacidades suportadas e as configurações correspondentes em detalhes.

## <a name="purview-data-sources"></a>Fontes de dados de competência

|**Categoria**|  **Loja de Dados**  |**Extração de metadados**|**Digitalização completa**|**Digitalização incremental**|**Digitalização de âmbito**|**Classificação**|**Linhagem**|
|---|---|---|---|---|---|---|---|
| Azure | [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md)| Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md) (Armazenamento do Azure Data Lake Gen2)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Base de Dados SQL do Azure](register-scan-azure-sql-database.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Exemplo gerido pela base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Azure Synapse Analytics (anteriormente SQL DW)](register-scan-azure-synapse-analytics.md)|Yes| Yes| No| Yes| Yes| Yes|
|Base de Dados|[Oracle DB](register-scan-oracle-source.md)|Yes| Yes| No| No| No| Yes|
||[SQL Server](register-scan-on-premises-sql-server.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Teradata](register-scan-teradata-source.md)|Yes| Yes| No| No| No| Yes|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Yes| Yes| No| No| No| Yes|
|Serviços e aplicações|[SAP ECC](register-scan-sapecc-source.md)|Yes| Yes| No| Yes| Yes| Yes|
||[SAP S4HANA](register-scan-saps4hana-source.md)|Yes| Yes| No| Yes| Yes| Sim|

## <a name="next-steps"></a>Próximos passos

- [Registar e digitalizar fonte de armazenamento Azure Blob](register-scan-azure-blob-storage-source.md)