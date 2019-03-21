---
title: Notas de versão de armazém de dados SQL do Azure, Maio de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c17cb13bff0ea9eb3b0bb2caf5bb527fa3958428
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852615"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>O que há de novo no Azure SQL Data Warehouse? Maio de 2018 
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Maio de 2018. 

## <a name="gen-2-instances"></a>Instâncias de Gen 2
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) escalão do Azure SQL Data Warehouse de computação otimizada geração 2 define novos padrões de desempenho para o armazenamento de dados na cloud. Os clientes obtém agora até cinco vezes um melhor desempenho de consulta, quatro vezes mais simultaneidade e cinco vezes maior potência de computação em comparação com a geração atual. Agora que possa servir 128 consultas em simultâneo a partir de um único cluster, o mais alto de qualquer serviço de armazenamento de dados na cloud.

Consulte a [Turbine análises de cloud com o Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) anúncio do blogue de Rohan Kumar, Vice-presidente, dados do Azure.

## <a name="auto-statistics"></a>Estatísticas automáticas
As estatísticas são fundamentais para otimizar a geração de planos de consulta em modernos otimizadores baseado em custos, como o mecanismo do SQL Data Warehouse. Quando todas as consultas são conhecidas com antecedência, determinar o que os objetos de estatísticas precisam ser criados é uma tarefa alcançável. No entanto, quando o sistema é enfrentar ad hoc e consultas aleatórias que é típico para cargas de trabalho de armazenamento de dados, os administradores de sistema podem apresentar dificuldades para prever as estatísticas precisam ser criadas mais tempo e à esquerda para planos de execução de consulta potencialmente inferior ao ideal tempos de resposta da consulta. Uma forma de atenuar este problema é criar objetos de estatísticas em todas as colunas de tabela com antecedência. No entanto, esse processo é fornecido com uma penalidade como objetos de estatísticas precisam de ser mantidas durante o processo de carregamento, causando períodos de carregamento de tabela.

SQL Data Warehouse suporta agora a criação automática de objetos de estatísticas, fornecendo maior flexibilidade, produtividade e a facilidade de utilização para os administradores de sistema e os programadores, garantindo que o sistema continua a oferecer planos de execução de qualidade e melhor tempos de resposta.

Para ativar ou desativar a criação automática de estatísticas no SQL Data Warehouse, execute a seguinte instrução:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Como melhor prática e documentação de orientação, recomendamos a definição `AUTO_CREATE_STATISTICS` a opção de `ON`.

> [!NOTE]
> A criação automática de estatística está *ativada por predefinição* para todos os novos armazéns de dados.
>  

Consulte a [ALTER DATABASE definido Options](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) artigo para obter mais detalhes.

## <a name="rejected-row-support"></a>Suporte de linha de rejeitado
Os clientes utilizam frequentemente [PolyBase (tabelas externas) para carregar dados](design-elt-data-loading.md) para o SQL Data Warehouse devido ao elevado desempenho, a natureza de carregamento de dados em paralelo. O PolyBase é o modelo de carregamento padrão quando o carregamento de dados via [do Azure Data Factory](https://azure.com/adf) também. 

SQL Data Warehouse adiciona a capacidade de definir uma localização de linha rejeitadas por meio da `REJECTED_ROW_LOCATION` parâmetro com o [criar tabela externa](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) instrução. Após a execução de um [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) da tabela externa, todas as linhas que não foi possível carregar serão armazenadas num arquivo de perto a origem para investigação adicional. 

Consulte a [carregar com confiança com o SQL Data Warehouse PolyBase rejeitado linha localização](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blogue para obter mais detalhes sobre o comportamento de linha de rejeitado.

O exemplo seguinte mostra a nova sintaxe para especificar linhas rejeitadas.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTERAR A VISTA
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) permite que um usuário modificar uma vista criada anteriormente sem ter de criar/eliminar a vista e voltar a aplicar permissões. 

O exemplo seguinte modifica uma vista criada anteriormente.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
O [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) função devolve uma cadeia de caracteres resultante da concatenação de dois ou mais valores de uma forma de ponto-a-ponto. Ela separa os valores concatenados com o delimitador especificado no primeiro argumento. O `CONCAT_WS` função é útil para gerar a saída de comma-separated. o valor (CSV).

O exemplo seguinte mostra a concatenação de um conjunto de valores de int com uma vírgula.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
A instrução devolve o resultado seguinte:
```
result
---------
1,2,3
```
O exemplo seguinte mostra a concatenação de um conjunto de valores de tipo de dados mistos com uma vírgula.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
A instrução devolve o resultado seguinte:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
O [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) procedimento armazenado retorna informações sobre os tipos de dados suportados pelo ambiente atual do sistema. Que é frequentemente utilizada pelas ferramentas de ligação por meio de conexões ODBC para investigação de tipo de dados.

O exemplo seguinte obtém os detalhes para todos os tipos de dados suportados pelo SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>SELECIONE INTO com ORDER BY alteração de comportamento
Agora irá bloquear o SQL Data Warehouse `SELECT INTO` consultas que contêm um `ORDER BY` cláusula. Anteriormente, esta operação teria êxito pela primeira ordenação os dados na memória e, em seguida, inserir na tabela de destino, reordenar os dados de acordo com a forma de tabela.

### <a name="previous-behavior"></a>Comportamento anterior
A instrução a seguir funcionaria com sobrecarga de processamento adicional.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Comportamento atual
A instrução a seguir lançará um erro que indica a `ORDER BY` cláusula não é suportada num `SELECT INTO` instrução.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
A instrução de erro devolvida:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Definir PARSEONLY ' num Estado de consulta (alteração de comportamento)
Usando o `SET PARSEONLY ON` sintaxe permite que um usuário ter o motor do SQL Data Warehouse examinar a sintaxe de cada instrução T-SQL e retornar todas as mensagens de erro sem compilar ou executar a instrução. Anteriormente, no [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) vista de sistema, o estado para estas instruções permaneceria no `Running` estado. O `sys.dm_pdw_exec_requests` Vista agora irá devolver o estado como `Complete`.

## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse]. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Fórum do Stack Overflow]
* [Twitter]


[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
