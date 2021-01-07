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
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780217"
---
# <a name="supported-data-stores"></a>Arquivos de dados suportados

A Purview suporta as seguintes lojas de dados. Clique em cada loja de dados para saber as capacidades suportadas e as configurações correspondentes em detalhes.

## <a name="purview-data-sources"></a>Fontes de dados de competência

|**Categoria**|  **Loja de Dados**  |**Extração de metadados**|**Digitalização completa**|**Digitalização incremental**|**Digitalização de âmbito**|**Classificação**|**Linhagem**|
|---|---|---|---|---|---|---|---|
| Azure | [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md)| Yes| Yes| Yes| Yes| Yes| Yes|
||[BD do Cosmos para o Azure](register-scan-azure-cosmos-database.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md) (Armazenamento do Azure Data Lake Gen2)|Yes| Yes| Yes| Yes| Yes| Yes|
||[Base de Dados SQL do Azure](register-scan-azure-sql-database.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Instância Gerida da Base de Dados SQL do Azure](register-scan-azure-sql-database-managed-instance.md)|Yes| Yes| No| Yes| Yes| Yes|
||[Azure Synapse Analytics (anteriormente SQL DW)](register-scan-azure-synapse-analytics.md)|Yes| Yes| No| Yes| Yes| Yes|
|Base de Dados|[SQL Server](register-scan-on-premises-sql-server.md)|Yes| Yes| No| Yes| Yes| Yes|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Yes| Yes| No| No| No| Sim|

## <a name="next-steps"></a>Próximos passos

- [Registar e digitalizar fonte de armazenamento Azure Blob](register-scan-azure-blob-storage-source.md)