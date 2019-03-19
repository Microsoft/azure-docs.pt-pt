---
title: Notas de versão de armazém de dados SQL do Azure, Julho de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 123198b21122a23d81794db0a5ca2051b15ee2e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834206"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>O que há de novo no Azure SQL Data Warehouse? Julho de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Julho de 2018.

## <a name="lightning-fast-query-performance"></a>Desempenho de consulta rápida de palestras
[O Azure SQL Data Warehouse](https://aka.ms/sqldw) define novos parâmetros de comparação de desempenho com a introdução do acesso de dados de instantâneo que melhora as operações de shuffle. Acesso de dados de instantâneo reduz a sobrecarga para operações de movimento de dados através da utilização direta do servidor SQL para operações de dados nativos do SQL Server. A integração com o mecanismo do SQL Server diretamente para o movimento de dados significa que o SQL Data Warehouse está agora **67% mais rápida do que o Amazon Redshift** através de uma carga de trabalho derivam de norma da indústria bem reconhecida [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![O Azure SQL Data Warehouse é mais rápido e mais barato do que o Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>origem: [Relatório de analista investigação da Gigaom: Armazém de dados no parâmetro de comparação de Cloud](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Além do desempenho de tempo de execução, o [investigação da Gigaom](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) relatório medido também a proporção de desempenho por preço de quantificar o custo USD de cargas de trabalho específicas. O SQL Data Warehouse foi **pelo menos 23% mais barato** que Redshift para cargas de trabalho de 30 TB. Com capacidade de SQL Data Warehouse para dimensionar a computação elástica, bem como colocar em pausa e retomar a cargas de trabalho, os clientes pagam apenas quando o serviço está em utilização, reduzindo ainda mais os seus custos.
![O Azure SQL Data Warehouse é mais rápido e mais barato do que o Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>origem: [Relatório de analista investigação da Gigaom: Armazém de dados no parâmetro de comparação de Cloud](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Simultaneidade de consulta
O SQL Data Warehouse também garante que os dados estão acessíveis em suas organizações. A Microsoft aperfeiçoou o serviço suportar 128 consultas em simultâneo para que mais utilizadores, podem consultar o mesmo banco de dados e não são bloqueados pelo outros pedidos. Em comparação, o Amazon Redshift restringe as consultas em simultâneo máximas a 50, limitar o acesso de dados dentro da organização.

SQL Data Warehouse fornece estes ganhos de simultaneidade de consulta desempenho e a consulta, sem qualquer aumento do preço e modulares após a respetiva arquitetura exclusiva com armazenamento e computação desacoplados.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Granularidade mais fina para cruzada restaurações de região e o servidor
Agora, pode restaurar em regiões e servidores através de qualquer ponto de restauro em vez de selecionar geo redundante cópias de segurança que são executadas a cada 24 horas. Cruzada restauro de região e servidor são suportadas para ambos os pontos de restauração de automática pelo utilizador ou ativar a granularidade mais fina para proteção de dados adicionais. Com mais pontos de restauro disponíveis, pode ter a garantia de que o armazém de dados será logicamente consistente durante a restauração entre regiões.

![Restaurar Command - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![opções de restauro - Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Para obter mais informações, consulte a [Accelerated e pontos de restauro flexível](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) postagem de blog.

## <a name="20-minute-restorations"></a>Restaurações de 20 minutos
SQL Data Warehouse oferece agora o restauro de qualquer armazém de dados estiver **menos de 20 minutos** dentro da mesma região, independentemente do tamanho do banco de dados. O tempo de restauro aplica-se quer o restauro estiver no mesmo ou um servidor lógico diferente na mesma região. Além disso, o processo de instantâneo foi aperfeiçoado para reduzir a quantidade de tempo necessário para criar um ponto de restauro. Em lower níveis de desempenho (definições de DWU inferior), o aprimoramento é um *redução de 2x* para a criação do instantâneo no tempo.

Para obter mais informações, consulte a [Accelerated e pontos de restauro flexível](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) postagem de blog.

## <a name="custom-restoration-configurations"></a>Configurações de restauração personalizados
Agora pode alterar o nível de desempenho (DWU) ao restaurar no portal do Azure. Também pode restaurar um armazém de dados de geração 2 atualizado. Ao restaurar com uma instância de Gen 2, agora pode avaliar o impacto de geração 2 antes [atualizar o seu armazém de dados de geração 1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Configuração de restauração personalizados – armazém de dados SQL do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
O [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) procedimento armazenado, muitas vezes, é utilizado pelas ferramentas para obter metadados sobre parâmetros no batch do Transact-SQL. O procedimento retorna uma linha para cada parâmetro no batch com as informações de tipo deduzido para esse parâmetro. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

O conjunto de resultados inclui metadados sobre o `@id` parâmetro (resultados parciais mostrados)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
O [sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) procedimento armazenado atualiza os metadados para um objeto de base de dados se os metadados subjacentes tornou-se desatualizados devido a alterações os objetos subjacentes. Isto pode ocorrer se as tabelas de bases para uma visão são alteradas e a vista não tenha sido recriada. Isso poupa a etapa de remover e recriar objetos dependentes.

O exemplo abaixo mostra uma vista que se tornam obsoleta devido à alteração de tabela subjacentes. Observará que os dados estão corretos para a primeira alteração de coluna (1 a Mollie), mas o nome da coluna é inválido e a segunda coluna não está presente. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

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
