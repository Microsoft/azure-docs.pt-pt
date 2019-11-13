---
title: Guia de otimização do desempenho
description: Saiba mais sobre como usar as recomendações para ajustar manualmente o desempenho da consulta do banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 0dc3a121b30f33d533b1079d9c81501130487017
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009091"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>Ajuste manual de desempenho de consulta no banco de dados SQL do Azure

Depois de identificar um problema de desempenho que você está enfrentando com o banco de dados SQL, este artigo foi criado para ajudá-lo a:

- Ajuste seu aplicativo e aplique algumas práticas recomendadas que podem melhorar o desempenho.
- Ajuste o banco de dados alterando índices e consultas para trabalhar com mais eficiência com os dados.

Este artigo pressupõe que você já tenha trabalhado por meio das recomendações do [supervisor](sql-database-advisor.md) de banco de dados SQL do Azure e das [recomendações de ajuste automático](sql-database-automatic-tuning.md)do banco de dados SQL do Azure. Ele também pressupõe que você tenha revisado [uma visão geral do monitoramento e do ajuste](sql-database-monitor-tune-overview.md) e de seus artigos relacionados relacionados à solução de problemas de desempenho. Além disso, este artigo pressupõe que você não tem recursos de CPU, problema de desempenho relacionado à execução que pode ser resolvido aumentando o tamanho da computação ou a camada de serviço para fornecer mais recursos ao seu banco de dados.

## <a name="tune-your-application"></a>Ajustar seu aplicativo

No SQL Server local tradicional, o processo de planejamento de capacidade inicial geralmente é separado do processo de execução de um aplicativo em produção. As licenças de hardware e produto são compradas primeiro e o ajuste de desempenho é feito posteriormente. Quando você usa o banco de dados SQL do Azure, é uma boa ideia interajustar o processo de executar um aplicativo e ajustá-lo. Com o modelo de pagamento por capacidade sob demanda, você pode ajustar seu aplicativo para usar os recursos mínimos necessários agora, em vez de obter excesso de provisionamento em hardware com base em palpites de planos de crescimento futuros para um aplicativo, que geralmente estão incorretos. Alguns clientes podem optar por não ajustar um aplicativo e, em vez disso, optar por provisionar excessivamente os recursos de hardware. Essa abordagem pode ser uma boa ideia se você não quiser alterar um aplicativo de chave durante um período ocupado. Porém, o ajuste de um aplicativo pode minimizar os requisitos de recursos e reduzir as contas mensais ao usar as camadas de serviço no banco de dados SQL do Azure.

### <a name="application-characteristics"></a>Características do aplicativo

Embora as camadas de serviço do banco de dados SQL do Azure sejam projetadas para melhorar a estabilidade e a previsibilidade do desempenho de um aplicativo, algumas práticas recomendadas podem ajudá-lo a ajustar seu aplicativo para aproveitar melhor os recursos em um tamanho de computação. Embora muitos aplicativos tenham ganhos de desempenho significativos simplesmente alternando para um tamanho de computação ou camada de serviço mais alto, alguns aplicativos precisam de ajuste adicional para se beneficiarem de um nível mais alto de serviço. Para aumentar o desempenho, considere o ajuste adicional do aplicativo para aplicativos com estas características:

- **Aplicativos com desempenho lento devido ao comportamento "informativo"**

  Os aplicativos informais fazem operações de acesso a dados excessivas que são sensíveis à latência de rede. Talvez seja necessário modificar esses tipos de aplicativos para reduzir o número de operações de acesso a dados para o banco de dado SQL. Por exemplo, você pode melhorar o desempenho do aplicativo usando técnicas como o envio em lote de consultas ad hoc ou a movimentação das consultas para procedimentos armazenados. Para obter mais informações, consulte [consultas em lote](#batch-queries).

- **Bancos de dados com uma carga de trabalho intensiva que não tem suporte em um único computador inteiro**

   Os bancos de dados que excedem os recursos do maior tamanho de computação Premium podem se beneficiar do dimensionamento da carga de trabalho. Para obter mais informações, consulte [fragmentação entre bancos de dados](#cross-database-sharding) e [particionamento funcional](#functional-partitioning).

- **Aplicativos que têm consultas abaixo do ideal**

  Os aplicativos, especialmente aqueles na camada de acesso a dados, que têm consultas mal ajustadas podem não se beneficiar de um tamanho de computação mais alto. Isso inclui consultas que não têm uma cláusula WHERE, têm índices ausentes ou têm Estatísticas desatualizadas. Esses aplicativos se beneficiam das técnicas de ajuste de desempenho de consulta padrão. Para obter mais informações, consulte [índices ausentes](#identifying-and-adding-missing-indexes) e [ajuste e dicas de consulta](#query-tuning-and-hinting).

- **Aplicativos com design de acesso a dados abaixo do ideal**

   Os aplicativos que têm problemas de simultaneidade de acesso a dados inerentes, por exemplo, deadlocks, podem não se beneficiar de um tamanho de computação mais alto. Considere a redução de viagens de ida e volta no banco de dados SQL do Azure, armazenando em cache no lado do cliente o serviço de cache do Azure ou outra tecnologia de cache. Consulte [cache da camada de aplicativo](#application-tier-caching).

## <a name="tune-your-database"></a>Ajustar seu banco de dados

Nesta seção, vamos examinar algumas técnicas que você pode usar para ajustar o banco de dados SQL do Azure para obter o melhor desempenho para seu aplicativo e executá-lo com o menor tamanho de computação possível. Algumas dessas técnicas correspondem às práticas recomendadas de ajuste de SQL Server tradicionais, mas outras são específicas do banco de dados SQL do Azure. Em alguns casos, você pode examinar os recursos consumidos de um banco de dados para encontrar áreas para ajustar ainda mais e estender técnicas tradicionais de SQL Server para trabalhar no banco de dados SQL do Azure.

### <a name="identifying-and-adding-missing-indexes"></a>Identificando e adicionando índices ausentes

Um problema comum no desempenho do banco de dados OLTP está relacionado ao design do banco de dados físico. Geralmente, os esquemas de banco de dados são projetados e enviados sem testes em escala (seja na carga ou no volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em uma pequena escala, mas degradando substancialmente em volumes de dados de nível de produção. A origem mais comum desse problema é a falta de índices apropriados para satisfazer filtros ou outras restrições em uma consulta. Geralmente, os índices ausentes são manifestados como uma verificação de tabela quando uma busca de índice pode ser suficiente.

Neste exemplo, o plano de consulta selecionado usa uma verificação quando uma busca seria suficiente:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Um plano de consulta com índices ausentes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

O banco de dados SQL do Azure pode ajudá-lo a encontrar e corrigir condições de índice ausentes comuns. As DMVs criadas no banco de dados SQL do Azure examinam as compilações de consulta nas quais um índice reduziria significativamente o custo estimado para executar uma consulta. Durante a execução da consulta, o banco de dados SQL controla com que frequência cada plano de consulta é executado e controla a lacuna estimada entre o plano de consulta em execução e o imaginado em que o índice existia. Você pode usar essas DMVs para adivinhar rapidamente quais alterações em seu design de banco de dados físico podem melhorar o custo geral da carga de trabalho para um banco de dados e sua carga de trabalho real.

Você pode usar essa consulta para avaliar possíveis índices ausentes:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

Neste exemplo, a consulta resultou nesta sugestão:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Depois de criada, essa mesma instrução SELECT escolhe um plano diferente, que usa uma busca em vez de uma verificação e, em seguida, executa o plano com mais eficiência:

![Um plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A principal percepção é que a capacidade de e/s de um sistema de mercadoria compartilhado é mais limitada do que a de um computador servidor dedicado. Há um prêmio de minimizar a e/s desnecessária para tirar o máximo proveito do sistema na DTU de cada tamanho de computação das camadas de serviço do banco de dados SQL do Azure. As opções apropriadas de design de banco de dados físico podem melhorar significativamente a latência para consultas individuais, melhorar a taxa de transferência de solicitações simultâneas manipuladas por unidade de escala e minimizar os custos necessários para atender à consulta. Para obter mais informações sobre as DMVs de índice ausentes, consulte [Sys. dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Ajuste e dicas de consulta

O otimizador de consulta no banco de dados SQL do Azure é semelhante ao otimizador de consulta tradicional SQL Server. A maioria das práticas recomendadas para ajustar consultas e entender as limitações do modelo de raciocínio para o otimizador de consulta também se aplica ao banco de dados SQL do Azure. Se você ajustar as consultas no banco de dados SQL do Azure, poderá obter o benefício adicional de reduzir as demandas de recursos agregados. Seu aplicativo pode ser capaz de executar com um custo menor do que um equivalente não ajustado, pois ele pode ser executado em um tamanho de computação inferior.

Um exemplo comum em SQL Server e que também se aplica ao banco de dados SQL do Azure é como os parâmetros de "farejações" do otimizador de consulta. Durante a compilação, o otimizador de consulta avalia o valor atual de um parâmetro para determinar se ele pode gerar um plano de consulta mais adequado. Embora essa estratégia geralmente possa levar a um plano de consulta significativamente mais rápido do que um plano compilado sem valores de parâmetro conhecidos, atualmente ele funciona de forma inperfeita tanto no SQL Server quanto no banco de dados SQL do Azure. Às vezes, o parâmetro não é farejado e, às vezes, o parâmetro é farejado, mas o plano gerado é abaixo do ideal para o conjunto completo de valores de parâmetro em uma carga de trabalho. A Microsoft inclui dicas de consulta (diretivas) para que você possa especificar a intenção mais deliberadamente e substituir o comportamento padrão da detecção de parâmetros. Geralmente, se você usar dicas, poderá corrigir casos em que o SQL Server padrão ou o comportamento do banco de dados SQL do Azure é inperfeito para uma carga de trabalho específica do cliente.

O exemplo a seguir demonstra como o processador de consultas pode gerar um plano que é abaixo do ideal para requisitos de desempenho e de recursos. Este exemplo também mostra que, se você usar uma dica de consulta, poderá reduzir o tempo de execução de consulta e os requisitos de recursos para o banco de dados SQL:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

O código de instalação cria uma tabela com distribuição de dados distorcida. O plano de consulta ideal difere com base em qual parâmetro está selecionado. Infelizmente, o comportamento de cache de plano nem sempre recompila a consulta com base no valor de parâmetro mais comum. Portanto, é possível que um plano abaixo do ideal seja armazenado em cache e usado para muitos valores, mesmo quando um plano diferente pode ser uma opção de plano melhor em média. Em seguida, o plano de consulta cria dois procedimentos armazenados que são idênticos, exceto pelo fato de que um tem uma dica de consulta especial.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Recomendamos que você aguarde pelo menos 10 minutos antes de começar a parte 2 do exemplo, para que os resultados sejam distintos nos dados de telemetria resultantes.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Cada parte deste exemplo tenta executar uma instrução INSERT parametrizada 1.000 vezes (para gerar uma carga suficiente para usar como um conjunto de dados de teste). Quando ele executa procedimentos armazenados, o processador de consultas examina o valor do parâmetro que é passado para o procedimento durante sua primeira compilação ("detecção" de parâmetro). O processador armazena em cache o plano resultante e o utiliza para invocações posteriores, mesmo que o valor do parâmetro seja diferente. O plano ideal pode não ser usado em todos os casos. Às vezes, você precisa guiar o otimizador para escolher um plano que seja melhor para o caso médio em vez do caso específico de quando a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "verificação" que lê todas as linhas para localizar cada valor que corresponde ao parâmetro:

![Ajuste de consulta usando um plano de verificação](./media/sql-database-performance-guidance/query_tuning_1.png)

Como executamos o procedimento usando o valor 1, o plano resultante era ideal para o valor 1, mas estava abaixo do ideal para todos os outros valores na tabela. O resultado provavelmente não é o que você desejaria se fosse escolher cada plano aleatoriamente, pois o plano é executado mais lentamente e usa mais recursos.

Se você executar o teste com `SET STATISTICS IO` definido como `ON`, o trabalho de verificação lógica neste exemplo será feito nos bastidores. Você pode ver que há 1.148 leituras feitas pelo plano (o que é ineficiente, se o caso médio for retornar apenas uma linha):

![Ajuste de consulta usando uma verificação lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma dica de consulta para instruir o otimizador a usar um valor específico durante o processo de compilação. Nesse caso, ele força o processador de consulta a ignorar o valor que é passado como o parâmetro e, em vez disso, assumir `UNKNOWN`. Isso se refere a um valor que tem a frequência média na tabela (ignorando a distorção). O plano resultante é um plano baseado em busca que é mais rápido e usa menos recursos, em média, do que o plano na parte 1 deste exemplo:

![Ajuste de consulta usando uma dica de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Você pode ver o efeito na tabela **Sys. resource_stats** (há um atraso desde o momento em que você executa o teste e quando os dados populam a tabela). Para este exemplo, a parte 1 foi executada durante a janela de tempo 22:25:00 e a parte 2 foi executada às 22:35:00. A janela de tempo anterior usou mais recursos na janela de tempo do que o mais recente (devido a melhorias de eficiência do plano).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Resultados de exemplo de ajuste de consulta](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo seja intencionalmente pequeno, o efeito dos parâmetros abaixo do ideal pode ser substancial, especialmente em bancos de dados maiores. A diferença, em casos extremos, pode estar entre segundos para casos rápidos e horas para casos lentos.

Você pode examinar **Sys. resource_stats** para determinar se o recurso de um teste usa mais ou menos recursos do que outro teste. Ao comparar dados, separe o tempo dos testes para que eles não estejam na mesma janela de 5 minutos na exibição **Sys. resource_stats** . O objetivo do exercício é minimizar a quantidade total de recursos usados e não minimizar os recursos de pico. Em geral, a otimização de um trecho de código para latência também reduz o consumo de recursos. Verifique se as alterações feitas em um aplicativo são necessárias e se as alterações não afetam negativamente a experiência do cliente para alguém que possa estar usando dicas de consulta no aplicativo.

Se uma carga de trabalho tiver um conjunto de consultas repetitivas, muitas vezes faz sentido capturar e validar a melhoridade das suas opções de plano, pois ela orienta a unidade mínima de tamanho de recurso necessária para hospedar o banco de dados. Depois de validá-lo, reexamine ocasionalmente os planos para ajudá-lo a garantir que eles não foram degradados. Você pode saber mais sobre [dicas de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Fragmentação entre bancos de dados

Como o banco de dados SQL do Azure é executado em hardware de mercadoria, os limites de capacidade para um banco de dados individual são menores do que para uma instalação de SQL Server local tradicional. Alguns clientes usam técnicas de fragmentação para espalhar operações de banco de dados em vários bancos de dados quando as operações não se ajustam dentro dos limites de um banco de dados individual no banco de dados SQL do Azure. A maior parte dos clientes que usam técnicas de fragmentação no banco de dados SQL do Azure dividem seus dados em uma única dimensão entre vários bancos de dados. Para essa abordagem, você precisa entender que os aplicativos OLTP geralmente executam transações que se aplicam a apenas uma linha ou a um pequeno grupo de linhas no esquema.

> [!NOTE]
> O banco de dados SQL agora fornece uma biblioteca para auxiliar na fragmentação. Para obter mais informações, consulte [visão geral da biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md).

Por exemplo, se um banco de dados tiver o nome do cliente, o pedido e os detalhes do pedido (como o banco de dados de exemplo Northwind tradicional que acompanha o SQL Server), você poderá dividir esses dados em vários bancos de dado agrupando um cliente com o pedido relacionado e detalhes do pedido divulgação. Você pode garantir que os dados do cliente permaneçam em um banco de dado individual. O aplicativo dividiria clientes diferentes em bancos de dados, distribuindo efetivamente a carga entre vários bancos de dados. Com a fragmentação, os clientes não só podem evitar o limite máximo de tamanho do banco de dados, mas o banco de dados SQL do Azure também pode processar cargas de trabalho significativamente maiores do que os limites dos diferentes tamanhos de computação, desde que cada banco de dados individual caiba em seu DTU.

Embora a fragmentação do banco de dados não reduza a capacidade de recursos agregados para uma solução, é altamente eficaz oferecer suporte a soluções muito grandes que se espalham por vários bancos de dados. Cada banco de dados pode ser executado em um tamanho de computação diferente para dar suporte a bancos de dados "efetivos" muito grandes com requisitos de recursos altos.

### <a name="functional-partitioning"></a>Criação de partições funcionais

SQL Server os usuários geralmente combinam muitas funções em um banco de dados individual. Por exemplo, se um aplicativo tiver lógica para gerenciar o inventário de uma loja, esse banco de dados poderá ter uma lógica associada ao inventário, controlar ordens de compra, procedimentos armazenados e exibições indexadas ou materializadas que gerenciam relatórios de fim de mês. Essa técnica facilita a administração do banco de dados para operações como backup, mas também requer que você dimensione o hardware para lidar com a carga de pico em todas as funções de um aplicativo.

Se você usar uma arquitetura de expansão no banco de dados SQL do Azure, é uma boa ideia dividir as diferentes funções de um aplicativo em bancos diferentes de dados. Usando essa técnica, cada aplicativo é dimensionado de forma independente. Como um aplicativo se torna mais ocupado (e a carga no banco de dados aumenta), o administrador pode escolher tamanhos de computação independentes para cada função no aplicativo. No limite, com essa arquitetura, um aplicativo pode ser maior do que um único computador de mercadoria pode lidar, pois a carga é distribuída entre vários computadores.

### <a name="batch-queries"></a>Consultas em lote

Para aplicativos que acessam dados usando consultas ad hoc de alto volume, frequentes, uma quantidade significativa de tempo de resposta é gasto na comunicação de rede entre a camada de aplicativo e a camada de banco de dados SQL do Azure. Mesmo quando o aplicativo e o banco de dados SQL do Azure estão no mesmo data center, a latência de rede entre os dois pode ser ampliada por um grande número de operações de acesso a dados. Para reduzir as viagens de ida e volta da rede para as operações de acesso a dados, considere usar a opção para fazer o lote das consultas ad hoc ou compilá-las como procedimentos armazenados. Se você enviar em lote as consultas ad hoc, poderá enviá-las com várias consultas como um lote grande em uma única viagem para o banco de dados SQL do Azure. Se você compilar consultas ad hoc em um procedimento armazenado, poderá obter o mesmo resultado que o lote. O uso de um procedimento armazenado também oferece o benefício de aumentar as chances de armazenar em cache os planos de consulta no banco de dados SQL do Azure para que você possa usar o procedimento armazenado novamente.

Alguns aplicativos são de gravação intensiva. Às vezes, você pode reduzir a carga de e/s total em um banco de dados considerando como as gravações em lote são juntas. Geralmente, isso é tão simples quanto usar transações explícitas em vez de transações de confirmação automática em procedimentos armazenados e lotes ad hoc. Para obter uma avaliação das diferentes técnicas que você pode usar, consulte [técnicas de envio em lote para aplicativos de banco de dados SQL no Azure](sql-database-use-batching-to-improve-performance.md). Experimente sua própria carga de trabalho para localizar o modelo certo para envio em lote. Lembre-se de entender que um modelo pode ter garantias de consistência transacionais ligeiramente diferentes. Encontrar a carga de trabalho certa que minimiza o uso de recursos exige a localização da combinação certa de compensações de consistência e desempenho.

### <a name="application-tier-caching"></a>Cache da camada de aplicativo

Alguns aplicativos de banco de dados têm cargas de trabalho de leitura pesada. As camadas de cache podem reduzir a carga no banco de dados e podem reduzir potencialmente o tamanho de computação necessário para dar suporte a um banco de dados usando o banco de dados SQL do Azure. Com o [cache do Azure para Redis](https://azure.microsoft.com/services/cache/), se você tiver uma carga de trabalho com muita leitura, poderá ler os dados uma vez (ou talvez uma vez por computador da camada de aplicativo, dependendo de como ele está configurado) e, em seguida, armazenar esses dados fora do banco de dado SQL. Essa é uma maneira de reduzir a carga do banco de dados (CPU e e/s de leitura), mas há um efeito na consistência transacional, pois os dados que estão sendo lidos do cache podem estar fora de sincronia com os dados no banco de dado. Embora, em muitos aplicativos, algum nível de inconsistência seja aceitável, isso não é verdade para todas as cargas de trabalho. Você deve compreender totalmente os requisitos do aplicativo antes de implementar uma estratégia de cache da camada de aplicativo.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre camadas de serviço baseadas em DTU, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
- Para obter mais informações sobre as camadas de serviço baseadas em vCore, consulte [modelo de compra baseado em VCORE](sql-database-service-tiers-vcore.md).
- Para obter mais informações sobre pools elásticos, consulte [o que é um pool elástico do Azure?](sql-database-elastic-pool.md)
- Para obter informações sobre o desempenho e pools elásticos, consulte [quando considerar um pool elástico](sql-database-elastic-pool-guidance.md)
