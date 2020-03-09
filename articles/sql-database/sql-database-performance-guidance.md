---
title: Guia de otimização do desempenho
description: Aprenda a usar recomendações para sintonizar manualmente o seu desempenho de consulta de base de dados Azure SQL.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382368"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>Desempenho de consulta de melodia manual na Base de Dados Azure SQL

Depois de ter identificado um problema de desempenho que está a enfrentar com a Base de Dados SQL, este artigo foi concebido para o ajudar:

- Sintonize a sua aplicação e aplique algumas boas práticas que podem melhorar o desempenho.
- Sintonize a base de dados alterando índices e consultas para trabalhar de forma mais eficiente com os dados.

Este artigo pressupõe que já trabalhou através das [recomendações](sql-database-advisor.md) de consultores de dados de base de dados Azure SQL e das recomendações de [afinação automática](sql-database-automatic-tuning.md)da Base de Dados Azure SQL. Também assume que reviu [uma visão geral da monitorização e afinação](sql-database-monitor-tune-overview.md) e seus artigos relacionados relacionados relacionados com problemas de desempenho. Além disso, este artigo assume que não tem recursos cpu, problema de desempenho relacionado com o funcionamento que pode ser resolvido aumentando o tamanho da computação ou o nível de serviço para fornecer mais recursos à sua base de dados.

## <a name="tune-your-application"></a>Sintonize a sua aplicação

No tradicional SQL Server, o processo de planeamento inicial da capacidade é frequentemente separado do processo de execução de uma aplicação em produção. As licenças de hardware e produto são compradas primeiro, e a afinação de desempenho é feita depois. Quando utiliza a Base de Dados Azure SQL, é uma boa ideia interceder o processo de execução de uma aplicação e ajustá-la. Com o modelo de pagamento da capacidade a pedido, pode sintonizar a sua aplicação para utilizar os recursos mínimos necessários agora, em vez de sobre-fornecer hardware com base em palpites de planos de crescimento futuros para uma aplicação, que muitas vezes são incorretas. Alguns clientes podem optar por não afinar uma aplicação e, em vez disso, optar por fornecer recursos de hardware em excesso. Esta abordagem pode ser uma boa ideia se não quiser alterar uma aplicação chave durante um período agitado. Mas, sintonizar uma aplicação pode minimizar os requisitos de recursos e baixar as faturas mensais quando utiliza os níveis de serviço na Base de Dados Azure SQL.

### <a name="application-characteristics"></a>Características de aplicação

Embora os níveis de serviço de base de dados Azure SQL sejam projetados para melhorar a estabilidade de desempenho e a previsibilidade de uma aplicação, algumas boas práticas podem ajudá-lo a afinar a sua aplicação para melhor tirar partido dos recursos em tamanho de computação. Embora muitas aplicações tenham ganhos significativos de desempenho simplesmente mudando para um tamanho de computação mais elevado ou nível de serviço, algumas aplicações precisam de afinação adicional para beneficiar de um nível de serviço mais elevado. Para um maior desempenho, considere a sintonização adicional de aplicações que tenham estas características:

- **Aplicações que têm desempenho lento devido ao comportamento "tagarela"**

  As aplicações tagarela fazem operações excessivas de acesso a dados que são sensíveis à latência da rede. Pode ser necessário modificar este tipo de aplicações para reduzir o número de operações de acesso a dados na base de dados SQL. Por exemplo, pode melhorar o desempenho da aplicação utilizando técnicas como o lote de consultas ad hoc ou a deslocação das consultas para procedimentos armazenados. Para mais informações, consulte [consultas de lote](#batch-queries).

- **Bases de dados com uma carga de trabalho intensiva que não pode ser suportada por uma única máquina**

   Bases de dados que excedam os recursos do maior tamanho de cálculo Premium podem beneficiar da redução da carga de trabalho. Para mais informações, consulte a base [de dados cruzada](#cross-database-sharding) e a partilha [funcional](#functional-partitioning).

- **Aplicações que têm consultas sub-óptimas**

  As aplicações, especialmente as da camada de acesso a dados, que tenham dúvidas mal afinadas podem não beneficiar de um tamanho de computação mais elevado. Isto inclui consultas que carecem de uma cláusula DE ONDE, têm índices em falta ou têm estatísticas desatualizadas. Estas aplicações beneficiam de técnicas padrão de afinação de desempenho. Para mais informações, consulte [índices em falta](#identifying-and-adding-missing-indexes) e [afinação e insinuação](#query-tuning-and-hinting)de consultas.

- **Aplicações que têm design de acesso a dados sub-ideal**

   As aplicações que têm problemas inerentes ao acesso a dados, por exemplo, o impasse, podem não beneficiar de uma maior dimensão do cálculo. Considere reduzir as viagens de ida e volta contra a Base de Dados Azure SQL, apertando dados do lado do cliente com o serviço Azure Caching ou outra tecnologia de cache. Ver [Caching nível](#application-tier-caching)de aplicação .

## <a name="tune-your-database"></a>Sintonize a sua base de dados

Nesta secção, olhamos para algumas técnicas que pode usar para sintonizar a Base de Dados Azure SQL para obter o melhor desempenho para a sua aplicação e executá-la no tamanho mais baixo possível da computação. Algumas destas técnicas combinam com as tradicionais práticas de afinação do Servidor SQL, mas outras são específicas da Base de Dados Azure SQL. Em alguns casos, pode examinar os recursos consumidos para uma base de dados para encontrar áreas para sintonizar e alargar as técnicas tradicionais do SQL Server para trabalhar na Base de Dados Azure SQL.

### <a name="identifying-and-adding-missing-indexes"></a>Identificar e adicionar índices em falta

Um problema comum no desempenho da base de dados OLTP diz respeito ao desenho da base de dados física. Muitas vezes, os esquemas de base de dados são concebidos e enviados sem testes à escala (em carga ou em volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em pequena escala, mas degrada-se substancialmente em volumes de dados ao nível da produção. A fonte mais comum desta questão é a falta de índices adequados para satisfazer filtros ou outras restrições numa consulta. Muitas vezes, os índices em falta manifestam-se como uma digitalização de tabela quando uma procura de índice pode ser suficiente.

Neste exemplo, o plano de consulta selecionado usa uma varredura quando uma procura seria suficiente:

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

![Um plano de consulta com índices em falta](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

A Base de Dados Azure SQL pode ajudá-lo a encontrar e corrigir condições comuns de índice em falta. Os DMVs incorporados na Base de Dados Azure SQL analisam as compilações de consultas em que um índice reduziria significativamente o custo estimado para executar uma consulta. Durante a execução da consulta, a Base de Dados SQL rastreia a frequência com que cada plano de consulta é executado, e rastreia o fosso estimado entre o plano de execução e o imaginado onde esse índice existiu. Pode utilizar estes DMVs para adivinhar rapidamente quais as alterações ao design da sua base de dados física que podem melhorar o custo global de carga de trabalho para uma base de dados e a sua carga de trabalho real.

Pode utilizar esta consulta para avaliar potenciais índices em falta:

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

Depois de criada, essa mesma declaração SELECT escolhe um plano diferente, que usa uma procura em vez de uma varredura, e depois executa o plano de forma mais eficiente:

![Um plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A principal visão é que a capacidade de IO de um sistema de mercadorias partilhado é mais limitada do que a de uma máquina de servidor dedicada. Há um prémio em minimizar o IO desnecessário para tirar o máximo partido do sistema no DTU de cada tamanho computacional dos níveis de serviço de base de dados Azure SQL. As escolhas de conceção de bases de dados físicas adequadas podem melhorar significativamente a latência para consultas individuais, melhorar a entrada de pedidos simultâneos tratados por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para obter mais informações sobre os DMVs do índice em falta, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Afinação e insinuação de consultas

O optimizador de consultas na Base de Dados Azure SQL é semelhante ao tradicional optimizador de consulta sQL Server. A maioria das melhores práticas para afinar consultas e compreender as limitações do modelo de raciocínio para o optimizador de consultas também se aplicam à Base de Dados Azure SQL. Se sintonizar consultas na Base de Dados Azure SQL, poderá obter o benefício adicional de reduzir as exigências de recursos agregados. A sua aplicação pode ser capaz de funcionar a um custo mais baixo do que um equivalente não afinado porque pode funcionar a um tamanho de computação mais baixo.

Um exemplo que é comum no SQL Server e que também se aplica à Base de Dados Azure SQL é como os parâmetros do optimizador de consultas "farejam". Durante a compilação, o optimizador de consultas avalia o valor atual de um parâmetro para determinar se pode gerar um plano de consulta mais ideal. Embora esta estratégia possa muitas vezes levar a um plano de consulta que é significativamente mais rápido do que um plano compilado sem valores de parâmetros conhecidos, atualmente funciona imperfeitamente tanto no SQL Server como na Base de Dados Azure SQL. Às vezes o parâmetro não é farejado, e às vezes o parâmetro é farejado, mas o plano gerado é sub-ideal para o conjunto completo de valores de parâmetros em uma carga de trabalho. A Microsoft inclui dicas de consulta (diretivas) para que possa especificar a intenção de forma mais deliberada e anular o comportamento padrão do cheiro de parâmetros. Muitas vezes, se utilizar sugestões, pode corrigir casos em que o comportamento padrão do SQL Server ou do Azure SQL Database é imperfeito para uma carga de trabalho específica do cliente.

O exemplo seguinte demonstra como o processador de consulta pode gerar um plano que é sub-ideal tanto para os requisitos de desempenho como para recursos. Este exemplo também mostra que se utilizar uma sugestão de consulta, pode reduzir os requisitos de tempo de execução de consulta e recursos para a sua base de dados SQL:

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

O código de configuração cria uma tabela que tem distorcido a distribuição de dados. O plano de consulta ideal difere com base no parâmetro selecionado. Infelizmente, o comportamento do plano nem sempre recompila a consulta com base no valor mais comum do parâmetro. Assim, é possível que um plano sub-ideal seja cached e usado para muitos valores, mesmo quando um plano diferente pode ser uma escolha de plano melhor, em média. Em seguida, o plano de consulta cria dois procedimentos armazenados que são idênticos, exceto que um tem uma dica especial de consulta.

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

Recomendamos que espere pelo menos 10 minutos antes de iniciar a parte 2 do exemplo, de modo a que os resultados sejam distintos nos dados de telemetria resultantes.

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

Cada parte deste exemplo tenta executar uma declaração de inserção parametrizada 1.000 vezes (para gerar uma carga suficiente para usar como conjunto de dados de teste). Quando executa os procedimentos armazenados, o processador de consulta examina o valor do parâmetro que é passado ao procedimento durante a sua primeira compilação (parâmetro "sniffing"). O processador caches o plano resultante e usa-o para invocações posteriores, mesmo que o valor do parâmetro seja diferente. O plano ideal pode não ser usado em todos os casos. Às vezes é preciso guiar o optimizador para escolher um plano que seja melhor para o caso médio e não para o caso específico a partir do momento em que a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "digitalização" que lê todas as linhas para encontrar cada valor que corresponda ao parâmetro:

![Afinação de consultas usando um plano de digitalização](./media/sql-database-performance-guidance/query_tuning_1.png)

Como executámos o procedimento utilizando o valor 1, o plano resultante foi ideal para o valor 1, mas foi sub-ideal para todos os outros valores da tabela. O resultado provavelmente não é o que você gostaria se escolhesse cada plano aleatoriamente, porque o plano funciona mais lentamente e usa mais recursos.

Se fizer o teste com `SET STATISTICS IO` definido para `ON`, o trabalho de digitalização lógica neste exemplo é feito nos bastidores. Pode ver que existem 1.148 leituras feitas pelo plano (o que é ineficiente, se o caso médio é para voltar apenas uma linha):

![Afinação de consultas usando uma varredura lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma sugestão de consulta para dizer ao optimizador para usar um valor específico durante o processo de compilação. Neste caso, obriga o processador de consulta a ignorar o valor que é passado como parâmetro, e em vez de assumir `UNKNOWN`. Isto refere-se a um valor que tem a frequência média na tabela (ignorando o enviesamento). O plano resultante é um plano baseado em procura que é mais rápido e utiliza menos recursos, em média, do que o plano na parte 1 deste exemplo:

![Afinação de consulta usando uma dica de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Pode ver o efeito na tabela **sys.resource_stats** (há um atraso a partir do momento em que executa o teste e quando os dados povoam a tabela). Para este exemplo, a parte 1 executada durante a janela de tempo 22:25:00, e a parte 2 executada às 22:35:00. A janela de tempo anterior utilizou mais recursos naquela janela do tempo do que a anterior (devido a melhorias de eficiência do plano).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Resultados do exemplo de afinação de consultas](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo seja intencionalmente pequeno, o efeito dos parâmetros sub-ideais pode ser substancial, especialmente em bases de dados maiores. A diferença, em casos extremos, pode ser entre segundos para casos rápidos e horas para casos lentos.

Pode examinar **sys.resource_stats** para determinar se o recurso para um teste utiliza mais ou menos recursos do que outro teste. Quando comparar dados, separe o tempo dos testes para que não estejam na mesma janela de 5 minutos na vista **sys.resource_stats.** O objetivo do exercício é minimizar a quantidade total de recursos utilizados, e não minimizar os recursos máximos. Geralmente, a otimização de um código para a latência também reduz o consumo de recursos. Certifique-se de que as alterações que faz a uma aplicação são necessárias e que as alterações não afetam negativamente a experiência do cliente para alguém que possa estar a usar sugestões de consulta na aplicação.

Se uma carga de trabalho tiver um conjunto de consultas repetidas, muitas vezes faz sentido capturar e validar a otimização das escolhas do seu plano porque conduz a unidade de tamanho mínimo de recursos necessária para alojar a base de dados. Depois de validá-lo, ocasionalmente reexamine os planos para ajudá-lo a certificar-se de que eles não se degradaram. Você pode aprender mais sobre dicas de [consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Sharding de base de dados cruzada

Uma vez que a Base de Dados Azure SQL funciona em hardware de mercadoria, os limites de capacidade para uma base de dados individual são inferiores às de uma instalação tradicional no local Do Servidor SQL. Alguns clientes usam técnicas de sharding para difundir operações de base de dados em várias bases de dados quando as operações não se enquadram dentro dos limites de uma base de dados individual na Base de Dados Azure SQL. A maioria dos clientes que usam técnicas de sharding na Base de Dados Azure SQL dividem os seus dados numa única dimensão em várias bases de dados. Para esta abordagem, é necessário compreender que as aplicações OLTP realizam frequentemente transações que se aplicam a apenas uma linha ou a um pequeno grupo de linhas no esquema.

> [!NOTE]
> A Base de Dados SQL fornece agora uma biblioteca para ajudar com o sharding. Para mais informações, consulte a visão geral da biblioteca do [cliente da Elástico.](sql-database-elastic-database-client-library.md)

Por exemplo, se uma base de dados tiver nome, encomenda e detalhes de encomenda (como a base de dados tradicional do Northwind que envia com O Servidor SQL), pode dividir estes dados em várias bases de dados agrupando um cliente com o detalhe de encomendas e encomendas relacionados informação. Pode garantir que os dados do cliente se mantenham numa base de dados individual. A aplicação dividiria diferentes clientes através de bases de dados, efetivamente espalhando a carga através de várias bases de dados. Com o sharding, os clientes não só podem evitar o limite máximo de tamanho da base de dados, como também a Base de Dados Azure SQL também pode processar cargas de trabalho significativamente maiores do que os limites dos diferentes tamanhos de computação, desde que cada base de dados individual se encaixe no seu DTU.

Embora a base de dados não reduza a capacidade de recursos agregados para uma solução, é altamente eficaz em suportar soluções muito grandes que estão espalhadas por várias bases de dados. Cada base de dados pode funcionar com um tamanho de computação diferente para suportar bases de dados muito grandes e "eficazes" com elevados requisitos de recursos.

### <a name="functional-partitioning"></a>Criação de partições funcionais

Os utilizadores do SQL Server combinam frequentemente muitas funções numa base de dados individual. Por exemplo, se uma aplicação tem lógica para gerir o inventário de uma loja, essa base de dados pode ter lógica associada ao inventário, rastreamento de ordens de compra, procedimentos armazenados e vistas indexadas ou materializadas que gerem relatórios de fim de mês. Esta técnica facilita a administração da base de dados para operações como backup, mas também requer que você dimensione o hardware para lidar com a carga máxima em todas as funções de uma aplicação.

Se utilizar uma arquitetura de escala na Base de Dados Azure SQL, é uma boa ideia dividir diferentes funções de uma aplicação em diferentes bases de dados. Utilizando esta técnica, cada aplicação escala de forma independente. À medida que uma aplicação se torna mais movimentada (e a carga na base de dados aumenta), o administrador pode escolher tamanhos de computação independentes para cada função na aplicação. No limite, com esta arquitetura, uma aplicação pode ser maior do que uma única máquina de mercadoria pode manusear porque a carga é espalhada por várias máquinas.

### <a name="batch-queries"></a>Consultas de lote

Para aplicações que acedem a dados utilizando consultas ad hoc de alto volume, frequentes e ad hoc, uma quantidade substancial de tempo de resposta é gasto na comunicação de rede entre o nível de aplicação e o nível de base de dados Azure SQL. Mesmo quando tanto a aplicação como a Base de Dados Azure SQL estão no mesmo centro de dados, a latência da rede entre os dois pode ser ampliada por um grande número de operações de acesso a dados. Para reduzir as viagens de ida e volta da rede para as operações de acesso a dados, considere utilizar a opção de remar as consultas ad hoc ou compilar como procedimentos armazenados. Se você emite as consultas ad hoc, você pode enviar múltiplas consultas como um grande lote em uma única viagem à Base de Dados Azure SQL. Se compilar consultas ad hoc num procedimento armazenado, poderá obter o mesmo resultado que se as emita em lotação. A utilização de um procedimento armazenado também lhe dá o benefício de aumentar as chances de gravar os planos de consulta na Base de Dados Azure SQL para que possa voltar a utilizar o procedimento armazenado.

Algumas aplicações são intensivas. Por vezes, pode reduzir a carga total de IO numa base de dados, considerando como escrever em conjunto. Muitas vezes, isto é tão simples como usar transações explícitas em vez de transações automáticas em procedimentos armazenados e lotes ad hoc. Para uma avaliação de diferentes técnicas pode utilizar, consulte [técnicas de loteamento para aplicações de Base de Dados SQL em Azure](sql-database-use-batching-to-improve-performance.md). Experimente com a sua própria carga de trabalho para encontrar o modelo certo para o loteamento. Certifique-se de que um modelo pode ter garantias de consistência transacional ligeiramente diferentes. Encontrar a carga de trabalho certa que minimize o uso de recursos requer encontrar a combinação certa de consistência e trocas de desempenho.

### <a name="application-tier-caching"></a>Cache de nível de aplicação

Algumas aplicações de base de dados têm cargas de trabalho pesadas. As camadas de cache podem reduzir a carga na base de dados e podem potencialmente reduzir o tamanho da computação necessária para suportar uma base de dados utilizando a Base de Dados Azure SQL. Com [o Azure Cache for Redis](https://azure.microsoft.com/services/cache/), se tiver uma carga de trabalho pesada, pode ler os dados uma vez (ou talvez uma vez por máquina de nível de aplicação, dependendo da configuração), e depois armazenar esses dados fora da sua base de dados SQL. Esta é uma forma de reduzir a carga de base de dados (CPU e ler IO), mas há um efeito na consistência transacional porque os dados que estão a ser lidos a partir da cache podem estar dessincronizados com os dados na base de dados. Embora em muitas aplicações algum nível de inconsistência seja aceitável, isso não é verdade para todas as cargas de trabalho. Deve compreender completamente quaisquer requisitos de aplicação antes de implementar uma estratégia de cache de nível de aplicação.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre os níveis de serviço baseados em DTU, consulte [o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
- Para obter mais informações sobre os níveis de serviço baseados em vCore, consulte o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- Para mais informações sobre piscinas elásticas, veja [o que é uma piscina elástica Azure?](sql-database-elastic-pool.md)
- Para obter informações sobre desempenho e piscinas elásticas, consulte [Quando considerar uma piscina elástica](sql-database-elastic-pool-guidance.md)
