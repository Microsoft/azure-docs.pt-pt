---
title: Migre a sua solução para o SQL Data Warehouse | Documentos da Microsoft
description: Orientações de migração para trazer a sua solução para a plataforma do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313026"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migre a sua solução para o Azure SQL Data Warehouse
Ver o que envolve a migração de uma solução de base de dados existente para o Azure SQL Data Warehouse. 

## <a name="profile-your-workload"></a>A carga de trabalho de perfil
Antes de migrar, deseja ser que determinadas SQL Data Warehouse é a solução certa para a sua carga de trabalho. SQL Data Warehouse é um sistema distribuído concebido para efetuar análises em dados de grande dimensão.  Migrar para o SQL Data Warehouse requer algumas alterações de design que não são demasiado difícil de entender, mas poderão demorar algum tempo para implementar. Se a sua empresa precisar de um armazém de dados de nível empresarial, são os benefícios que vale a pena o esforço. No entanto, se não precisar do poder do SQL Data Warehouse, é mais vantajoso usar o SQL Server ou base de dados do Azure SQL.

Considere utilizar o SQL Data Warehouse quando:
- Ter um ou mais Terabytes de dados
- Plano para executar análises em grandes quantidades de dados
- Necessita da capacidade de dimensionar a computação e armazenamento 
- Pretende reduzir os custos ao recursos de computação em pausa quando não precisar delas.

Não utilize o SQL Data Warehouse para cargas de trabalho (OLTP) operacionais que têm:
- Leituras e escritas de elevada frequência
- Seleciona um grande número de singleton
- Grandes volumes de inserções de linha única
- Linha por linha necessidades de processamento
- Formatos incompatíveis (JSON, XML)


## <a name="plan-the-migration"></a>Planear a migração

Depois de decidir migrar uma solução existente para o SQL Data Warehouse, é importante planear a migração antes de começar. 

É um objetivo de planeamento garantir que seus dados, seus esquemas de tabela e seu código são compatíveis com o SQL Data Warehouse. Existem algumas diferenças de compatibilidade para contornar entre o sistema atual e o SQL Data Warehouse. Além disso, a migrar grandes quantidades de dados para o Azure exige tempo. Um planejamento cuidadoso agiliza a obter os seus dados para o Azure. 

É outra meta do planejamento fazer ajustes de design para garantir que a solução tira partido do SQL Data Warehouse foi concebido para fornecer o desempenho de consulta elevado. Estruturar armazéns de dados para uma escala apresenta padrões e as abordagens tradicionais por isso, nem sempre são o melhor de design diferentes. Embora pode fazer alguns ajustes de design após a migração, as alterações mais cedo no processo de economizará tempo mais tarde.

Para efetuar uma migração com êxito, terá de migrar seus esquemas de tabela, seu código e os dados. Para obter orientações sobre esses tópicos de migração, consulte:

-  [Migrar seus esquemas](sql-data-warehouse-migrate-schema.md)
-  [Migrar o seu código](sql-data-warehouse-migrate-code.md)
-  [Migre os seus dados](sql-data-warehouse-migrate-data.md). 

<!--
## Perform the migration


## Deploy the solution


## Validate the migration

-->

## <a name="next-steps"></a>Passos Seguintes
O gato (equipe Consultiva para clientes) também tem algumas excelentes orientações de armazém de dados do SQL, que publicam por meio de blogs.  Dê uma olhada no seu artigo, [migrar dados para o Azure SQL Data Warehouse na prática] [ Migrating data to Azure SQL Data Warehouse in practice] para obter orientações adicionais sobre a migração.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/
