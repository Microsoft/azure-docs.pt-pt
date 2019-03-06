---
title: Documentação de orientação de otimização do desempenho de base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como utilizar as recomendações para manualmente a otimizar o desempenho das consultas SQL Database do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a49d30d3058a6cf3ce82d56076f348861ad631ff
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438623"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>Manual otimizar o desempenho de consulta na base de dados do Azure SQL

Assim que identificou um problema de desempenho que está a enfrentar com a base de dados SQL, este artigo foi desenvolvido para ajudá-lo a:

- Otimize a sua aplicação e aplicam-se algumas das melhores práticas que podem melhorar o desempenho.
- Ajuste o banco de dados, alterando os índices e consultas com mais eficiência a trabalhar com dados.

Este artigo pressupõe que já trabalhou por meio da base de dados SQL do Azure [recomendações do Assistente de base de dados](sql-database-advisor.md) e a base de dados do SQL do Azure [recomendações de otimização automática](sql-database-automatic-tuning.md). Também parte do princípio de que consultou [uma descrição geral da monitorização e otimização](sql-database-monitor-tune-overview.md) e os respetivos artigos relacionados relacionadas com a resolução de problemas de desempenho. Além disso, este artigo pressupõe que não tem recursos de CPU, o problema de desempenho relacionados com a execução que podem ser resolvidos aumentando o tamanho de computação ou camada para fornecer mais recursos para sua base de dados de serviço.

## <a name="tune-your-application"></a>Ajuste seu aplicativo

No SQL Server do tradicional no local, o processo de planeamento de capacidade inicial, muitas vezes, é separado do processo de execução de uma aplicação na produção. Licenças de produto e de hardware são adquiridas em primeiro lugar e ajuste de desempenho é feito mais tarde. Quando utiliza a base de dados do Azure SQL, é uma boa idéia interweave o processo de execução de uma aplicação e ajuste-lo. Com o modelo de pagar por capacidade a pedido, pode otimizar a sua aplicação para utilizar os recursos mínimos necessários de agora, em vez de sobreaprovisionar em hardware com base nos palpites dos planos de crescimento futuro para um aplicativo, que, muitas vezes, estão incorretos. Alguns clientes podem optar por não otimizar um aplicativo e, em vez disso, optar por aprovisionar excessivamente recursos de hardware. Esta abordagem poderá ser uma boa idéia se não pretender alterar um aplicativo chave durante um período de ocupado. No entanto, um aplicativo de otimização pode minimizar os requisitos de recursos e a fatura mensal inferior ao utilizar os escalões de serviço na base de dados do Azure SQL.

### <a name="application-characteristics"></a>Características de aplicação

Apesar dos escalões de serviço de base de dados do Azure SQL concebidos para melhorar a estabilidade de desempenho e previsibilidade para uma aplicação, algumas das práticas recomendadas podem ajudá-lo a otimizar o seu aplicativo para melhor tirar partido dos recursos num tamanho de computação. Embora muitos aplicativos têm significativa ganhos de desempenho simplesmente alternando para uma maior tamanho de computação ou camada de serviço, alguns aplicativos necessitar de otimização adicional para beneficiar de um nível mais elevado de serviço. Para aumentar o desempenho, considere a otimização de aplicativo adicional para aplicações que têm estas características:

- **Aplicações que têm um desempenho lento devido ao comportamento "conversadoras"**

  Aplicativos chatty fazer operações de acesso excessivo dados sensíveis à latência de rede. Poderá ter de modificar esses tipos de aplicativos para reduzir o número de operações de acesso de dados na base de dados SQL. Por exemplo, pode melhorar o desempenho do aplicativo usando técnicas como a criação de batches de consultas ad hoc ou para mover as consultas para procedimentos armazenados. Para obter mais informações, consulte [consultas de lote](#batch-queries).

- **Bases de dados com uma carga de trabalho intensiva que não são suportados por uma única máquina inteira**

   Bases de dados que excedem os recursos do Premium mais elevado de computação tamanho pode se beneficiar aumentar horizontalmente a carga de trabalho. Para obter mais informações, consulte [fragmentação entre bases de dados](#cross-database-sharding) e [criação de partições funcionais](#functional-partitioning).

- **Aplicações com consultas abaixo do ideal**

  Aplicativos, especialmente aqueles na camada de acesso de dados, mal ter concebido consultas poderão não se beneficiar de um tamanho de computação mais elevado. Isto inclui consultas que não têm uma cláusula WHERE, índices ausentes ou tem desatualizados estatísticas. Esses aplicativos se beneficiam com técnicas de ajuste de desempenho de consulta padrão. Para obter mais informações, consulte [índices ausentes](#identifying-and-adding-missing-indexes) e [consultar o ajuste e indicações](#query-tuning-and-hinting).

- **Aplicações com o design de acesso de dados abaixo do ideal**

   Aplicações que têm problemas de simultaneidade de acesso de dados inerente, travado por exemplo, não poderão se beneficiar de um tamanho de computação mais elevado. Considere a redução de ida e volta no banco de dados do SQL Azure com a cache de dados no lado do cliente com o serviço de cache do Azure ou outra tecnologia de colocação em cache. Ver [colocação em cache de camada de aplicativo](#application-tier-caching).

## <a name="tune-your-database"></a>Otimizar a sua base de dados

Nesta secção, vamos ver algumas técnicas que pode utilizar para otimizar a base de dados do Azure SQL para obter o melhor desempenho para a sua aplicação e executá-la em tamanho de computação possíveis mais baixo. Algumas dessas técnicas correspondem a tradicional do SQL Server, ajuste as práticas recomendadas, mas outros são específicos a base de dados do Azure SQL. Em alguns casos, pode examinar os recursos consumidos para uma base de dados encontrar áreas para otimizar e estender as técnicas tradicionais de SQL Server para funcionar na base de dados do Azure SQL.

### <a name="identifying-and-adding-missing-indexes"></a>Identificar e a adição de índices em falta

Um problema comum no desempenho da base de dados OLTP está relacionado com o design de banco de dados físico. Muitas vezes, esquemas de banco de dados são projetadas e enviadas sem as testar em escala (seja na carga ou no volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em pequena escala, mas degradar significativamente em volumes de dados ao nível da produção. A origem mais comum deste problema é a falta de índices adequados para satisfazer os filtros ou outras restrições numa consulta. Muitas vezes, os manifestos de índices em falta como uma tabela de análise quando seek um índice pode ser suficiente.

Neste exemplo, o plano de consulta selecionada utiliza uma análise quando um seek seria suficiente:

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

Base de dados SQL do Azure pode ajudar a localizar e corrigir comuns em falta condições de índice. DMVs que estão incorporadas na base de dados do Azure SQL ver em que um índice reduziria significativamente o custo estimado para executar uma consulta de compilações de consulta. Durante a execução da consulta, a base de dados SQL controla a frequência com que cada plano de consulta é executado e controla a estimado lacuna entre o plano de consulta em execução e a imagined onde esse índice existia. Pode usar esses DMVs adivinhar rapidamente as alterações que sua estrutura de banco de dados físico podem melhorar o custo de carga de trabalho geral para uma base de dados e a sua carga de trabalho real.

Pode utilizar esta consulta para avaliar o potenciais índices em falta:

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

Depois de criado, essa mesma instrução SELECT escolhe um plano diferente, que usa um seek em vez de uma análise e, em seguida, executa o plano de forma mais eficiente:

![Um plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A principal idéia é que a capacidade de e/s de um sistema de mercadoria partilhado, é mais limitada do que uma máquina do servidor dedicado. Há uma premium na minimização de e/s desnecessários para tirar o máximo partido do sistema em DTU de cada tamanho de computação dos escalões de serviço de base de dados do Azure SQL. Design de banco de dados físico adequado opções podem melhorar significativamente a latência para consultas individuais, melhorar o débito de pedidos simultâneos processados por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para obter mais informações sobre o índice em falta DMVs, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Ajuste de consulta e indicações

O otimizador de consultas na base de dados do Azure SQL é semelhante para o otimizador de consultas do SQL Server tradicional. A maioria das práticas recomendadas para o ajuste de consultas e entender o raciocínio limitações de modelo para o otimizador de consultas também se aplicam a base de dados do Azure SQL. Se ajustar consultas na base de dados do Azure SQL, poderá obter o benefício adicional de reduzir as exigências de recursos de agregação. Seu aplicativo poderá ser executado um custo menor do que um equivalente não atento porque pode ser executado num tamanho de computação mais baixo.

É um exemplo que é comum no SQL Server e que também se aplica a SQL Database do Azure como o otimizador de consultas "fareja" parâmetros. Durante a compilação, o otimizador de consultas avalia o valor atual de um parâmetro para determinar se ele pode gerar um plano de consulta mais ideal. Embora muitas vezes, essa estratégia pode levar para um plano de consulta que é significativamente mais rápido do que um plano compilado sem valores de parâmetro conhecido, ele trabalha atualmente imperfectly ambos no SQL Server e na base de dados do Azure SQL. Por vezes, o parâmetro não é intercetado e, às vezes, o parâmetro é intercetado mas o plano gerado está abaixo do ideal para o conjunto completo de valores de parâmetro numa carga de trabalho. Microsoft inclui sugestões de consulta (diretivas) para que possa especificar intenção mais deliberadamente e substituir o comportamento predefinido de detecção de parâmetro. Muitas vezes, se utilizar sugestões, pode corrigir os casos em que o comportamento padrão do SQL Server ou SQL Database do Azure é imperfeito para uma carga de trabalho do cliente específico.

O exemplo seguinte demonstra como o processador de consultas pode gerar um plano que está abaixo do ideal para desempenho e os requisitos de recursos. Este exemplo também mostra que se utilizar uma sugestão de consulta, pode reduzir os requisitos de recursos e tempo de execução da consulta da base de dados SQL:

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

O código de configuração cria uma tabela que tem inclinados a distribuição de dados. O plano de consulta ideal é diferente com base no qual parâmetro está selecionado. Infelizmente, o plano de comportamento de colocação em cache sempre não recompila a consulta com base no valor de parâmetro mais comuns. Por isso, é possível que um plano abaixo do ideal para ser armazenado em cache e utilizado para muitos valores, mesmo quando um plano diferente pode ser uma opção de plano melhor em média. Em seguida, o plano de consulta cria dois procedimentos armazenados que são idênticos, exceto pelo fato de um tem uma sugestão de consulta especial.

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

Recomendamos que aguarde, pelo menos, 10 minutos antes de começar a parte 2 de exemplo, para que os resultados são diferentes dos dados de telemetria resultante.

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

Cada parte deste exemplo tenta executar uma instrução insert parametrizado 1.000 vezes (para gerar uma carga suficiente para utilizar como um conjunto de dados de teste). Quando executa os procedimentos armazenados, o processador de consultas examina o valor de parâmetro que é passado para o procedimento durante a sua primeira compilação (parâmetro "detecção"). O processador coloca em cache o plano resultante e utiliza-o para invocações posteriores, mesmo que o valor do parâmetro é diferente. O plano ideal não pode ser usado em todos os casos. Às vezes precisa orientar o otimizador de escolher um plano que é melhor para o caso médio, em vez do caso específico de quando a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "verificação", que lê todas as linhas para encontrar cada valor que corresponde do parâmetro:

![Ao utilizar um plano de análise de otimização de consulta](./media/sql-database-performance-guidance/query_tuning_1.png)

Porque o procedimento é executada ao utilizar o valor de 1, o plano resultante foi otimizado para o valor de 1, mas foi abaixo do ideal para todos os outros valores na tabela. O resultado provável que não é o que deve se escolher cada plano aleatoriamente, uma vez que o plano executa mais lentamente e utiliza mais recursos.

Se executar o teste com `SET STATISTICS IO` definido como `ON`, o trabalho de verificação lógica neste exemplo é executado em segundo plano. Pode ver que existem 1,148 leituras feitas pelo plano (que é ineficiente, se o caso médio é retornar apenas uma linha):

![Otimização através de uma verificação de lógica de consulta](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo utiliza uma sugestão de consulta para informar o otimizador de utilizar um valor específico durante o processo de compilação. Neste caso, ela força o processador de consultas para ignorar o valor que é passado como parâmetro, e, em vez disso, supor `UNKNOWN`. Isso se refere a um valor que tem a frequência de média na tabela (ignorando skew). O plano resultante é um plano com base em seek, que é mais rápido e utiliza menos recursos, em média, o plano de na parte 1 deste exemplo:

![Ajuste de consultas, utilizando uma sugestão de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Pode ver o efeito na **resource_stats** tabela (há um atraso a partir do momento em que executar o teste e quando os dados preenche a tabela). Para este exemplo, parte 1, executadas durante a janela de tempo 22:00 25: e executado às 22:00 35: parte 2. A janela de tempo anterior utilizada mais recursos na janela de tempo que o posterior (devido aos melhoramentos de eficiência de plano).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Ajuste os resultados de exemplo da consulta](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo é intencionalmente pequeno, o efeito de longe do ideal parâmetros pode ser substancial, especialmente em bases de dados maiores. A diferença, em casos extremos, pode ser entre segundos para casos rápidos e horas para casos lentas.

Pode examinar **resource_stats** para determinar se o recurso para um teste utiliza recursos mais ou menos do que outro teste. Ao comparar dados, separar o cronograma de testes para que não estão na mesma janela de 5 minutos no **resource_stats** vista. O objetivo do exercício é para minimizar a quantidade total de recursos utilizados e não para minimizar os recursos de pico. Em geral, otimizar um trecho de código para latência também reduz o consumo de recursos. Certifique-se de que as alterações que fizer para um aplicativo são necessárias e que as alterações não afetam negativamente a experiência do cliente para alguém que possam estar a utilizar sugestões de consulta no aplicativo.

Se uma carga de trabalho tem um conjunto de repetição de consultas, muitas vezes faz sentido para capturar e validar o optimality de suas opções de plano porque o orienta a unidade de tamanho mínimo de recursos necessária para alojar a base de dados. Depois de validar, ocasionalmente reexaminar os planos para o ajudar a tornar-se de que eles não têm degradado. Pode saber mais sobre [consultar sugestões (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Fragmentação de entre bases de dados

Como o SQL Database do Azure é executado no hardware do produto, os limites de capacidade para uma base de dados individual são mais baixos que uma instalação do SQL Server tradicionais no local. Alguns clientes usam técnicas de fragmentação para distribuir as operações de base de dados ao longo de várias bases de dados quando as operações não se ajustam dentro dos limites de uma base de dados individual na base de dados do Azure SQL. A maioria dos clientes que usam técnicas de fragmentação na base de dados do Azure SQL dividir os dados numa única dimensão em várias bases de dados. Para essa abordagem, precisa entender o que os aplicativos de OLTP costumam realizam muitas transações que se apliquem a apenas uma linha ou um pequeno grupo de linhas no esquema.

> [!NOTE]
> Agora, a base de dados SQL fornece uma biblioteca para ajudar a fragmentação. Para obter mais informações, consulte [descrição geral da biblioteca de cliente da linha de base de dados elástica](sql-database-elastic-database-client-library.md).

Por exemplo, se uma base de dados tem o nome do cliente, ordem e os detalhes de pedido (como o tradicional de exemplo Northwind fornecido com o SQL Server), pode dividir dados em várias bases de dados através do agrupamento de um cliente com o relativas às encomendas e orderdetail informações. Pode garantir que os dados do cliente permanecem num banco de dados individual. O aplicativo é dividido diferentes clientes entre bases de dados, com eficiência distribuição da carga entre várias bases de dados. Com a fragmentação, os clientes não só podem evitar o limite de tamanho máximo da base de dados, mas a base de dados do Azure SQL também pode processar cargas de trabalho que são significativamente maiores que os limites de tamanhos de computação diferentes, desde que cada base de dados individual se encaixa no seu DTU.

Embora a fragmentação de base de dados não reduz a capacidade do recurso agregado de uma solução, é altamente uma forma eficaz de suporte a soluções muito grandes que são distribuídas por várias bases de dados. Cada base de dados pode ser executado num tamanho de computação diferentes para oferecer suporte a muito grandes, bases de dados "eficiente" com requisitos de recursos elevados.

### <a name="functional-partitioning"></a>Criação de partições funcionais

Utilizadores do SQL Server, muitas vezes, combinam várias funções numa base de dados individual. Por exemplo, se um aplicativo tem lógica para gerir o inventário de um arquivo, essa base de dados pode ter lógica associados com controle de inventário, ordens de compra, procedimentos armazenados e modos de exibição indexados ou materializados que gerir relatórios do fim do mês. Essa técnica torna mais fácil administrar a base de dados para operações como cópia de segurança, mas ele também requer que o hardware para processar o pico de carga em todas as funções de uma aplicação de tamanho.

Se utilizar uma arquitetura de escalamento horizontal na base de dados do Azure SQL, é uma boa idéia dividir funções diferentes de uma aplicação em diferentes bases de dados. Usando essa técnica, cada aplicativo dimensionamento independente. Como um aplicativo torna-se quanto mais ocupado (e aumenta a carga na base de dados), o administrador pode escolher tamanhos de computação independente para cada função no aplicativo. Atingiu o limite, com essa arquitetura, um aplicativo pode ser maior do que uma máquina de mercadoria único pode manipular uma vez que a carga é distribuída em várias máquinas.

### <a name="batch-queries"></a>Consultas em lote

Para aplicações que acedem a dados através da utilização de grande volume, frequentes, consultas ad hoc, uma quantidade substancial de tempo de resposta é gasto em comunicações de rede entre a camada de aplicativos e a camada de base de dados do Azure SQL. Mesmo quando a aplicação e o SQL Database do Azure estão no mesmo centro de dados, operações de acesso poderá ampliação por um grande número de dados a latência de rede entre os dois. Para reduzir a rede round automático passar para as operações de acesso de dados, considere utilizar a opção para optar por lote as consultas ad hoc ou para compilá-los procedimentos armazenados como. Se as consultas ad hoc do batch, pode enviar várias consultas como um lote grande numa única viagem para a base de dados do Azure SQL. Se compilar consultas ad hoc num procedimento armazenado, é possível atingir o mesmo resultado, como se do batch-los. Usar um procedimento armazenado também oferece o benefício de aumentar as chances de colocação em cache os planos de consulta na base de dados do Azure SQL, pelo que pode utilizar o procedimento armazenado novamente.

Alguns aplicativos são escrita intensiva. Por vezes, pode reduzir a carga de e/s total numa base de dados considerando como gravações do batch em conjunto. Muitas vezes, isso é tão simples quanto usar transações explícitas em vez de transações de consolidação automática em procedimentos armazenados e lotes ad hoc. Para uma edição de avaliação de técnicas diferentes que pode utilizar, consulte [técnicas para aplicações de base de dados SQL no Azure de criação de batches](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimente com o seu próprio carga de trabalho para encontrar o modelo certo para a criação de batches. Certifique-se de que compreende que a um modelo pode ter garantias de consistência transacional ligeiramente diferente. Localizar a carga de trabalho correta que minimiza o uso de recursos, é necessário localizar a combinação certa de vantagens e desvantagens de desempenho e consistência.

### <a name="application-tier-caching"></a>Colocação em cache de camada de aplicativos

Alguns aplicativos de banco de dados têm cargas de trabalho de leitura intensiva. Camadas de armazenamento em cache pode reduzir a carga na base de dados e pode reduzir o tamanho de computação necessário para suportar uma base de dados com a base de dados do Azure SQL. Com o [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/), se tiver uma carga de trabalho de leitura intensiva, pode ler os dados uma vez (ou talvez uma vez por computador de camada de aplicativos, dependendo de como é configurado) e, em seguida, armazenar esses dados fora da sua base de dados SQL. Esta é uma forma de reduzir a carga de base de dados (CPU e e/s de leitura), mas existe um efeito na consistência transacional, porque os dados que está a ser lidos da cache podem ser sincronizados com os dados na base de dados. Embora em muitos aplicativos algum nível de inconsistência é aceitável, que não é verdadeiro para todas as cargas de trabalho. Deve compreender na totalidade quaisquer requisitos de aplicação antes de implementar uma estratégia de colocação em cache de camada de aplicativos.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre escalões de serviço baseado em DTU, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
- Para obter mais informações sobre escalões de serviço baseado em vCore, consulte [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- Para obter mais informações sobre conjuntos elásticos, consulte [o que é um conjunto elástico do Azure?](sql-database-elastic-pool.md)
- Para obter informações sobre os conjuntos de desempenho e elástico, consulte [quando considerar um conjunto elástico](sql-database-elastic-pool-guidance.md)