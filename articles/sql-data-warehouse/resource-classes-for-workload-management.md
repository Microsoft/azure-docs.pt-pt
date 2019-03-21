---
title: Classes de recursos para a gestão da carga de trabalho - Azure SQL Data Warehouse | Documentos da Microsoft
description: Orientações sobre o uso de classes de recursos para gerir a simultaneidade e recursos de cálculo para consultas no armazém de dados SQL do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 5b21c16a166a3a264156b7719be6a331e00e6e8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881372"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Gestão da carga de trabalho com classes de recursos no Azure SQL Data Warehouse

Orientações sobre o uso de classes de recursos para gerir a memória e simultaneidade para consultas no seu armazém de dados SQL do Azure.  

## <a name="what-is-workload-management"></a>O que é a gestão da carga de trabalho

Gestão da carga de trabalho fornece a capacidade de otimizar o desempenho geral de todas as consultas. Uma carga de trabalho bem ajustada executa consultas e operações de carregamento com eficiência, quer sejam computação intensiva ou e/s intensiva. O SQL Data Warehouse fornece capacidades de gestão da carga de trabalho para ambientes de vários utilizadores. Um armazém de dados não se destina a cargas de trabalho do multi-inquilinos.

A capacidade de desempenho de um armazém de dados é determinada através da [unidades de armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Para ver os limites de memória e simultaneidade para todos os perfis de desempenho, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md).
- Para ajustar a capacidade de desempenho, pode [aumentar ou reduzir verticalmente](quickstart-scale-compute-portal.md).

A capacidade de desempenho de uma consulta é determinada pela classe de recursos da consulta. O restante deste artigo explica o que são as classes de recursos e como ajustá-los.

## <a name="what-are-resource-classes"></a>Quais são classes de recursos

A capacidade de desempenho de uma consulta é determinada pela classe de recursos do utilizador.  Classes de recursos são previamente determinados limites de recursos no Azure SQL Data Warehouse que regem recursos de computação e simultaneidade para execução da consulta. Classes de recursos podem ajudar a gerir a carga de trabalho ao definir limites no número de consultas que são executadas em simultâneo e nos recursos de computação atribuídos a cada consulta.  Há uma compensação entre a memória e simultaneidade.

- Classes de recursos menores reduzem o máximo de memória por consulta, mas aumentam a simultaneidade.
- Classes de recursos maiores aumentam a memória máxima por consulta, mas reduzem a simultaneidade.

Existem dois tipos de classes de recursos:

- Classes de recursos estáticos, que são adequadas para a simultaneidade aumentada num tamanho de conjunto de dados que é fixo.
- Classes de recursos dinâmicos, que são adequadas para conjuntos de dados que estão aumentando de tamanho e tem melhor desempenho, como o nível de serviço for aumentado verticalmente.

Classes de recursos utilizam blocos de simultaneidade para medir o consumo de recursos.  [Ranhuras de simultaneidade](#concurrency-slots) são explicados neste artigo.

- Para ver a utilização de recursos para as classes de recursos, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md#concurrency-maximums).
- Para ajustar a classe de recursos, pode executar a consulta num utilizador diferente ou [alterar a classe de recursos do utilizador atual](#change-a-users-resource-class) associação.

### <a name="static-resource-classes"></a>Classes estáticas

A mesma quantidade de memória, independentemente do nível de desempenho atual, o que é medido em alocar a classes estáticas [unidades de armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md). Uma vez que as consultas a obter a alocação de memória mesmo, independentemente do nível de desempenho [aumentar horizontalmente o armazém de dados](quickstart-scale-compute-portal.md) permite consultas mais ser executado dentro de uma classe de recursos.  Classes de recursos estáticos são ideais se o volume de dados é conhecido e constante.

As classes estáticas são implementadas com estas funções de base de dados predefinido:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Classes de dinâmica de recursos

Classes de recursos dinâmica alocar uma variável quantidade de memória consoante o nível de serviço atual. Embora classes estáticas sejam benéficos para simultaneidade mais elevada e volumes de dados estáticos, classes de recursos dinâmicos estão mais adequados para uma variável ou cada vez maior quantidade de dados.  Ao aumentar verticalmente para um maior nível de serviço, as suas consultas obtém automaticamente mais memória.  

As classes de dinâmica de recursos são implementadas com estas funções de base de dados predefinido:

- smallrc
- mediumrc
- largerc
- xlargerc

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Classes de dinâmica de recursos de geração 2 são verdadeiramente dinâmicos

Quando compreendendo os detalhes das classes de recursos dinâmicos em Gen1, existem alguns detalhes que adicionam complexidade adicional para compreender o respetivo comportamento:

- A classe de recursos de smallrc funciona com um modelo de memória fixo, como uma classe de recursos estáticos.  Consultas de Smallrc dinamicamente não receber mais memória à medida que aumenta o nível de serviço.
- Como alterar os níveis de serviço, a simultaneidade de consulta disponível pode ir ou reduzir verticalmente.
- Níveis de serviços de dimensionamento não fornece uma alteração proporcional a memória alocada para as mesmas classes de recursos.

No **geração 2 apenas**, classes de recursos dinâmicos são verdadeiramente dinâmicas endereçamento pontos mencionados acima.  A nova regra é 3-10-22-70 para alocações de percentagem de memória para classes de recursos pequena-medium-grande-xlarge **independentemente do nível de serviço**.  A tabela a seguir tenha os detalhes consolidados da percentagens de alocação de memória e o número mínimo de consultas em simultâneo que são executados, independentemente do nível de serviço.

| Classe de Recursos | Percentagem de memória | Consultas em simultâneo de min |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3%                | 32                     |
| mediumrc       | 10%               | 10                     |
| largerc        | 22%               | 4                      |
| xlargerc       | 70%               | 1                      |

### <a name="default-resource-class"></a>Classe de recursos padrão

Por predefinição, cada utilizador é membro da classe de recursos dinâmicos **smallrc**.

A classe de recursos do administrador do serviço é fixa em smallrc e não pode ser alterada.  O administrador de serviço é o utilizador criado durante o processo de aprovisionamento.  O administrador de serviço neste contexto é o início de sessão especificado ao início de sessão"administrador de servidor" Criar uma nova instância de armazém de dados SQL com um novo servidor.

> [!NOTE]
> Os utilizadores ou grupos definidos como administrador do Active Directory também são administradores de serviço.
>
>

## <a name="resource-class-operations"></a>Operações de classe de recursos

Classes de recursos foram concebidas para melhorar o desempenho de atividades de gestão e a manipulação de dados. Consultas complexas também podem beneficiar-se em execução numa classe de recursos grandes. Por exemplo, o desempenho para associações de grandes das consultas e tipos podem aumentar quando a classe de recursos é grande o suficiente para permitir que a consulta seja executada na memória.

### <a name="operations-governed-by-resource-classes"></a>Operações de classes de recursos

Estas operações são regidas pelas classes de recursos:

- SELEÇÃO DE INSERT, UPDATE, DELETE
- SELECIONE (ao consultar tabelas de utilizador)
- ALTER INDEX - RECOMPILAÇÃO ou REORGANIZAÇÃO
- ALTERAR A RECRIAÇÃO DE TABELA
- CRIAR ÍNDICE
- CRIAR O ÍNDICE COLUMNSTORE EM CLUSTER
- CRIAR TABLE AS SELECT (CTAS)
- Carregamento de dados
- Operações de movimento de dados realizadas pelo serviço de movimento de dados (DMS)

> [!NOTE]  
> SELECIONE as instruções em vistas de gestão dinâmica (DMVs) ou outro sistema vistas não são regidas por qualquer um dos limites de simultaneidade. Pode monitorizar o sistema, independentemente do número de consultas em execução no mesmo.

### <a name="operations-not-governed-by-resource-classes"></a>Operações não regidas pelas classes de recursos

Algumas consultas são sempre executam na classe de recurso smallrc, apesar do utilizador é membro de uma classe de recursos maior. Estas consultas excluídas não são considerados o limite de simultaneidade. Por exemplo, se o limite de simultaneidade é 16, número de utilizadores que pode selecionar de vistas do sistema sem afetar os blocos de simultaneidade disponíveis.

As seguintes instruções são excluídas da classes de recursos e são sempre executados no smallrc:

-Criar ou remover a tabela - ALTER TABLE... COMUTADOR, DIVIDIR ou UNIR PARTIÇÃO-ALTER INDEX DESATIVAR - DROP INDEX-criar, UPDATE ou DROP STATISTICS-TRUNCATE TABLE-ALTER AUTHORIZATION-CREATE LOGIN-criar, alterar ou remover o utilizador-criar, alterar ou remover o procedimento – criar ou remover ver - inserir valores - SELECIONE vistas de sistema e DMVs-EXPLICAR - DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Ranhuras de simultaneidade

Ranhuras de simultaneidade são uma maneira conveniente de controlar os recursos disponíveis para a execução da consulta. Eles são como pedidos de suporte que comprar para reservar lugares num conjunto, como lugares são limitado. O número total de ranhuras de simultaneidade por armazém de dados é determinado pelo nível de serviço. Antes de uma consulta pode começar a executar, tem de ser capaz de reserva suficiente ranhuras de simultaneidade. Quando uma consulta estiver concluída, liberar seus blocos de simultaneidade.  

- Uma consulta em execução com 10 blocos de simultaneidade pode aceder a recursos de computação 5 vezes mais do que uma consulta em execução com 2 blocos de simultaneidade.
- Se cada consulta requer 10 ranhuras de simultaneidade e há 40 ranhuras de simultaneidade, apenas 4 consultas podem ser executadas simultaneamente.

Apenas as consultas de recursos regidas consumam ranhuras de simultaneidade. Consultas de sistema e algumas consultas simples não consumam qualquer ranhuras. O número exato de ranhuras de simultaneidade consumida é determinado por classe de recursos da consulta.

## <a name="view-the-resource-classes"></a>Ver as classes de recursos

Classes de recursos são implementados como funções de bases de dados previamente definidos. Existem dois tipos de classes de recursos: estáticas e dinâmicas. Para ver uma lista de classes de recursos, utilize a seguinte consulta:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Alterar a classe de recursos de um utilizador

Classes de recursos são implementados por atribuir utilizadores a funções de base de dados. Quando um utilizador executa uma consulta, a consulta é executada com a classe de recursos do utilizador. Por exemplo, se um utilizador for um membro da função de base de dados staticrc10, executam as consultas com pequenas quantidades de memória. Se um utilizador de base de dados é um membro das funções de base de dados xlargerc ou staticrc80, suas consultas executadas com grandes quantidades de memória.

Para aumentar a classe de recursos de um utilizador, utilize [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) para adicionar o utilizador a uma função de base de dados de uma classe de recursos grande.  O abaixo código adiciona um utilizador à função de base de dados de largerc.  Cada solicitação chegará 22% da memória do sistema.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para diminuir a classe de recursos, utilize [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Se 'loaduser' não é um membro ou quaisquer outras classes de recursos, eles irem para a classe de recursos de smallrc padrão com uma concessão de memória de 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Precedência de classe de recursos

Os utilizadores podem ser membros de várias classes de recursos. Quando um utilizador pertencer a mais do que uma classe de recursos:

- Classes de recursos dinâmicos têm precedência sobre classes de recursos estáticos. Por exemplo, se um utilizador for membro de mediumrc(dynamic) e staticrc80 (estático), consultas executadas com mediumrc.
- Classes de recursos maiores têm precedência sobre as classes de recursos mais pequenas. Por exemplo, se um utilizador for membro de mediumrc e largerc, consultas executadas com largerc. Da mesma forma, se um utilizador for membro de staticrc20 e statirc80, consultas executadas com staticrc80 alocações de recursos.

## <a name="recommendations"></a>Recomendações

É recomendável a criação de um utilizador que se dedica à execução de um tipo específico de consulta ou carregar a operação. Dar a esse utilizador uma classe de recursos permanente em vez de alterar a classe de recursos com frequência. Classes de recursos estáticos oferecem maior controle geral na carga de trabalho, portanto, sugerimos que utilize classes estáticas antes de considerar as classes de recursos dinâmicos.

### <a name="resource-classes-for-load-users"></a>Classes de recursos para os utilizadores de carga

`CREATE TABLE` índices columnstore utiliza em cluster por predefinição. A compressão de dados num columnstore índice é uma operação de memória intensiva e pressão de memória pode reduzir a qualidade do índice. Pressão da memória pode levar à necessidade de uma classe de recursos maior ao carregar dados. Para garantir a cargas tem memória suficiente, pode criar um utilizador designado para execução de cargas e atribua esse utilizador uma classe de recursos mais elevada.

A memória necessária para processar cargas de forma eficiente depende da natureza da tabela carregada e o tamanho de dados. Para obter mais informações sobre os requisitos de memória, consulte [maximizando a qualidade de rowgroup](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Depois de ter determinado o requisito de memória, escolha se pretende atribuir o utilizador de carga a uma classe de recursos estáticos ou dinâmicos.

- Utilize uma classe de recursos estáticos quando os requisitos de memória de tabela enquadram-se dentro de um intervalo específico. Executam cargas com memória adequada. Ao dimensionar o armazém de dados, as cargas não têm mais memória. Ao utilizar uma classe de recursos estáticos, as alocações de memória mantenha-se constante. Essa consistência conserva memória e permite que mais consultas a serem executadas em simultâneo. É recomendável que novas soluções utilizem as classes estáticas primeiro como eles fornecem maior controle.
- Utilize uma classe de recursos dinâmicos quando variam bastante, requisitos de memória de tabela. Cargas podem exigir mais memória do que a DWU atual ou o nível de cDWU fornece. Dimensionar o armazém de dados adiciona mais memória para operações de carga, que permite que cargas de realizar mais rapidamente.

### <a name="resource-classes-for-queries"></a>Classes de recursos para consultas

Algumas consultas são computação intensiva e algumas não estão.  

- Seleccione uma classe de recursos dinâmicos quando consultas complexas, mas não precisam de alta simultaneidade.  Por exemplo, a geração de relatórios de diários ou semanais é uma necessidade ocasional de recursos. Se os relatórios estão a processar grandes quantidades de dados, dimensionar o armazém de dados fornece mais memória a classe de recursos existentes do usuário.
- Escolha uma classe de recursos estáticos quando as expectativas de recurso variam ao longo do dia. Por exemplo, uma classe de recursos estáticos funciona bem quando o armazém de dados é consultado por muitas pessoas. Quando dimensionar o armazém de dados, a quantidade de memória alocada para o utilizador não é alterado. Por conseqüência, mais consultas podem ser executadas em paralelo no sistema.

Concessões de memória adequada dependem de vários fatores, como a quantidade de dados consultados, a natureza dos esquemas de tabela, e várias associações, selecionar e predicados de grupo. Em geral, alocar mais memória permite consultas para a conclusão, mas reduz a simultaneidade geral. Se a simultaneidade não é um problema, alocação excessiva da memória não afetar o débito.

Para otimizar o desempenho, use as classes de recursos diferente. Os seguintes secção nos dá um procedimento armazenado que o ajuda a descobrir a melhor classe de recursos.

## <a name="example-code-for-finding-the-best-resource-class"></a>Código de exemplo para localizar a classe de recursos melhor

Pode utilizar o seguinte procedimento armazenado especificado para [Gen1](#stored-procedure-definition-for-gen1) ou [Gen2](#stored-procedure-definition-for-gen2)-descobrir, memória e simultaneidade concedem por classe de recursos num determinado SLO e a classe de recursos melhor para a memória intensiva CCI operações na tabela CCI não-particionada numa classe de recursos específico:

Esta é a finalidade deste procedimento armazenado:

1. Para ver a concessão por classe de recursos num determinado SLO de memória e simultaneidade. Utilizador tem de fornecer NULL para o esquema e tablename, conforme mostrado neste exemplo.  
2. Para ver a classe de recursos melhor para as operações de CCI de memória intensiva (carga, a tabela de cópia, a recriação de índice, etc.) na tabela CCI não-particionada numa classe de recursos específico. O procedimento armazenado utiliza o esquema da tabela para obter informações sobre a concessão de memória necessária.

### <a name="dependencies--restrictions"></a>As dependências e restrições

- Este procedimento armazenado não é projetado para calcular o requisito de memória para uma tabela particionada cci.
- Este procedimento armazenado não usa os requisitos de memória em conta para a parte SELECIONE CTAS/INSERT-SELECIONAR e considera um SELECT.
- Este procedimento armazenado utiliza uma tabela temporária, o que está disponível na sessão em que este procedimento armazenado foi criado.
- Este procedimento armazenado depende as ofertas atuais (por exemplo, configuração de hardware, configuração DMS) e, se qualquer um que for alterado, em seguida, esse procedimento não funcionará corretamente.  
- Este procedimento armazenado depende de ofertas de limite de simultaneidade existentes e se eles alterar, em seguida, este procedimento armazenado não funcionará corretamente.  
- Este procedimento armazenado depende de ofertas de classe de recursos existentes e se eles alterar, em seguida, este procedimento armazenado não funcionará corretamente.  

>[!NOTE]  
>Se não estiver a obter o resultado depois de executar o procedimento armazenado com parâmetros fornecidos, em seguida, pode haver dois casos.
>
>1. O parâmetro de armazém de dados contém um valor inválido de SLO
>2. Em alternativa, não existe nenhuma classe de recurso correspondente para a operação de CCI na tabela.
>
>Por exemplo, em DW100, a concessão de memória mais elevada disponível é 400 MB, e se o esquema da tabela é amplo o bastante para ultrapassar o requisito de 400 MB.

### <a name="usage-example"></a>Exemplo de utilização

Sintaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: A fornecer um parâmetro nulo para extrair a DWU atual da BD do armazém de dados ou fornecer qualquer DWU suportado na forma de 'DW100'
2. @SCHEMA_NAME: Forneça um nome de esquema da tabela
3. @TABLE_NAME: Forneça um nome de tabela do interesse

Exemplos de executar este procedimento armazenado:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A instrução seguinte cria Table1, que é utilizada nos exemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition-for-gen1"></a>Definição do procedimento armazenado para geração 1

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT -FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
                     WHEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000))
                     ELSE 1
              END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

### <a name="stored-procedure-definition-for-gen2"></a>Definição do procedimento armazenado para geração 2

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
  SELECT @DWU = 'DW'+CAST(Nodes*CASE WHEN CPUVer>6 THEN 500 ELSE 100 END AS VARCHAR(10))+CASE WHEN CPUVer>6 THEN 'c' ELSE '' END
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), CPUVer=max(i.cpu_count)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE'
         )A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW1000c', 32, 40, 1, 4, 8, 28, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500c', 32, 60, 1, 6, 13, 42, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000c', 48, 80, 2, 8, 17, 56, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW2500c', 48, 100, 3, 10, 22, 70, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000c', 64, 120, 3, 12, 26, 84, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW5000c', 64, 200, 6, 20, 44, 140, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW6000c', 128, 240, 7, 24, 52, 168, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW7500c', 128, 300, 9, 30, 66, 210, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW10000c', 128, 400, 12, 40, 88, 280, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW15000c', 128, 600, 18, 60, 132, 420, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW30000c', 128, 1200, 36, 120, 264, 840, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-step"></a>Passo seguinte

Para obter mais informações sobre a gestão de utilizadores de base de dados e segurança, consulte [proteger uma base de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre classes de recursos maiores como podem melhorar a qualidade do índice columnstore em cluster, consulte [otimizações de memória para compressão columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
