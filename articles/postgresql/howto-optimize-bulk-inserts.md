---
title: Otimizar inserções a granel - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode otimizar as operações de inserção a granel numa Base de Dados Azure para PostgreSQL - Servidor Único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4c4bac16917be0064ebb111328753d378d462a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770140"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Otimizar inserções a granel e utilizar dados transitórios numa Base de Dados Azure para PostgreSQL - Servidor Único 
Este artigo descreve como pode otimizar as operações de inserção a granel e utilizar dados transitórios numa Base de Dados Azure para servidor PostgreSQL.

## <a name="use-unlogged-tables"></a>Use mesas não ligadas
Se tiver operações de carga de trabalho que envolvam dados transitórios ou que insiram grandes conjuntos de dados a granel, considere utilizar tabelas não ligadas.

As tabelas não logged são uma função PostgreSQL que pode ser usada eficazmente para otimizar as inserções a granel. PostgreSQL usa Write-Ahead Logging (WAL). Proporciona atómico e durabilidade, por defeito. Atómico, consistência, isolamento e durabilidade compõem as propriedades acides. 

Inserir numa tabela não logged significa que o PostgreSQL insere sem escrever no registo de transações, que por si só é uma operação de I/S. Como resultado, estas tabelas são consideravelmente mais rápidas do que as tabelas comuns.

Utilize as seguintes opções para criar uma tabela não ligada:
- Crie uma nova mesa não logged `CREATE UNLOGGED TABLE <tableName>`utilizando a sintaxe .
- Converter uma mesa de registo existente numa mesa não logged utilizando a sintaxe `ALTER TABLE <tableName> SET UNLOGGED`.  

Para inverter o processo, utilize `ALTER TABLE <tableName> SET LOGGED`a sintaxe.

## <a name="unlogged-table-tradeoff"></a>Troca de mesa não registada
Mesas não ligadas não são seguras para acidentes. Uma mesa não alocada é automaticamente truncada após um acidente ou sujeita a uma paragem imaculada. O conteúdo de uma mesa não ligada também não é replicado para servidores de espera. Quaisquer índices criados numa tabela não logged também são automaticamente desconectados. Após a operação de inserção estar concluída, converta a tabela em registo de modo a que a inserção seja durável.

Algumas cargas de trabalho dos clientes sofreram aproximadamente uma melhoria de desempenho de 15% a 20% quando foram utilizadas tabelas não registadas.

## <a name="next-steps"></a>Passos seguintes
Reveja a sua carga de trabalho para utilização de dados transitórios e grandes inserções a granel. Consulte a seguinte documentação postgresqL:
 
- [Criar comandos SQL de mesa](https://www.postgresql.org/docs/current/static/sql-createtable.html)
