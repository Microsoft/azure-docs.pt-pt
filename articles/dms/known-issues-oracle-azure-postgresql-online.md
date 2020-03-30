---
title: 'Questões conhecidas: Migrar da Oracle para a Base de Dados Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Conheça questões conhecidas e limitações de migração com migrações on-line da Oracle para a Base de Dados Azure para o servidor PostgreSQL-Single utilizando o Serviço de Migração de Bases de Dados Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235249"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Questões conhecidas/limitações de migração com migrações on-line de Oracle para Azure DB para o servidor PostgreSQL-Single

Questões e limitações conhecidas associadas às migrações on-line da Oracle para a Base de Dados Azure para o servidor PostgreSQL-Single são descritas nas seguintes secções.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versões Oracle suportadas como base de dados de origem

O Serviço de Migração da Base de Dados Azure suporta a ligação a:

- Oracle versão 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Edição Pessoal.

O Serviço de Migração de Bases de Dados Azure não suporta a ligação a bases de dados de contentores multi-inquilinos (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versões PostgreSQL suportadas como base de dados de destino

O Azure Database Migration Service suporta migrações para a Base de Dados Azure para a versão do servidor PostgreSQL-Single 9.5, 9.6, 10 e 11. Consulte as versões de base de [dados PostgreSQL suportadas](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) para obter informações atuais sobre suporte de versão na Base de Dados Azure para servidor PósgreSQL-Single.

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

Além disso, as colunas BLOB/CLOB vazias são mapeadas para NULO no alvo.

## <a name="lob-limitations"></a>Limitações lob

- Quando o modo LOB de tamanho limitado estiver ativado, os LOBs vazios na fonte oráculo são replicados como valores NULOS.
- Nomes de objetos longos (mais de 30 bytes) não são suportados.
- Os dados na coluna LONG e LONG RAW não podem exceder 64k. Qualquer dado além de 64k será truncado.
- Apenas no Oráculo 12, quaisquer alterações nas colunas LOB não são suportadas (migradas).
- As colunas UPDATEs para XMLTYPE e LOB não são suportadas (migradas).

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

- Os clientes devem usar o SYSDBA para se ligarem à Oracle.
- As alterações de dados resultantes de operações de partição/subpartição (ADD, DROP, EXCHANGE e TRUNCATE) não serão migradas e podem causar os seguintes erros:
  - Para operações ADD, atualizações e eliminações nos dados adicionados pode devolver um aviso de "0 linhas afetadas".
  - Para as operações DROP e TRUNCATE, novas inserções podem resultar em erros de "duplicados".
  - Para as operações EXCHANGE, podem ocorrer um aviso de "0 linhas afetadas" e erros de "duplicados".
- Tabelas cujos nomes contêm apóstrofos não podem ser replicadas.
