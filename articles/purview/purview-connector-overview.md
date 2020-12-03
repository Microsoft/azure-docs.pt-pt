---
title: Visão geral do conector de visão geral do conector
description: Este artigo descreve as diferentes lojas de dados e funcionalidades suportadas em Purview
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555139"
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
|Base de Dados|[SQL Server](register-scan-on-premises-sql-server.md)|Sim| Sim| Não| Sim| Sim| Sim|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Sim| Sim| Não| Não| Não| Sim|

## <a name="next-steps"></a>Próximos passos

- [Registar e digitalizar fonte de armazenamento Azure Blob](register-scan-azure-blob-storage-source.md)