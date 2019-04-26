---
title: Otimizar inserções em massa numa base de dados do Azure para o servidor PostgreSQL
description: Este artigo descreve como pode otimizar as operações de inserção em massa numa base de dados do Azure para o servidor PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/22/2018
ms.date: 03/04/2019
ms.openlocfilehash: a82984ce4c2a2e44306abaa63265e0c25cc6ace4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422122"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Otimizar inserções em massa e utilizar dados transitórios numa base de dados do Azure para o servidor PostgreSQL 
Este artigo descreve como pode otimizar as operações de inserção em massa e usar dados transitórios numa base de dados do Azure para o servidor PostgreSQL.

## <a name="use-unlogged-tables"></a>Utilizar tabelas unlogged
Se tiver de operações de carga de trabalho que envolvem dados transitórios ou que inserir grandes conjuntos de dados em massa, considere utilizar tabelas unlogged.

Tabelas unlogged é uma funcionalidade de PostgreSQL que pode ser utilizada eficazmente para otimizar as inserções em massa. PostgreSQL utiliza o registo de escrita-Ahead (WAL). Ele fornece atomicidade e durabilidade, por predefinição. Atomicidade, consistência, isolamento e durabilidade constituem as propriedades ACID. 

Inserir num meio de tabela unlogged esse PostgreSQL inserções sem ter de escrever para a transação iniciar sessão, que por si só é uma operação de e/s. Como resultado, essas tabelas são consideravelmente mais rápidas do que as tabelas comuns.

Utilize as seguintes opções para criar uma tabela unlogged:
- Criar uma nova tabela unlogged utilizando a sintaxe `CREATE UNLOGGED TABLE <tableName>`.
- Converter um existente com sessão iniciada tabela para uma tabela de unlogged utilizando a sintaxe `ALTER TABLE <tableName> SET UNLOGGED`.  

Para reverter o processo, utilize a sintaxe `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Compensação de tabela unlogged
Tabelas unlogged não são seguras para falhas. Uma tabela unlogged é truncada automaticamente após uma pane ou sujeitos a um encerramento unclean. O conteúdo de uma tabela unlogged também não é replicado para servidores em espera. Índices criados numa tabela unlogged também são automaticamente unlogged. Após a inserção operação for concluída, converter a tabela de sessão iniciada para que a inserção é durável.

Algumas cargas de trabalho do cliente tem ocorrido aproximadamente uma melhoria de desempenho de 15 a 20 por cento quando unlogged tabelas foram utilizadas.

## <a name="next-steps"></a>Passos Seguintes
Reveja a sua carga de trabalho para utilizações de dados transitórios e inserções em grande volume. Consulte a seguinte documentação de PostgreSQL:
 
- [Criar comandos SQL de tabela](https://www.postgresql.org/docs/current/static/sql-createtable.html)