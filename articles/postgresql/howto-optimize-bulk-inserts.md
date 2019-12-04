---
title: Otimizar inserções em massa-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode otimizar operações de inserção em massa em um banco de dados do Azure para PostgreSQL-servidor único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770140"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Otimizar inserções em massa e usar dados transitórios em um banco de dado do Azure para PostgreSQL-servidor único 
Este artigo descreve como você pode otimizar as operações de inserção em massa e usar dados transitórios em um servidor do banco de dados do Azure para PostgreSQL.

## <a name="use-unlogged-tables"></a>Usar tabelas não registradas
Se você tiver operações de carga de trabalho que envolvam dados transitórios ou que insiram conjuntos de grandes volumes em massa, considere o uso de tabelas não registradas em log.

As tabelas não registradas em log são um recurso PostgreSQL que pode ser usado com eficiência para otimizar inserções em massa. O PostgreSQL usa o log write-ahead (WAL). Ele fornece atomicidade e durabilidade, por padrão. Atomicidade, consistência, isolamento e durabilidade compõem as propriedades ACID. 

A inserção em uma tabela não registrada significa que o PostgreSQL faz inserções sem gravar no log de transações, que é uma operação de e/s. Como resultado, essas tabelas são consideravelmente mais rápidas do que as tabelas comuns.

Use as seguintes opções para criar uma tabela não registrada:
- Crie uma nova tabela não registrada usando a sintaxe `CREATE UNLOGGED TABLE <tableName>`.
- Converta uma tabela registrada existente em uma tabela não registrada usando a sintaxe `ALTER TABLE <tableName> SET UNLOGGED`.  

Para reverter o processo, use a sintaxe `ALTER TABLE <tableName> SET LOGGED`.

## <a name="unlogged-table-tradeoff"></a>Compensação de tabela não registrada
As tabelas não registradas não são seguras contra falhas. Uma tabela não registrada é truncada automaticamente após uma falha ou está sujeita a um desligamento não limpo. O conteúdo de uma tabela não registrada também não é replicado para servidores em espera. Todos os índices criados em uma tabela não registrada também são desconectados automaticamente. Depois que a operação de inserção for concluída, converta a tabela em log para que a inserção seja durável.

Algumas cargas de trabalho do cliente tiveram aproximadamente 15% a 20% de melhoria no desempenho quando tabelas não registradas foram usadas.

## <a name="next-steps"></a>Passos seguintes
Examine sua carga de trabalho para usos de dados transitórios e grandes inserções em massa. Consulte a seguinte documentação do PostgreSQL:
 
- [Comandos de criação de tabela SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)
