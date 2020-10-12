---
title: Classes de recursos para gestão da carga de trabalho
description: Orientação para o uso de classes de recursos para gerir a concordância e calcular recursos para consultas em Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a557d4045b18b5c0ff71b3e47f0c189028702863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289536"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Gestão da carga de trabalho com classes de recursos em Azure Synapse Analytics

Orientação para o uso de classes de recursos para gerir a memória e a concordância para consultas de piscina synapse SQL em Azure Synapse.  

## <a name="what-are-resource-classes"></a>O que são classes de recursos

A capacidade de desempenho de uma consulta é determinada pela classe de recursos do utilizador.  As classes de recursos são limites de recursos pré-determinados na piscina Sinaapse SQL que regem os recursos computativos e a concuência para a execução de consultas. As classes de recursos podem ajudá-lo a configurar recursos para as suas consultas, estabelecendo limites no número de consultas que funcionam simultaneamente e nos recursos de computação atribuídos a cada consulta.  Há uma troca entre memória e concordância.

- Classes de recursos mais pequenas reduzem a memória máxima por consulta, mas aumentam a concordância.
- As classes de recursos maiores aumentam a memória máxima por consulta, mas reduzem a conúnva.

Existem dois tipos de classes de recursos:

- Classes de recursos estáticos, que são adequadas para uma maior concordância num conjunto de dados que é fixado.
- Classes dinâmicas de recursos, que são adequadas para conjuntos de dados que estão crescendo em tamanho e precisam de um maior desempenho à medida que o nível de serviço é aumentado.

As classes de recursos utilizam slots de concordância para medir o consumo de recursos.  [As faixas horárias de concurrency](#concurrency-slots) são explicadas mais tarde neste artigo.

- Para visualizar a utilização dos recursos para as classes de recursos, consulte os [limites de Memória e conúcência](memory-concurrency-limits.md).
- Para ajustar a classe de recursos, pode executar a consulta sob um utilizador diferente ou alterar a adesão [da classe de recursos do utilizador atual.](#change-a-users-resource-class)

### <a name="static-resource-classes"></a>Classes de recursos estáticos

As classes de recursos estáticos atribuem a mesma quantidade de memória, independentemente do nível de desempenho atual, que é medido em [unidades de armazém de dados.](what-is-a-data-warehouse-unit-dwu-cdwu.md) Uma vez que as consultas obtêm a mesma alocação de memória independentemente do nível de desempenho, [a escala do armazém de dados](quickstart-scale-compute-portal.md) permite que mais consultas decorram dentro de uma classe de recursos.  As classes de recursos estáticos são ideais se o volume de dados for conhecido e constante.

As classes de recursos estáticos são implementadas com estas funções de base de dados pré-definidas:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- estáticarc70
- estáticarc80

### <a name="dynamic-resource-classes"></a>Classes dinâmicas de recursos

As Classes dinâmicas de Recursos atribuem uma quantidade variável de memória dependendo do nível de serviço atual. Embora as classes de recursos estáticos sejam benéficas para volumes de dados mais concunciosos e estáticos, as classes dinâmicas de recursos são mais adequadas para uma quantidade crescente ou variável de dados.  Quando se escala para um nível de serviço maior, as suas consultas obtêm automaticamente mais memória.  

As classes dinâmicas de recursos são implementadas com estas funções de base de dados pré-definidas:

- smallrc
- mediumrc
- maior
- xlargerc

A atribuição de memória para cada classe de recursos é a seguinte.

| Nível de Serviço  | smallrc           | mediumrc               | maior                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6,25%             | 10%                    | 22%                    | 70%                    |
| DW500c         | 5%                | 10%                    | 22%                    | 70%                    |
| DW1000c para<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |

### <a name="default-resource-class"></a>Classe de recursos predefinidos

Por predefinição, cada utilizador é membro da classe de recursos **dinâmicos smallrc**.

A classe de recursos do administrador de serviço é fixada na smallrc e não pode ser alterada.  O administrador de serviço é o utilizador criado durante o processo de provisionamento.  O administrador de serviço neste contexto é o login especificado para o "login de administração do servidor" ao criar uma nova piscina Sinapse SQL com um novo servidor.

> [!NOTE]
> Os utilizadores ou grupos definidos como administrador de Diretório Ativo são também administradores de serviços.
>
>

## <a name="resource-class-operations"></a>Operações de classe de recursos

As classes de recursos destinam-se a melhorar o desempenho das atividades de gestão e manipulação de dados. Consultas complexas também podem beneficiar de correr sob uma grande classe de recursos. Por exemplo, o desempenho de consulta para grandes junções e tipos pode melhorar quando a classe de recursos é grande o suficiente para permitir que a consulta seja executada na memória.

### <a name="operations-governed-by-resource-classes"></a>Operações regidas por classes de recursos

Estas operações são regidas por classes de recursos:

- INSERIR-SELECT, ATUALIZAR, EXCLUIR
- SELECIONE (ao consultar as tabelas de utilizadores)
- ÍNDICE DE ALTERAÇÃO - RECONSTRUIR OU REORGANIZAR
- ALTERAR A RECONSTRUÇÃO DA TABELA
- CREATE INDEX
- CRIAR ÍNDICE DE LOJA DE COLUNAS AGRUPADAS
- CRIAR TABELA COMO SELECT (CTAS)
- Carregamento de dados
- Operações de movimento de dados conduzidas pelo Serviço de Movimento de Dados (DMS)

> [!NOTE]  
> As declarações selecionadas sobre pontos de vista dinâmicos de gestão (DMVs) ou outras visões do sistema não são regidas por nenhum dos limites de concordância. Pode monitorizar o sistema independentemente do número de consultas que o executam.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Operações não regidas por classes de recursos

Algumas consultas são sempre executadas na classe de recursos smallrc, mesmo que o utilizador seja membro de uma classe de recursos maior. Estas consultas isentas não contam para o limite de concordância. Por exemplo, se o limite de concordância for de 16, muitos utilizadores podem selecionar a partir de pontos de vista do sistema sem afetar as faixas horárias disponíveis.

As seguintes declarações estão isentas de classes de recursos e são sempre executadas em smallrc:

- CRIAR ou LARGAR
- ALTER TABELA ... COMUTAÇÃO, DIVISÃO ou FUSÃO PARTIÇÃO
- ALTERAR DESATIVAÇÃO DO ÍNDICE
- DROP INDEX
- CRIAR, ATUALIZAR OU DEIXAR CAIR ESTATÍSTICAS
- TABELA TRUNCADA
- ALTERAR AUTORIZAÇÃO
- CRIAR LOGIN
- CRIAR, ALTERAR ou DEIXAR CAIR O UTILIZADOR
- CRIAR, ALTERAR OU LARGAR PROCEDIMENTO
- CRIAR ou DEIXAR VER
- INSERIR VALORES
- SELECIONE a partir de vistas do sistema e DMVs
- EXPLICAR
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Slots de concurrency

As faixas horárias de concurrency são uma forma conveniente de rastrear os recursos disponíveis para a execução de consultas. São como bilhetes que se compram para reservar lugares num concerto porque os lugares são limitados. O número total de faixas horárias por armazém de dados é determinado pelo nível de serviço. Antes de uma consulta começar a executar, deve ser capaz de reservar slots de conuncy suficientes. Quando uma consulta termina, liberta as suas ranhuras de concordância.  

- Uma consulta em execução com 10 slots de concuncy pode aceder 5 vezes mais recursos computacional do que uma consulta em execução com 2 slots de conuncy.
- Se cada consulta requer 10 slots de concordância e existem 40 slots de conuncy, então apenas 4 consultas podem ser executadas simultaneamente.

Apenas as consultas regeradas por recursos consomem faixas horárias de conuncy. Consultas de sistema e algumas consultas triviais não consomem slots. O número exato de faixas horárias de conuncy consumidas é determinado pela classe de recursos da consulta.

## <a name="view-the-resource-classes"></a>Ver as classes de recursos

As classes de recursos são implementadas como funções de base de dados pré-definidas. Existem dois tipos de classes de recursos: dinâmica e estática. Para visualizar uma lista das classes de recursos, utilize a seguinte consulta:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Alterar a classe de recursos de um utilizador

As classes de recursos são implementadas atribuindo os utilizadores a funções de base de dados. Quando um utilizador executa uma consulta, a consulta é executado com a classe de recursos do utilizador. Por exemplo, se um utilizador é membro da função de base de dados staticrc10, as suas consultas são executadas com pequenas quantidades de memória. Se um utilizador de base de dados for membro das funções de base de dados xlargerc ou staticrc80, as suas consultas são executadas com grandes quantidades de memória.

Para aumentar a classe de recursos de um utilizador, utilize [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para adicionar o utilizador a uma função de base de dados de uma grande classe de recursos.  O código abaixo adiciona um utilizador à função de base de dados maior.  Cada pedido recebe 22% da memória do sistema.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para diminuir a classe de recursos, utilize [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Se o 'loaduser' não for membro ou qualquer outra classe de recursos, eles vão para a classe de recursos de pequeno por defeito com uma bolsa de memória de 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Precedência da classe de recursos

Os utilizadores podem ser membros de várias classes de recursos. Quando um utilizador pertence a mais de uma classe de recursos:

- As classes dinâmicas de recursos têm precedência sobre as classes de recursos estáticos. Por exemplo, se um utilizador for membro tanto do mediumrc (dinâmico) como do staticrc80 (estático), as consultas são executadas com o mediumrc.
- As classes de recursos maiores têm precedência sobre classes de recursos mais pequenas. Por exemplo, se um utilizador é membro da mediumrc e do biggerc, as consultas são executadas com maiores. Da mesma forma, se um utilizador for membro tanto da staticrc20 como do statirc80, as consultas são executadas com alocações de recursos estáticarc80.

## <a name="recommendations"></a>Recomendações

>[!NOTE]
>Considere aproveitar as capacidades de gestão da carga de trabalho[(isolamento da carga de trabalho,](sql-data-warehouse-workload-isolation.md) [classificação](sql-data-warehouse-workload-classification.md) e [importância)](sql-data-warehouse-workload-importance.md)para um maior controlo sobre a sua carga de trabalho e desempenho previsível.  
>
>

Recomendamos a criação de um utilizador que se dedique a executar um tipo específico de consulta ou operação de carga. Dê a esse utilizador uma classe de recursos permanente em vez de alterar a classe de recursos com frequência. As classes de recursos estáticos oferecem um maior controlo geral sobre a carga de trabalho, pelo que sugerimos a utilização de classes de recursos estáticos antes de considerar classes dinâmicas de recursos.

### <a name="resource-classes-for-load-users"></a>Classes de recursos para utilizadores de carga

`CREATE TABLE` utiliza índices de loja de colunas agrupados por padrão. Comprimir dados num índice de colunas é uma operação intensiva de memória, e a pressão da memória pode reduzir a qualidade do índice. A pressão da memória pode levar a uma classe de recursos mais elevada ao carregar dados. Para garantir que as cargas têm memória suficiente, pode criar um utilizador designado para executar cargas e atribuir esse utilizador a uma classe de recursos mais elevada.

A memória necessária para processar cargas de forma eficiente depende da natureza da tabela carregada e do tamanho dos dados. Para obter mais informações sobre os requisitos de memória, consulte [maximizar a qualidade do grupo de remo.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)

Depois de ter determinado o requisito de memória, escolha se deve atribuir o utilizador de carga a uma classe de recursos estático ou dinâmico.

- Utilize uma classe de recursos estáticos quando os requisitos de memória de mesa se enquadrarem num intervalo específico. As cargas são executadas com memória apropriada. Quando escala o armazém de dados, as cargas não precisam de mais memória. Utilizando uma classe de recursos estáticos, as alocações de memória permanecem constantes. Esta consistência conserva a memória e permite que mais consultas corram simultaneamente. Recomendamos que as novas soluções utilizem as classes de recursos estáticos primeiro, uma vez que estas proporcionam um maior controlo.
- Utilize uma classe de recursos dinâmicos quando os requisitos de memória de mesa variam muito. As cargas podem requerer mais memória do que o nível atual de DWU ou cDWU fornece. O dimensionamento do armazém de dados adiciona mais memória às operações de carga, o que permite que as cargas funcionem mais rapidamente.

### <a name="resource-classes-for-queries"></a>Aulas de recursos para consultas

Algumas consultas são computacionalmente intensivas e outras não.  

- Escolha uma classe de recursos dinâmicos quando as consultas são complexas, mas não precisa de alta concordância.  Por exemplo, gerar relatórios diários ou semanais é uma necessidade ocasional de recursos. Se os relatórios estiverem a processar grandes quantidades de dados, o dimensionamento do armazém de dados fornece mais memória à classe de recursos existente do utilizador.
- Escolha uma classe de recursos estáticos quando as expectativas dos recursos variam ao longo do dia. Por exemplo, uma classe de recursos estáticos funciona bem quando o armazém de dados é questionado por muitas pessoas. Ao escalonar o armazém de dados, a quantidade de memória atribuída ao utilizador não muda. Consequentemente, mais consultas podem ser executadas paralelamente ao sistema.

Os subsídios de memória adequados dependem de muitos fatores, tais como a quantidade de dados consultados, a natureza dos esquemas de tabela, e várias juntas, selecionam e predicados de grupo. Em geral, a atribuição de mais memória permite que as consultas completem mais rapidamente, mas reduz a concordância geral. Se a concordância não for um problema, a sobre-atribuição da memória não prejudica a produção.

Para sintonizar o desempenho, utilize diferentes classes de recursos. A próxima secção dá um procedimento armazenado que o ajuda a descobrir a melhor classe de recursos.

## <a name="example-code-for-finding-the-best-resource-class"></a>Código de exemplo para encontrar a melhor classe de recursos

Pode utilizar o seguinte procedimento armazenado especificado para calcular a concessão de concuência e memória por classe de recursos numa determinada SLO e a melhor classe de recursos para operações de CCI intensivas de memória em mesa CCI não partida numa determinada classe de recursos:

Aqui está o propósito deste procedimento armazenado:

1. Para ver a concência e o subsídio de memória por classe de recursos numa determinada SLO. O utilizador precisa de fornecer NUN para o esquema e para o nome de mesa, como mostrado neste exemplo.  
2. Para ver a melhor classe de recursos para as operações de CCI intensivas na memória (carga, tabela de cópias, índice de reconstrução, etc.) na tabela CCI não partida numa determinada classe de recursos. O proc armazenado usa esquema de mesa para descobrir o subsídio de memória necessário.

### <a name="dependencies--restrictions"></a>Restrições & dependências

- Este procedimento armazenado não foi concebido para calcular o requisito de memória para uma tabela cci dividida.
- Este procedimento armazenado não tem em conta os requisitos de memória para a parte SELECT de CTAS/INSERT-SELECT e assume que é um SELECT.
- Este procedimento armazenado utiliza uma tabela temporária, que está disponível na sessão onde este procedimento armazenado foi criado.
- Este procedimento armazenado depende das ofertas atuais (por exemplo, configuração de hardware, DMS config), e se alguma dessas alterações mudar, então este proc armazenado não funcionará corretamente.  
- Este procedimento armazenado depende das ofertas limites de concordância existentes e se estas alterações, então este procedimento armazenado não funcionará corretamente.  
- Este procedimento armazenado depende das ofertas de classe de recursos existentes e se estas alterações, então este procedimento armazenado não funcionará corretamente.  

>[!NOTE]  
>Se não estiver a receber a saída após a execução do procedimento armazenado com parâmetros fornecidos, então pode haver dois casos.
>
>1. Ou o parâmetro DW contém um valor SLO inválido
>2. Ou, não há classe de recursos correspondente para a operação CCI em cima da mesa.
>
>Por exemplo, no DW100c, o subsídio de memória mais elevado disponível é de 1 GB, e se o esquema de mesa for suficientemente largo para ultrapassar a exigência de 1 GB.

### <a name="usage-example"></a>Exemplo de utilização

Sintaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Ou fornece um parâmetro NU PARA extrair o DWU atual do DWB ou fornecer qualquer DWU suportado sob a forma de 'DW100c'
2. @SCHEMA_NAME: Fornecer um nome de esquema da mesa
3. @TABLE_NAME: Fornecer um nome de mesa dos juros

Exemplos de execução deste proc armazenado:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A seguinte declaração cria o Quadro1 que é utilizado nos exemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definição de procedimento armazenado

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
(@DWU VARCHAR(8),
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
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
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

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão de utilizadores de bases de dados e segurança, consulte [Secure a database in Synapse SQL](sql-data-warehouse-overview-manage-security.md). Para obter mais informações sobre como as classes de recursos maiores podem melhorar a qualidade do índice de colunas agrupadas, consulte [otimizações de memória para compressão de colunas.](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
