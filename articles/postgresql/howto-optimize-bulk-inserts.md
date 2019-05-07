---
title: Otimizar inserções em massa numa base de dados do Azure para PostgreSQL - servidor único
description: Este artigo descreve como pode otimizar as operações de inserção em massa numa base de dados do Azure para PostgreSQL - único servidor.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c1ae29f7c498a79af09aaaf6d7aeae29561aa500
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067037"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Otimizar inserções em massa e utilizar dados transitórios numa base de dados do Azure para PostgreSQL - servidor único 
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
