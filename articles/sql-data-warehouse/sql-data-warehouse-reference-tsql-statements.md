---
title: Instruções T-SQL - Azure SQL Data Warehouse | Documentos da Microsoft
description: Ligações para a documentação para instruções T-SQL suportado no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 06/13/2018
ms.author: twounder
ms.reviewer: igorstan
ms.openlocfilehash: 8922d4399f52937710f111c3c8ad4c9af91b6bb7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890105"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Instruções T-SQL suportadas no Azure SQL Data Warehouse
Ligações para a documentação para instruções T-SQL suportado no Azure SQL Data Warehouse.

## <a name="data-definition-language-ddl-statements"></a>Instruções de definição DDL (linguagem) de dados
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTERAR O ÍNDICE](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTERAR O PROCEDIMENTO](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [CREATE COLUMNSTORE INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [CRIAR UM ÂMBITO DE BASE DE DADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
* [CRIAR ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)
* [CRIAR FUNÇÃO](https://msdn.microsoft.com/library/mt203952.aspx)
* [CRIAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
* [CRIAR PROCEDIMENTO](https://msdn.microsoft.com/library/ms187926.aspx)
* [CRIAR ESQUEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [CRIAR VISTA](https://msdn.microsoft.com/library/ms187956.aspx)
* [CRIAR o CLASSIFICADOR de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql) (pré-visualização)
* [REMOVER ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/mt146367.aspx)
* [REMOVER O FORMATO DE FICHEIRO EXTERNO](https://msdn.microsoft.com/library/mt146379.aspx)
* [REMOVER TABELA EXTERNA](https://msdn.microsoft.com/library/mt130698.aspx)
* [REMOVER O ÍNDICE](https://msdn.microsoft.com/library/ms176118.aspx)
* [LARGAR O PROCEDIMENTO](https://msdn.microsoft.com/library/ms174969.aspx)
* [ESTATÍSTICAS DE SOLTAR](https://msdn.microsoft.com/library/ms175075.aspx)
* [REMOVER TABELA](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [VISTA DE LISTA](https://msdn.microsoft.com/library/ms173492.aspx)
* [CLASSIFICADOR de carga de trabalho de lista](/sql/t-sql/statements/drop-workload-classifier-transact-sql) (pré-visualização)
* [MUDAR O NOME](https://msdn.microsoft.com/library/mt631611.aspx)
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instruções de manipulação DML (linguagem) de dados
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [ATUALIZAÇÃO](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Comandos da consola de base de dados
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instruções de consulta
* [SELECIONE](https://msdn.microsoft.com/library/ms189499.aspx)
* [COM common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCETO e INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [EXPLAIN](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [Usando PIVOT e UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [AGRUPAR POR](https://msdn.microsoft.com/library/ms177673.aspx)
* [TER](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDENAR POR](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNIÃO](https://msdn.microsoft.com/library/ms180026.aspx)
* [ONDE](https://msdn.microsoft.com/library/ms188047.aspx)
* [PARTE SUPERIOR](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Condição de pesquisa](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subconsultas](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Declarações de segurança
* Permissões: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTERAR A AUTORIZAÇÃO](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTERAR O CERTIFICADO](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTERAR A CHAVE DE ENCRIPTAÇÃO DA BASE DE DADOS](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTERAR A CHAVE MESTRA](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTERAR FUNÇÃO](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTERAR UTILIZADOR](https://msdn.microsoft.com/library/ms176060.aspx)
* [CERTIFICADO DE CÓPIA DE SEGURANÇA](https://msdn.microsoft.com/library/ms178578.aspx)
* [FECHAR A CHAVE MESTRA](https://msdn.microsoft.com/library/ms188387.aspx)
* [CRIAR CERTIFICADO](https://msdn.microsoft.com/library/ms187798.aspx)
* [CRIAR CHAVE DE ENCRIPTAÇÃO DA BASE DE DADOS](https://msdn.microsoft.com/library/bb677241.aspx)
* [CRIAR INÍCIO DE SESSÃO](https://msdn.microsoft.com/library/ms189751.aspx)
* [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
* [CRIAR FUNÇÃO](https://msdn.microsoft.com/library/ms187936.aspx)
* [CRIAR UTILIZADOR](https://msdn.microsoft.com/library/ms173463.aspx)
* [REMOVER CERTIFICADO](https://msdn.microsoft.com/library/ms179906.aspx)
* [REMOVER A CHAVE DE ENCRIPTAÇÃO DA BASE DE DADOS](https://msdn.microsoft.com/library/bb630256.aspx)
* [REMOVER O INÍCIO DE SESSÃO](https://msdn.microsoft.com/library/ms188012.aspx)
* [REMOVER A CHAVE MESTRA](https://msdn.microsoft.com/library/ms180071.aspx)
* [REMOVER FUNÇÃO](https://msdn.microsoft.com/library/ms174988.aspx)
* [REMOVER UTILIZADOR](https://msdn.microsoft.com/library/ms189438.aspx)
* [CHAVE MESTRA DO OPEN](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações de referência, consulte [elementos de linguagem T-SQL no Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-language-elements.md), e [vistas de sistema no Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).
