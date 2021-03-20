---
title: Otimizar inserções a granel - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve como pode otimizar as operações de inserção a granel numa Base de Dados Azure para PostgreSQL - Servidor Único.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86116289"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Otimize inserções a granel e utilize dados transitórios numa Base de Dados Azure para PostgreSQL - Servidor Único 
Este artigo descreve como pode otimizar operações de inserção a granel e utilizar dados transitórios numa Base de Dados Azure para servidor PostgreSQL.

## <a name="use-unlogged-tables"></a>Use mesas não ensoçadas
Se tiver operações de carga de trabalho que envolvam dados transitórios ou que insira grandes conjuntos de dados a granel, considere utilizar tabelas nãologadas.

As tabelas nãologadas são uma funcionalidade PostgreSQL que pode ser usada eficazmente para otimizar inserções a granel. PostgreSQL utiliza Write-Ahead Registo (WAL). Proporcionaatidade e durabilidade, por defeito. Aatóatriedade, a consistência, o isolamento e a durabilidade compõem as propriedades acid. 

Inserir numa tabela nãologada significa que o PostgreSQL insere-se sem escrever no registo de transações, que por si só é uma operação de E/S. Como resultado, estas tabelas são consideravelmente mais rápidas do que as tabelas normais.

Utilize as seguintes opções para criar uma tabela não assoada:
- Crie uma nova tabela nãologada utilizando a sintaxe `CREATE UNLOGGED TABLE <tableName>` .
- Converta uma tabela registada existente numa mesa não assoante utilizando a sintaxe `ALTER TABLE <tableName> SET UNLOGGED` .  

Para inverter o processo, utilize a sintaxe `ALTER TABLE <tableName> SET LOGGED` .

## <a name="unlogged-table-tradeoff"></a>Troca de mesa sem acordo
Mesas não ensopídas não são seguras. Uma mesa não obstruída é automaticamente truncada após uma colisão ou sujeita a uma paragem imaculada. O conteúdo de uma tabela nãologada também não é replicado para servidores de espera. Quaisquer índices criados numa tabela nãologada também são automaticamente deslogados. Após a conclusão da operação de inserção, converta a tabela para registar de modo a que a inserção seja durável.

Algumas cargas de trabalho dos clientes sofreram aproximadamente uma melhoria de desempenho de 15% a 20%, quando foram utilizadas mesas nãologadas.

## <a name="next-steps"></a>Passos seguintes
Reveja a sua carga de trabalho para utilização de dados transitórios e grandes inserções a granel. Consulte a seguinte documentação PostgreSQL:
 
- [Criar comandos SQL de mesa](https://www.postgresql.org/docs/current/static/sql-createtable.html)
