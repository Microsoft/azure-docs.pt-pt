---
title: Ajuste de desempenho com cache de conjunto de resultados | Microsoft Docs
description: Descrição geral das funcionalidades
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: f6323501fc0078677c4c0e2cd0e43a15583df29b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513977"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ajuste de desempenho com cache de conjunto de resultados  
Quando o cache do conjunto de resultados estiver habilitado, o Azure SQL Data Warehouse armazenará em cache automaticamente os resultados da consulta no banco de dados do usuário para uso repetitivo.  Isso permite que as execuções de consulta subsequentes obtenham resultados diretamente do cache persistente, de modo que a recomputação não seja necessária.   O cache do conjunto de resultados melhora o desempenho da consulta e reduz o uso de recursos de computação.  Além disso, as consultas que usam resultados em cache definidos não usam nenhum slot de simultaneidade e, portanto, não contam com os limites de simultaneidade existentes. Por segurança, os usuários só poderão acessar os resultados armazenados em cache se tiverem as mesmas permissões de acesso a dados que os usuários que criam os resultados armazenados em cache.  

## <a name="key-commands"></a>Comandos de chave
[Ativar/desativar o cache do conjunto de resultados para um banco de dados de usuário](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Ativar/desativar o cache do conjunto de resultados para uma sessão](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Verificar o tamanho do conjunto de resultados em cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Limpar o cache](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>O que não está armazenado em cache  

Quando o cache do conjunto de resultados está ativado para um banco de dados, os resultados são armazenados em cache para todas as consultas até que o cache esteja cheio, exceto para essas consultas:
- Consultas que usam funções não determinísticas, como DateTime. Now ()
- Consultas que usam funções definidas pelo usuário
- Consultas retornando dados com tamanho de linha maior que 64 KB

Consultas com grandes conjuntos de resultados (por exemplo, > 1 milhão linhas) podem sofrer um desempenho mais lento durante a primeira execução quando o cache de resultados está sendo criado.

O cache do conjunto de resultados não dá suporte à segurança em nível de linha.  

## <a name="when-cached-results-are-used"></a>Quando os resultados armazenados em cache são usados

O conjunto de resultados em cache será reutilizado para uma consulta se todos os seguintes requisitos forem atendidos:
- O usuário que está executando a consulta tem acesso a todas as tabelas referenciadas na consulta.
- Há uma correspondência exata entre a nova consulta e a consulta anterior que gerou o cache do conjunto de resultados.
- Não há nenhuma alteração de esquema ou de dados nas tabelas em que o conjunto de resultados em cache foi gerado.

Execute este comando para verificar se uma consulta foi executada com um erro de cache de resultado ou perda. Se houver um impacto no cache, o result_cache_hit retornará 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Gerenciar resultados em cache 

O tamanho máximo do cache do conjunto de resultados é 1 TB por banco de dados.  Os resultados armazenados em cache são automaticamente invalidados quando os dados de consulta subjacentes são alterados.  

A remoção de cache é gerenciada pelo Azure SQL Data Warehouse automaticamente após este agendamento: 
- A cada 48 horas, se o conjunto de resultados não foi usado ou se foi invalidado. 
- Quando o cache do conjunto de resultados se aproximar do tamanho máximo.

Os usuários podem esvaziar manualmente todo o cache do conjunto de resultados usando uma destas opções: 
- Desativar o recurso de cache do conjunto de resultados para o banco de dados 
- Executar DBCC DROPRESULTSETCACHE enquanto estiver conectado ao banco de dados

Pausar um banco de dados não esvaziará o conjunto de resultados em cache.  

## <a name="next-steps"></a>Passos seguintes
Para mais sugestões de desenvolvimento, consulte [Descrição geral do desenvolvimento no SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 
