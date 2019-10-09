---
title: Classes de recursos para gerenciamento de carga de trabalho no Azure SQL Data Warehouse | Microsoft Docs
description: Diretrizes para usar classes de recursos para gerenciar simultaneidade e recursos de computação para consultas no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 10/04/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 5ef95faf162a6774e42b7cf258515757fdc9c7eb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035073"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Gerenciamento de carga de trabalho com classes de recurso no Azure SQL Data Warehouse

Diretrizes para usar classes de recursos para gerenciar a memória e a simultaneidade para consultas em seu SQL Data Warehouse do Azure.  

## <a name="what-are-resource-classes"></a>O que são classes de recurso

A capacidade de desempenho de uma consulta é determinada pela classe de recurso do usuário.  As classes de recursos são limites de recursos predeterminados no Azure SQL Data Warehouse que regem os recursos de computação e a simultaneidade para a execução da consulta. As classes de recursos podem ajudá-lo a gerenciar sua carga de trabalho definindo limites no número de consultas executadas simultaneamente e nos recursos de computação atribuídos a cada consulta.  Há uma compensação entre a memória e a simultaneidade.

- Classes de recursos menores reduzem a memória máxima por consulta, mas aumentam a simultaneidade.
- Classes de recursos maiores aumentam a memória máxima por consulta, mas reduzem a simultaneidade.

Há dois tipos de classes de recurso:

- Classes de recursos estáticos, que são adequadas para aumentar a simultaneidade em um tamanho de conjunto de dados que é corrigido.
- Classes de recursos dinâmicos, que são adequadas para conjuntos de dados que estão crescendo em tamanho e precisam de aumento de desempenho conforme o nível de serviço é escalado verticalmente.

Classes de recurso usam slots de simultaneidade para medir o consumo de recursos.  Os [Slots de simultaneidade](#concurrency-slots) são explicados posteriormente neste artigo.

- Para exibir a utilização de recursos para as classes de recurso, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md#concurrency-maximums).
- Para ajustar a classe de recurso, você pode executar a consulta em um usuário diferente ou [alterar a associação de classe de recurso do usuário atual](#change-a-users-resource-class) .

### <a name="static-resource-classes"></a>Classes de recursos estáticos

As classes de recursos estáticos alocam a mesma quantidade de memória, independentemente do nível de desempenho atual, que é medido em [unidades data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Como as consultas obtêm a mesma alocação de memória independentemente do nível de desempenho, escalar horizontalmente [o data warehouse](quickstart-scale-compute-portal.md) permite que mais consultas sejam executadas dentro de uma classe de recurso.  As classes de recursos estáticos são ideais se o volume de dados é conhecido e constante.

As classes de recursos estáticos são implementadas com essas funções de banco de dados predefinidas:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Classes de recursos dinâmicos

As classes de recursos dinâmicos alocam uma quantidade variável de memória dependendo do nível de serviço atual. Embora as classes de recursos estáticos sejam úteis para maior simultaneidade e volumes de dados estáticos, as classes de recursos dinâmicos são mais adequadas para uma quantidade crescente ou variável de dados.  Quando você escala verticalmente para um nível de serviço maior, suas consultas obtêm automaticamente mais memória.  

As classes de recursos dinâmicos são implementadas com essas funções de banco de dados predefinidas:

- smallrc
- mediumrc
- largerc
- xlargerc

A alocação de memória para cada classe de recurso é a seguinte, **independentemente do nível de serviço**.  As consultas de simultaneidade mínima também são listadas.  Para alguns níveis de serviço, mais do que a simultaneidade mínima pode ser alcançada.

| Classe de Recursos | Percentual de memória | Mínimo de consultas simultâneas |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | Beta                | 32                     |
| mediumrc       | 10%               | 10                     |
| largerc        | 22%               | 4                      |
| xlargerc       | 70%               | 1                      |

### <a name="default-resource-class"></a>Classe de recurso padrão

Por padrão, cada usuário é um membro da classe de recurso dinâmico **smallrc**.

A classe de recurso do administrador de serviços é fixa em smallrc e não pode ser alterada.  O administrador de serviços é o usuário criado durante o processo de provisionamento.  O administrador de serviços nesse contexto é o logon especificado para o "logon de administrador do servidor" ao criar uma nova instância de SQL Data Warehouse com um novo servidor.

> [!NOTE]
> Os usuários ou grupos definidos como Active Directory admin também são administradores de serviço.
>
>

## <a name="resource-class-operations"></a>Operações de classe de recurso

As classes de recursos são projetadas para melhorar o desempenho de atividades de manipulação e gerenciamento de dados. Consultas complexas também podem se beneficiar da execução em uma classe de recursos grande. Por exemplo, o desempenho da consulta para grandes junções e classificações pode melhorar quando a classe de recurso é grande o suficiente para permitir que a consulta seja executada na memória.

### <a name="operations-governed-by-resource-classes"></a>Operações governadas por classes de recurso

Essas operações são governadas pelas classes de recurso:

- INSERIR – SELECIONAR, ATUALIZAR, EXCLUIR
- SELECT (ao consultar tabelas de usuário)
- ALTER INDEX-REBUILD ou reorganize
- ALTERAR RECOMPILAÇÃO DE TABELA
- CREATE INDEX
- CRIAR ÍNDICE COLUMNSTORE CLUSTERIZADO
- CREATE TABLE COMO SELECT (CTAS)
- Carregamento de dados
- Operações de movimentação de dados conduzidas pelo serviço de movimentação de dados (DMS)

> [!NOTE]  
> As instruções SELECT nas DMVs (exibições de gerenciamento dinâmico) ou outras exibições do sistema não são governadas por nenhum dos limites de simultaneidade. Você pode monitorar o sistema independentemente do número de consultas em execução nele.

### <a name="operations-not-governed-by-resource-classes"></a>Operações não governadas por classes de recurso

Algumas consultas são sempre executadas na classe de recurso smallrc, embora o usuário seja membro de uma classe de recurso maior. Essas consultas isentas não contam para o limite de simultaneidade. Por exemplo, se o limite de simultaneidade for 16, muitos usuários poderão selecionar de exibições do sistema sem afetar os slots de simultaneidade disponíveis.

As instruções a seguir são isentas das classes de recurso e sempre são executadas no smallrc:

- CRIAR ou descartar tabela
- ALTERAR TABELA... COMUTAdor, divisão ou MESCLAr partição
- ALTER INDEX DISABLE
- DROP INDEX
- CRIAR, atualizar ou descartar estatísticas
- TRUNCATE TABLE
- ALTERAR AUTORIZAÇÃO
- CRIAR LOGON
- CRIAR, alterar ou descartar usuário
- PROCEDIMENTO CREATE, ALTER ou DROP
- CRIAR ou descartar exibição
- INSERIR VALORES
- SELECIONAR de exibições do sistema e DMVs
- EXPLICO
- DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Slots de simultaneidade

Os slots de simultaneidade são uma maneira conveniente de acompanhar os recursos disponíveis para a execução da consulta. Eles são como tíquetes que você compra para reservar estações em um concerto, pois a assentos é limitada. O número total de Slots de simultaneidade por data warehouse é determinado pelo nível de serviço. Antes que uma consulta possa começar a ser executada, ela deve ser capaz de reservar slots de simultaneidade suficientes. Quando uma consulta é concluída, ela libera seus slots de simultaneidade.  

- Uma consulta em execução com 10 slots de simultaneidade pode acessar 5 vezes mais recursos de computação do que uma consulta em execução com 2 slots de simultaneidade.
- Se cada consulta exigir 10 slots de simultaneidade e houver 40 slots de simultaneidade, somente quatro consultas poderão ser executadas simultaneamente.

Somente consultas controladas por recursos consomem slots de simultaneidade. As consultas do sistema e algumas consultas triviais não consomem nenhum slot. O número exato de Slots de simultaneidade consumidos é determinado pela classe de recurso da consulta.

## <a name="view-the-resource-classes"></a>Exibir as classes de recurso

As classes de recurso são implementadas como funções de banco de dados predefinidas. Há dois tipos de classes de recurso: dinâmica e estática. Para exibir uma lista das classes de recurso, use a seguinte consulta:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Alterar a classe de recurso de um usuário

As classes de recurso são implementadas atribuindo usuários a funções de banco de dados. Quando um usuário executa uma consulta, a consulta é executada com a classe de recurso do usuário. Por exemplo, se um usuário for membro da função de banco de dados staticrc10, suas consultas serão executadas com pequenas quantidades de memória. Se um usuário de banco de dados for membro das funções de banco de dados xlargerc ou staticrc80, suas consultas serão executadas com grandes quantidades de memória.

Para aumentar a classe de recurso de um usuário, use [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) para adicionar o usuário a uma função de banco de dados de uma classe de recurso grande.  O código abaixo adiciona um usuário à função de banco de dados largerc.  Cada solicitação Obtém 22% da memória do sistema.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para diminuir a classe de recurso, use [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Se ' loaduser ' não for um membro ou outras classes de recurso, eles entrarão na classe de recurso smallrc padrão com uma concessão de memória de 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Precedência de classe de recurso

Os usuários podem ser membros de várias classes de recursos. Quando um usuário pertence a mais de uma classe de recurso:

- As classes de recursos dinâmicos têm precedência sobre as classes de recursos estáticos. Por exemplo, se um usuário for membro de mediumrc (dinâmico) e staticrc80 (estático), as consultas serão executadas com mediumrc.
- Classes de recursos maiores têm precedência sobre classes de recursos menores. Por exemplo, se um usuário for membro de mediumrc e largerc, as consultas serão executadas com largerc. Da mesma forma, se um usuário for membro de staticrc20 e statirc80, as consultas serão executadas com alocações de recursos staticrc80.

## <a name="recommendations"></a>Recomendações

É recomendável criar um usuário dedicado à execução de um tipo específico de consulta ou operação de carregamento. Dê a esse usuário uma classe de recurso permanente em vez de alterar a classe de recurso com frequência. As classes de recursos estáticos oferecem maior controle geral na carga de trabalho, então sugerimos o uso de classes de recursos estáticos antes de considerar as classes de recursos dinâmicos.

### <a name="resource-classes-for-load-users"></a>Classes de recursos para usuários de carga

o `CREATE TABLE` usa índices columnstore clusterizados por padrão. A compactação de dados em um índice columnstore é uma operação com uso intensivo de memória e a pressão de memória pode reduzir a qualidade do índice. A pressão de memória pode levar à necessidade de uma classe de recursos mais alta ao carregar dados. Para garantir que as cargas tenham memória suficiente, você pode criar um usuário designado para executar cargas e atribuir esse usuário a uma classe de recurso superior.

A memória necessária para processar cargas com eficiência depende da natureza da tabela carregada e do tamanho dos dados. Para obter mais informações sobre os requisitos de memória, consulte [maximizando a qualidade do rowgroup](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Depois de determinar o requisito de memória, escolha se deseja atribuir o usuário de carregamento a uma classe de recurso estático ou dinâmico.

- Use uma classe de recurso estático quando os requisitos de memória de tabela estiverem dentro de um intervalo específico. As cargas são executadas com a memória apropriada. Quando você dimensiona a data warehouse, as cargas não precisam de mais memória. Usando uma classe de recurso estático, as alocações de memória permanecem constantes. Essa consistência conserva a memória e permite que mais consultas sejam executadas simultaneamente. Recomendamos que as novas soluções usem as classes de recursos estáticos primeiro, pois elas fornecem maior controle.
- Use uma classe de recursos dinâmicos quando os requisitos de memória de tabela variarem muito. As cargas podem exigir mais memória do que o DWU atual ou o nível de cDWU fornece. O dimensionamento da data warehouse adiciona mais memória às operações de carregamento, o que permite que as cargas sejam executadas mais rapidamente.

### <a name="resource-classes-for-queries"></a>Classes de recurso para consultas

Algumas consultas são de computação intensiva e outras não.  

- Escolha uma classe de recurso dinâmico quando as consultas forem complexas, mas não precisarem de alta simultaneidade.  Por exemplo, a geração de relatórios diários ou semanais é uma necessidade ocasional de recursos. Se os relatórios estiverem processando grandes quantidades de dados, dimensionar o data warehouse fornecerá mais memória para a classe de recurso existente do usuário.
- Escolha uma classe de recurso estático quando as expectativas de recursos variarem ao longo do dia. Por exemplo, uma classe de recurso estático funciona bem quando a data warehouse é consultada por muitas pessoas. Ao dimensionar o data warehouse, a quantidade de memória alocada para o usuário não é alterada. Consequentemente, mais consultas podem ser executadas em paralelo no sistema.

As concessões de memória adequadas dependem de vários fatores, como a quantidade de dados consultados, a natureza dos esquemas de tabela e vários predicados de junções, de seleção e de grupo. Em geral, a alocação de mais memória permite que as consultas sejam concluídas mais rapidamente, mas reduz a simultaneidade geral. Se a simultaneidade não for um problema, a alocação excessiva de memória não danificará a taxa de transferência.

Para ajustar o desempenho, use classes de recurso diferentes. A próxima seção fornece um procedimento armazenado que ajuda você a descobrir a melhor classe de recurso.

## <a name="example-code-for-finding-the-best-resource-class"></a>Código de exemplo para localizar a melhor classe de recurso

Você pode usar o seguinte procedimento armazenado especificado para descobrir a simultaneidade e a concessão de memória por classe de recurso em um determinado SLO e a melhor classe de recursos para operações de CCI com uso intensivo de memória em uma tabela CCI não particionada em uma determinada classe de recurso:

Esta é a finalidade deste procedimento armazenado:

1. Para ver a simultaneidade e a concessão de memória por classe de recurso em um determinado SLO. O usuário precisa fornecer NULL para Schema e TableName, conforme mostrado neste exemplo.  
2. Para ver a melhor classe de recurso para as operações de CCI com uso intensivo de memória (carregar, copiar tabela, recompilar índice, etc.) na tabela CCI não particionada em uma determinada classe de recurso. O procedimento armazenado usa o esquema de tabela para descobrir a concessão de memória necessária.

### <a name="dependencies--restrictions"></a>Dependências & restrições

- Esse procedimento armazenado não foi projetado para calcular o requisito de memória para uma tabela CCI particionada.
- Esse procedimento armazenado não leva em conta os requisitos de memória para a parte SELECT de CTAS/INSERT-SELECT e pressupõe que é uma seleção.
- Esse procedimento armazenado usa uma tabela temporária, que está disponível na sessão em que esse procedimento armazenado foi criado.
- Esse procedimento armazenado depende das ofertas atuais (por exemplo, configuração de hardware, config do DMS) e, se qualquer uma dessas alterações for alterada, esse procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende das ofertas de limite de simultaneidade existentes e, se essa alteração for alterada, esse procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende das ofertas de classe de recurso existentes e, se essas alterações forem alteradas, esse procedimento armazenado não funcionará corretamente.  

>[!NOTE]  
>Se você não estiver obtendo a saída após executar o procedimento armazenado com parâmetros fornecidos, pode haver dois casos.
>
>1. O parâmetro DW contém um valor de SLO inválido
>2. Ou, não há nenhuma classe de recurso correspondente para a operação CCI na tabela.
>
>Por exemplo, em DW100c, a concessão de memória mais alta disponível é de 1 GB e se o esquema de tabela for grande o suficiente para cruzar o requisito de 1 GB.

### <a name="usage-example"></a>Exemplo de uso

Sintaxe  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: forneça um parâmetro nulo para extrair o DWU atual do banco de BD DW ou fornecer qualquer DWU com suporte no formato ' DW100c '
2. @SCHEMA_NAME: forneça um nome de esquema da tabela
3. @TABLE_NAME: forneça um nome de tabela de interesse

Exemplos que executam esse procedimento armazenado:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A instrução a seguir cria Table1 que é usado nos exemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definição do procedimento armazenado

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

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
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
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200c', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300c', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400c', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500c', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000c', 32, 40, 1, 4, 8, 28, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500c', 32, 60, 1, 6, 13, 42, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000c', 48, 80, 2, 8, 17, 56, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW2500c', 48, 100, 3, 10, 22, 70, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000c', 64, 120, 3, 12, 26, 84, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW5000c', 64, 200, 6, 20, 44, 140, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW6000c', 128, 240, 7, 24, 52, 168, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW7500c', 128, 300, 9, 30, 66, 210, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW10000c', 128, 400, 12, 40, 88, 280, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW15000c', 128, 600, 18, 60, 132, 420, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW30000c', 128, 1200, 36, 120, 264, 840, 1, 2, 4, 8, 16, 32, 64, 128 
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
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
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

Para obter mais informações sobre como gerenciar usuários e segurança de banco de dados, consulte [proteger um banco de dados no SQL data warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como as classes de recursos maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [otimizações de memória para compactação columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
