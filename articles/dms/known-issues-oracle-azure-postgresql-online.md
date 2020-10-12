---
title: 'Questões conhecidas: Migrar da Oracle para a Base de Dados de Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações on-line da Oracle para Azure Database para PostgreSQL-Single servidor utilizando o Serviço de Migração da Base de Dados Azure.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 2316e0314c9d8f36aee123d4a516f382f58e83e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291661"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Questões conhecidas/limitações de migração com migrações on-line da Oracle para Azure DB para PostgreSQL-Single servidor

Problemas e limitações conhecidos associados às migrações on-line da Oracle para Azure Database para PostgreSQL-Single servidor são descritos nas seguintes secções.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versões oracle suportadas como base de dados de origem

O Serviço de Migração da Base de Dados Azure suporta a ligação a:

- Oracle versão 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

O Serviço de Migração da Base de Dados Azure não suporta a ligação a bases de dados de contentores multi-inquilinos (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versões PostgreSQL suportadas como base de dados-alvo

O Azure Database Migration Service suporta migrações para Azure Database para PostgreSQL-Single versão 9.5, 9.6, 10 e 11. Consulte as [versões de base de dados PostgreSQL suportadas](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) para obter informações atuais sobre suporte de versão no Azure Database para PostgreSQL-Single servidor.

## <a name="datatype-limitations"></a>Limitações do tipo de dados

Os seguintes tipos de dados **não serão** migrados:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Mesas aninhadas
- Tipos de dados definidos pelo utilizador
- Notas
- Colunas virtuais
- Vistas materializadas com base na coluna ROWID

Além disso, as colunas BLOB/CLOB vazias são mapeadas para NU NO ALVO.

## <a name="lob-limitations"></a>Limitações LOB

- Quando o modo LOB de tamanho limitado está ativado, os LOBs vazios na fonte Oráculo são replicados como valores NUOS.
- Os nomes de objetos longos (mais de 30 bytes) não são suportados.
- Os dados na coluna RAW LONG and LONG não podem exceder 64k. Qualquer dado além de 64k será truncado.
- Apenas no Oráculo 12, quaisquer alterações nas colunas LOB não são suportadas (migradas).
- As colunas UPDATEs para XMLTYPE e LOB não são suportadas (migradas).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

- O utilizador deve ter privilégios DBA no Servidor Oráculo.
- As alterações de dados resultantes de operações de partição/sub-partição (ADD, DROP, EXCHANGE e TRUNCATE) não serão migradas e podem causar os seguintes erros:
  - Para as operações ADD, as atualizações e eliminações nos dados adicionados podem retornar um aviso de "0 linhas afetadas".
  - Para as operações DROP e TRUNCATE, novas inserções podem resultar em erros de "duplicados".
  - Para as operações EXCHANGE, podem ocorrer erros de "0 linhas afetadas" e "duplicações".
- Tabelas cujos nomes contêm apóstrofos não podem ser replicadas.
