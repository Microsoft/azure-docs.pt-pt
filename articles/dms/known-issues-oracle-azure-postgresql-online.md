---
title: Artigo sobre problemas conhecidos/limitações de migração com migrações online do Oracle para o banco de dados do Azure para PostgreSQL-servidor único | Microsoft Docs
description: Saiba mais sobre problemas conhecidos/limitações de migração com migrações online do Oracle para o banco de dados do Azure para PostgreSQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: cc7e510d9a6db93edc211799eda8df79116236a7
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053656"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemas conhecidos/limitações de migração com migrações online do Oracle para o BD do Azure para PostgreSQL-servidor único

Problemas conhecidos e limitações associadas a migrações online do Oracle para o banco de dados do Azure para PostgreSQL-um único servidor são descritos nas seções a seguir.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versões do Oracle com suporte como um banco de dados de origem

O serviço de migração de banco de dados do Azure dá suporte à conexão com

- Oracle versão 10g, 11C e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

O serviço de migração de banco de dados do Azure não dá suporte à conexão com bancos de dados de contêiner multilocatário (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versões do PostgreSQL com suporte como um banco de dados de destino

O serviço de migração de banco de dados do Azure dá suporte a migrações para o banco de dados do Azure para PostgreSQL, versão 9,5, 9,6, 10 e 11 do servidor. Consulte o artigo [versões de banco de dados PostgreSQL com suporte](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) para obter informações atuais sobre o suporte de versão no banco de dados do Azure para PostgreSQL-servidor único.

## <a name="datatype-limitations"></a>Limitações de DataType

Os seguintes tipos de texto **não** serão migrados:

- BArquivo
- ROWID
- REFERÊNCIA
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Tabelas aninhadas
- Tipos de dados definidos pelo usuário
- Notas
- Colunas virtuais
- Exibições materializadas com base na coluna de ROWID

Além disso, colunas BLOB/CLOB vazias são mapeadas para NULL no destino.

## <a name="lob-limitations"></a>Limitações de LOB

- Quando o modo LOB de tamanho limitado estiver habilitado, os LOBs vazios na origem do Oracle serão replicados como valores nulos.
- Não há suporte para nomes de objeto longos (mais de 30 bytes).
- Os dados na coluna bruta longa e longa não podem exceder 64K. Todos os dados além de 64K serão truncados.
- Somente no Oracle 12, não há suporte para alterações nas colunas LOB (migradas).
- Não há suporte para atualizações em colunas XMLTYPE e LOB (migradas).

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

- Os clientes devem usar o SYSDBA para se conectar ao Oracle.
- As alterações de dados resultantes de operações de partição/subpartição (adicionar, descartar, trocar e TRUNCAr) não serão migradas e poderão causar os seguintes erros:
  - Para adicionar operações, as atualizações e exclusões nos dados adicionados podem retornar um aviso "0 linhas afetadas".
  - Para operações DROP e TRUNCATE, novas inserções podem resultar em erros de "duplicatas".
  - Para operações do EXCHANGE, um aviso "0 linhas afetadas" e erros "duplicatas" podem ocorrer.
- Tabelas cujos nomes contêm apóstrofos não podem ser replicados.
