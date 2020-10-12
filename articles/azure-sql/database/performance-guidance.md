---
title: Orientação de afinação de desempenho para aplicações e bases de dados
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre afinação de aplicações e bases de dados para desempenho na Base de Dados Azure SQL e na Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 54a6293a29a407a7014aafb66587dcb01fc13337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89645786"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Sintonize aplicações e bases de dados para desempenho em Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Depois de ter identificado um problema de desempenho que está a enfrentar com a Base de Dados Azure SQL e a Azure SQL Managed Instance, este artigo foi concebido para o ajudar:

- Sintonize a sua aplicação e aplique algumas boas práticas que podem melhorar o desempenho.
- Sintonize a base de dados alterando índices e consultas para trabalhar de forma mais eficiente com os dados.

Este artigo pressupõe que já trabalhou através das recomendações do conselheiro de [base de dados](database-advisor-implement-performance-recommendations.md) Azure SQL Database e das [recomendações de afinação automática](automatic-tuning-overview.md)da Base de Dados Azure SQL, se aplicável. Também assume que reviu [uma visão geral da monitorização e afinação e dos](monitor-tune-overview.md) seus artigos relacionados relacionados com problemas de desempenho de resolução de problemas. Além disso, este artigo assume que não tem recursos de CPU, problema de desempenho relacionado com a execução que pode ser resolvido aumentando o tamanho do cálculo ou nível de serviço para fornecer mais recursos à sua base de dados.

## <a name="tune-your-application"></a>Sintonize a sua aplicação

Nos locais tradicionais DO SQL Server, o processo de planeamento inicial da capacidade é muitas vezes separado do processo de execução de uma aplicação em produção. As licenças de hardware e produtos são compradas primeiro, e a afinação de desempenho é feita depois. Quando se utiliza o Azure SQL, é uma boa ideia interligar o processo de execução de uma aplicação e a sintonizá-la. Com o modelo de pagamento da capacidade a pedido, pode afinar a sua aplicação para utilizar os recursos mínimos necessários agora, em vez de fornecer excessivamente em hardware com base em palpites de futuros planos de crescimento para uma aplicação, que muitas vezes são incorretos. Alguns clientes podem optar por não afinar uma aplicação e, em vez disso, optar por obter recursos de hardware excessivos. Esta abordagem pode ser uma boa ideia se não quiser alterar uma aplicação chave durante um período agitado. Mas, a sintonização de uma aplicação pode minimizar os requisitos de recursos e baixar as faturas mensais quando utiliza os níveis de serviço na Base de Dados Azure SQL e na Azure SQL Managed Instance.

### <a name="application-characteristics"></a>Características da aplicação

Embora os níveis de serviço Azure SQL E Azure SQL Managed Instance sejam projetados para melhorar a estabilidade de desempenho e previsibilidade para uma aplicação, algumas boas práticas podem ajudá-lo a afinar a sua aplicação para tirar melhor partido dos recursos a uma dimensão computacional. Apesar de muitas aplicações terem ganhos significativos de desempenho simplesmente mudando para um tamanho de computação mais elevado ou nível de serviço, algumas aplicações precisam de sintonização adicional para beneficiar de um nível de serviço mais elevado. Para um desempenho acrescido, considere a afinação adicional de aplicações para aplicações com estas características:

- **Aplicações que têm desempenho lento por causa do comportamento "tagarela"**

  As aplicações chatty fazem operações de acesso a dados excessivas que são sensíveis à latência da rede. Poderá ser necessário modificar este tipo de aplicações para reduzir o número de operações de acesso a dados na base de dados. Por exemplo, pode melhorar o desempenho da aplicação utilizando técnicas como a lotação de consultas ad hoc ou mover as consultas para procedimentos armazenados. Para obter mais informações, consulte [as consultas de Lote.](#batch-queries)

- **Bases de dados com uma carga de trabalho intensiva que não pode ser suportada por uma única máquina**

   As bases de dados que excedam os recursos do maior tamanho de computação Premium podem beneficiar da redução da carga de trabalho. Para obter mais informações, consulte [o fragmento de base de dados cruzada](#cross-database-sharding) e a divisão [funcional.](#functional-partitioning)

- **Aplicações que têm consultas sub-óptimas**

  As aplicações, especialmente as da camada de acesso a dados, que têm consultas mal afinadas podem não beneficiar de um tamanho de computação mais elevado. Isto inclui consultas que carecem de uma cláusula WHERE, têm índices em falta ou têm estatísticas desatualizadas. Estas aplicações beneficiam de técnicas padrão de afinação de desempenho de consulta. Para obter mais informações, consulte [índices em falta](#identifying-and-adding-missing-indexes) e [sintonização de consulta e sugestões](#query-tuning-and-hinting).

- **Aplicações que têm design de acesso a dados sub-ideal**

   As aplicações que tenham problemas inerentes ao acesso a dados, por exemplo, impasse, podem não beneficiar de um tamanho de computação mais elevado. Considere reduzir as viagens de ida e volta contra a base de dados, caching dados do lado do cliente com o serviço Azure Caching ou outra tecnologia de caching. Consulte [o caching do nível de aplicação](#application-tier-caching).

## <a name="tune-your-database"></a>Sintonize a sua base de dados

Nesta secção, olhamos para algumas técnicas que pode usar para sintonizar a base de dados para obter o melhor desempenho para a sua aplicação e executá-la com o menor tamanho de computação possível. Algumas destas técnicas combinam com as melhores práticas tradicionais do SQL Server, mas outras são específicas da Base de Dados Azure SQL e da Azure SQL Managed Instance. Em alguns casos, você pode examinar os recursos consumidos para uma base de dados para encontrar áreas para afinar e alargar as técnicas tradicionais do SQL Server para trabalhar em Azure SQL Database e Azure SQL Managed Instance.

### <a name="identifying-and-adding-missing-indexes"></a>Identificar e adicionar índices em falta

Um problema comum no desempenho da base de dados OLTP diz respeito ao desenho da base de dados física. Frequentemente, os esquemas de base de dados são concebidos e enviados sem testes à escala (seja em carga ou em volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em pequena escala, mas degradar-se substancialmente em volumes de dados ao nível da produção. A fonte mais comum desta questão é a falta de índices adequados para satisfazer filtros ou outras restrições numa consulta. Muitas vezes, os índices em falta manifestam-se como uma tomografia de tabela quando um índice procura o suficiente.

Neste exemplo, o plano de consulta selecionado utiliza uma digitalização quando uma procura seria suficiente:

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

![Um plano de consulta com índices em falta](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database e Azure SQL Managed Instance podem ajudá-lo a encontrar e corrigir condições comuns de índice em falta. Os DMVs que são incorporados na Base de Dados Azure SQL e na Azure SQL Managed Instance analisam compilações de consultas em que um índice reduziria significativamente o custo estimado para executar uma consulta. Durante a execução de consultas, o motor da base de dados rastreia a frequência com que cada plano de consulta é executado, e rastreia o intervalo estimado entre o plano de consulta de execução e o imaginado onde esse índice existiu. Pode utilizar estes DMVs para adivinhar rapidamente quais as alterações ao design da sua base de dados física que podem melhorar o custo global da carga de trabalho de uma base de dados e a sua carga de trabalho real.

Pode utilizar esta consulta para avaliar os potenciais índices em falta:

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

Depois de criado, essa mesma declaração SELECT escolhe um plano diferente, que usa uma procura em vez de uma digitalização, e executa o plano de forma mais eficiente:

![Um plano de consulta com índices corrigidos](./media/performance-guidance/query_plan_corrected_indexes.png)

A principal perceção é que a capacidade de IO de um sistema de mercadorias partilhado é mais limitada do que a de uma máquina de servidor dedicada. Há um prémio em minimizar iO desnecessário para tirar o máximo partido do sistema nos recursos de cada tamanho de cálculo dos níveis de serviço. Escolhas adequadas de conceção de bases de dados físicas podem melhorar significativamente a latência para consultas individuais, melhorar a produção de pedidos simultâneos tratados por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para obter mais informações sobre os DMVs de índice em falta, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Afinação de consultas e insinuações

O otimizador de consultas em Azure SQL Database e Azure SQL Managed Instance é semelhante ao otimizador de consulta sL SQL tradicional. A maioria das melhores práticas para afinar consultas e compreender as limitações do modelo de raciocínio para o otimizador de consultas também se aplicam à Base de Dados Azure SQL e à Azure SQL Managed Instance. Se sintonizar consultas na Base de Dados Azure SQL e na Azure SQL Managed Instance, poderá obter o benefício adicional de reduzir as exigências agregadas de recursos. A sua aplicação pode ser capaz de funcionar a um custo inferior ao de um equivalente não afinado, porque pode funcionar com um tamanho de cálculo mais baixo.

Um exemplo que é comum no SQL Server e que também se aplica à Base de Dados Azure SQL e ao Azure SQL Managed Instance é como o otimizador de consulta "cheira" parâmetros. Durante a compilação, o otimizador de consulta avalia o valor atual de um parâmetro para determinar se pode gerar um plano de consulta mais ideal. Embora esta estratégia muitas vezes possa levar a um plano de consulta que é significativamente mais rápido do que um plano compilado sem valores de parâmetros conhecidos, atualmente funciona imperfeitamente tanto no SQL Server, na Base de Dados Azure SQL, como no Azure SQL Managed Instance. Às vezes o parâmetro não é farejado, e às vezes o parâmetro é farejado, mas o plano gerado é sub-ideal para o conjunto completo de valores de parâmetros em uma carga de trabalho. A Microsoft inclui dicas de consulta (diretivas) para que possa especificar a intenção de forma mais deliberada e anular o comportamento padrão do cheiro de parâmetros. Muitas vezes, se utilizar sugestões, pode corrigir casos em que o padrão SQL Server, Azure SQL Database e Azure SQL Managed Instance é imperfeito para uma carga de trabalho específica do cliente.

O próximo exemplo demonstra como o processador de consulta pode gerar um plano que é sub-ideal tanto para os requisitos de desempenho como para os requisitos de recursos. Este exemplo também mostra que, se utilizar uma sugestão de consulta, pode reduzir os requisitos de tempo e recursos de consulta para a sua base de dados:

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

O código de configuração cria uma tabela que distorceu a distribuição de dados. O plano de consulta ideal difere com base no parâmetro selecionado. Infelizmente, o comportamento do plano nem sempre recompile a consulta com base no valor de parâmetro mais comum. Assim, é possível que um plano sub-ideal seja emconsendo e usado para muitos valores, mesmo quando um plano diferente pode ser uma escolha de plano melhor em média. Em seguida, o plano de consulta cria dois procedimentos armazenados que são idênticos, exceto que um tem uma dica de consulta especial.

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

Cada parte deste exemplo tenta executar uma declaração de inserção parametrizada 1.000 vezes (para gerar uma carga suficiente para usar como conjunto de dados de teste). Quando executa os procedimentos armazenados, o processador de consulta examina o valor do parâmetro que é transmitido ao procedimento durante a sua primeira compilação (parâmetro "sniffing"). O processador caches o plano resultante e usa-o para invocações posteriores, mesmo que o valor do parâmetro seja diferente. O plano ideal pode não ser utilizado em todos os casos. Às vezes é preciso guiar o otimizador para escolher um plano que seja melhor para o caso médio do que o caso específico de quando a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "digitalização" que lê todas as linhas para encontrar cada valor que corresponda ao parâmetro:

![Sintonização de consulta usando um plano de digitalização](./media/performance-guidance/query_tuning_1.png)

Como executamos o procedimento utilizando o valor 1, o plano resultante foi ideal para o valor 1, mas foi sub-ideal para todos os outros valores na tabela. O resultado provavelmente não é o que desejaria se escolhesse cada plano aleatoriamente, porque o plano funciona mais lentamente e utiliza mais recursos.

Se executar o teste com `SET STATISTICS IO` set para , o trabalho de `ON` digitalização lógica neste exemplo é feito nos bastidores. Pode ver que há 1.148 leituras feitas pelo plano (o que é ineficiente, se o caso médio é para devolver apenas uma linha):

![Sintonização de consulta usando uma varredura lógica](./media/performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma sugestão de consulta para dizer ao otimizador para usar um valor específico durante o processo de compilação. Neste caso, força o processador de consulta a ignorar o valor que é passado como parâmetro, e em vez de assumir `UNKNOWN` . Isto refere-se a um valor que tem a frequência média na tabela (ignorando o enviesamento). O plano resultante é um plano baseado em procura que é mais rápido e utiliza menos recursos, em média, do que o plano na parte 1 deste exemplo:

![Sintonização de consulta usando uma sugestão de consulta](./media/performance-guidance/query_tuning_3.png)

Pode ver o efeito na tabela **sys.resource_stats** (há um atraso a partir do momento em que executa o teste e quando os dados povoam a tabela). Para este exemplo, parte 1 executada durante a janela de tempo 22:25:00, e parte 2 executada às 22:35:00. A janela de tempo anterior utilizava mais recursos naquela janela de tempo do que a anterior (devido a melhorias de eficiência do plano).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Resultados do exemplo de afinação de consultas](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo seja intencionalmente pequeno, o efeito dos parâmetros sub-ideais pode ser substancial, especialmente em bases de dados maiores. A diferença, em casos extremos, pode ser entre segundos para casos rápidos e horas para casos lentos.

Pode examinar **sys.resource_stats** para determinar se o recurso para um teste utiliza mais ou menos recursos do que outro teste. Quando comparar dados, separe o tempo dos testes de modo a que não estejam na mesma janela de 5 minutos na vista **sys.resource_stats.** O objetivo do exercício é minimizar a quantidade total de recursos utilizados, e não minimizar os recursos máximos. Geralmente, a otimização de um código para a latência também reduz o consumo de recursos. Certifique-se de que as alterações que faz a uma aplicação são necessárias e que as alterações não afetam negativamente a experiência do cliente para alguém que possa estar a usar dicas de consulta na aplicação.

Se uma carga de trabalho tiver um conjunto de consultas repetidas, muitas vezes faz sentido capturar e validar a otimização das suas escolhas de plano, uma vez que conduz a unidade mínima de tamanho de recursos necessária para hospedar a base de dados. Depois de validá-lo, ocasionalmente reexamina os planos para ajudá-lo a garantir que eles não se degradaram. Pode saber mais sobre [dicas de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="very-large-database-architectures"></a>Arquiteturas de base de dados muito grandes

Antes da libertação do nível de serviço [Hyperscale](service-tier-hyperscale.md) para bases de dados únicas na Base de Dados Azure SQL, os clientes costumavam atingir os limites de capacidade para bases de dados individuais. Estes limites de capacidade ainda existem para bases de dados agrárias em piscinas elásticas Azure SQL Database e bases de dados de casos em Azure SQL Managed Instances. As duas secções seguintes discutem duas opções para resolver problemas com bases de dados muito grandes na Base de Dados Azure SQL e na Azure SQL Managed Instance quando não é possível utilizar o nível de serviço hyperscale.

### <a name="cross-database-sharding"></a>Fragmentos de base de dados cruzadas

Uma vez que a Base de Dados Azure SQL e a Azure SQL Managed Instance funciona em hardware de mercadoria, os limites de capacidade para uma base de dados individual são inferiores aos de uma instalação tradicional no local do SQL Server. Alguns clientes usam técnicas de fragmentos para espalhar operações de base de dados em várias bases de dados quando as operações não se enquadram nos limites de uma base de dados individual na Base de Dados Azure SQL e na Azure SQL Managed Instance. A maioria dos clientes que utilizam técnicas de fragmentos na Base de Dados Azure SQL e na Azure SQL Managed Instance dividem os seus dados numa única dimensão através de várias bases de dados. Para esta abordagem, você precisa entender que as aplicações OLTP muitas vezes realizam transações que se aplicam apenas a uma linha ou a um pequeno grupo de linhas no esquema.

> [!NOTE]
> A Azure SQL Database fornece agora uma biblioteca para ajudar no caco. Para mais informações, consulte a [visão geral da biblioteca do cliente da Base de Dados Elástica.](elastic-database-client-library.md)

Por exemplo, se uma base de dados tiver dados de nome, encomenda e encomenda (como a base de dados de exemplo tradicional Northwind que envia com SQL Server), pode dividir estes dados em várias bases de dados, agrupando um cliente com as informações de ordem e de encomenda relacionadas. Pode garantir que os dados do cliente permanecem numa base de dados individual. A aplicação dividiria diferentes clientes através de bases de dados, espalhando efetivamente a carga por várias bases de dados. Com o sharding, os clientes não só podem evitar o limite máximo de tamanho da base de dados, como a Azure SQL Database e a Azure SQL Managed Instance também podem processar cargas de trabalho significativamente maiores do que os limites dos diferentes tamanhos de cálculo, desde que cada base de dados se encaixe nos seus limites de nível de serviço.

Embora o fragmento de base de dados não reduza a capacidade agregada de recursos para uma solução, é altamente eficaz no suporte de soluções muito grandes que estão espalhadas por várias bases de dados. Cada base de dados pode funcionar com um tamanho de computação diferente para suportar bases de dados muito grandes e "eficazes" com elevados requisitos de recursos.

#### <a name="functional-partitioning"></a>Criação de partições funcionais

Os utilizadores muitas vezes combinam muitas funções numa base de dados individual. Por exemplo, se uma aplicação tiver lógica para gerir o inventário de uma loja, essa base de dados pode ter lógica associada ao inventário, rastreando as ordens de compra, procedimentos armazenados e vistas indexadas ou materializadas que gerem relatórios de fim de mês. Esta técnica facilita a administração da base de dados para operações como backup, mas também requer que você dimensione o hardware para lidar com a carga máxima em todas as funções de uma aplicação.

Se utilizar uma arquitetura de escala na Base de Dados Azure SQL e na Azure SQL Managed Instance, é uma boa ideia dividir diferentes funções de uma aplicação em diferentes bases de dados. Utilizando esta técnica, cada aplicação escala de forma independente. À medida que uma aplicação se torna mais movimentada (e a carga na base de dados aumenta), o administrador pode escolher tamanhos de cálculo independentes para cada função na aplicação. No limite, com esta arquitetura, uma aplicação pode ser maior do que uma única máquina de mercadoria pode manusear porque a carga está espalhada por várias máquinas.

### <a name="batch-queries"></a>Consultas de lote

Para aplicações que acedam aos dados utilizando consultas ad hoc de grande volume, frequentes e ad hoc, é gasto uma quantidade substancial de tempo de resposta na comunicação de rede entre o nível de aplicação e o nível de base de dados. Mesmo quando tanto a aplicação como a base de dados estão no mesmo centro de dados, a latência da rede entre os dois pode ser ampliada por um grande número de operações de acesso a dados. Para reduzir as viagens de ida e volta da rede para as operações de acesso a dados, considere usar a opção de lotear as consultas ad hoc ou compilá-las como procedimentos armazenados. Se você emarnar as consultas ad hoc, você pode enviar várias consultas como um grande lote em uma única viagem para a base de dados. Se compilar consultas ad hoc num procedimento armazenado, poderá obter o mesmo resultado que se as emarda. A utilização de um procedimento armazenado também lhe dá o benefício de aumentar as chances de caching os planos de consulta na base de dados para que possa usar novamente o procedimento armazenado.

Algumas aplicações são intensivas. Por vezes, pode reduzir a carga total de IO numa base de dados, considerando como fazer o lote de escritos em conjunto. Muitas vezes, isto é tão simples como usar transações explícitas em vez de fazer transações automáticas em procedimentos armazenados e lotes ad hoc. Para uma avaliação de diferentes técnicas que pode utilizar, consulte [técnicas de batching para aplicações de base de dados em Azure.](../performance-improve-use-batching.md) Experimente com a sua própria carga de trabalho para encontrar o modelo certo para o loteamento. Certifique-se de que um modelo pode ter garantias de consistência transacional ligeiramente diferentes. Encontrar a carga de trabalho certa que minimize o uso do recurso requer encontrar a combinação certa de consistência e trade-offs de desempenho.

### <a name="application-tier-caching"></a>Caching de nível de aplicação

Algumas aplicações de base de dados têm cargas de trabalho pesadas. As camadas de caching podem reduzir a carga na base de dados e podem potencialmente reduzir o tamanho do cálculo necessário para suportar uma base de dados utilizando a Base de Dados Azure SQL e a Azure SQL Managed Instance. Com [a Azure Cache para Redis,](https://azure.microsoft.com/services/cache/)se tiver uma carga de trabalho pesada de leitura, pode ler os dados uma vez (ou talvez uma vez por máquina de nível de aplicação, dependendo da configuração), e, em seguida, armazenar esses dados fora da sua base de dados. Esta é uma forma de reduzir a carga da base de dados (CPU e ler IO), mas há um efeito na consistência transacional porque os dados que estão a ser lidos a partir da cache podem estar dessincronizados com os dados na base de dados. Embora em muitas aplicações algum nível de inconsistência seja aceitável, isso não é verdade para todas as cargas de trabalho. Deve compreender completamente quaisquer requisitos de aplicação antes de implementar uma estratégia de caching de nível de aplicação.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre os níveis de serviço baseados na DTU, consulte o [modelo de compra baseado na DTU.](service-tiers-dtu.md)
- Para obter mais informações sobre os níveis de serviço baseados em vCore, consulte [o modelo de compra baseado em vCore.](service-tiers-vcore.md)
- Para mais informações sobre piscinas elásticas, veja [o que é uma piscina elástica Azure?](elastic-pool-overview.md)
- Para obter informações sobre desempenho e piscinas elásticas, consulte [Quando considerar uma piscina elástica](elastic-pool-overview.md)
