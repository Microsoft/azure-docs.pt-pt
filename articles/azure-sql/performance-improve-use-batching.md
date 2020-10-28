---
title: Como utilizar o lote para melhorar o desempenho da aplicação
description: O tópico fornece provas de que as operações de base de dados de loteamento melhoram consideravelmente a velocidade e escalabilidade das suas aplicações Azure SQL Management Instance. Embora estas técnicas de loteamento funcionem para qualquer base de dados SQL, o foco do artigo está no Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 07334d62cee94be8b5b8dd6188c1d6354c4d584b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792604"
---
# <a name="how-to-use-batching-to-improve-azure-sql-database-and-azure-sql-managed-instance-application-performance"></a>Como utilizar o lote para melhorar o desempenho da aplicação Azure SQL Estrudindo
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

As operações de loteamento para Azure SQL Database e Azure SQL Managed Instance melhoram significativamente o desempenho e escalabilidade das suas aplicações. Para compreender os benefícios, a primeira parte deste artigo cobre alguns resultados de testes de amostra que comparam pedidos sequenciais e em lote a uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance. O restante do artigo mostra as técnicas, cenários e considerações para ajudá-lo a usar o lote com sucesso nas suas aplicações Azure.

## <a name="why-is-batching-important-for-azure-sql-database-and-azure-sql-managed-instance"></a>Porque é que o lote é importante para a Azure SQL Database e para a Azure SQL Managed Instance?

O loteamento de chamadas para um serviço remoto é uma estratégia bem conhecida para aumentar o desempenho e a escalabilidade. Existem custos de processamento fixos para quaisquer interações com um serviço remoto, tais como serialização, transferência de rede e desseialização. A embalagem de muitas transações separadas num único lote minimiza estes custos.

Neste artigo, queremos examinar várias estratégias e cenários de loteamento. Embora estas estratégias também sejam importantes para aplicações no local que utilizam o SQL Server, existem várias razões para destacar o uso de lotes para Azure SQL Database e Azure SQL Managed Instance:

* Existe uma latência potencialmente maior na rede no acesso à Base de Dados Azure SQL e à Azure SQL Managed Instance, especialmente se estiver a aceder à Base de Dados Azure SQL ou ao Azure SQL Managed Instance de fora do mesmo centro de dados da Microsoft Azure.
* As características multitenantes da Base de Dados Azure SQL e da Azure SQL Managed Instance significam que a eficiência da camada de acesso aos dados está correlacionada com a escalabilidade global da base de dados. Em resposta ao uso superior a quotas predefinidas, a Base de Dados Azure SQL e a Azure SQL Managed Instance podem reduzir a produção ou responder com exceções de estrangulamento. Eficiências, como o loteamento, permitem-lhe fazer mais trabalho antes de atingir estes limites.
* O loteamento também é eficaz para arquiteturas que usam várias bases de dados (fragmentos). A eficiência da sua interação com cada unidade de base de dados ainda é um fator chave na sua escalabilidade global.

Um dos benefícios da utilização da Base de Dados Azure SQL ou da Azure SQL Managed Instance é que não tem de gerir os servidores que hospedam a base de dados. No entanto, esta infraestrutura gerida também significa que você tem que pensar de forma diferente sobre otimizações de bases de dados. Já não é possível procurar melhorar o hardware da base de dados ou a infraestrutura de rede. O Microsoft Azure controla esses ambientes. A área principal que pode controlar é como a sua aplicação interage com a Base de Dados Azure SQL e a Azure SQL Managed Instance. O loteamento é uma destas otimizações.

A primeira parte deste artigo examina várias técnicas de loteamento para aplicações .NET que utilizam a Base de Dados Azure SQL ou Azure SQL Managed Instance. As duas últimas secções abrangem as diretrizes e cenários de loteamento.

## <a name="batching-strategies"></a>Estratégias de loteamento

### <a name="note-about-timing-results-in-this-article"></a>Nota sobre os resultados do tempo neste artigo

> [!NOTE]
> Os resultados não são referências, mas destinam-se a mostrar **um desempenho relativo.** Os horários baseiam-se numa média de pelo menos 10 ensaios. As operações são inseridas numa mesa vazia. Estes testes foram medidos antes de V12, e não correspondem necessariamente à produção que poderá experimentar numa base de dados V12 utilizando os novos [níveis de serviço DTU](database/service-tiers-dtu.md) ou [os níveis de serviço vCore](database/service-tiers-vcore.md). O benefício relativo da técnica de loteamento deve ser semelhante.

### <a name="transactions"></a>Transações

Parece estranho começar uma revisão do loteando discutindo transações. Mas o uso de transações do lado do cliente tem um efeito de lote sutil do lado do servidor que melhora o desempenho. E as transações podem ser adicionadas com apenas algumas linhas de código, por isso fornecem uma forma rápida de melhorar o desempenho das operações sequenciais.

Considere o seguinte código C# que contém uma sequência de operações de inserção e atualização numa tabela simples.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```

O seguinte código ADO.NET sequencialmente executa estas operações.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

A melhor forma de otimizar este código é implementar alguma forma de loteamento do lado do cliente destas chamadas. Mas há uma maneira simples de aumentar o desempenho deste código simplesmente envolvendo a sequência de chamadas numa transação. Aqui está o mesmo código que usa uma transação.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

As transações estão, na verdade, a ser utilizadas em ambos os exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita encerra todas as chamadas. De acordo com a documentação para o [registo de transações por escrito,](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)os registos de registo são descarregados para o disco quando a transação se compromete. Assim, ao incluir mais chamadas numa transação, a escrita para o registo de transações pode atrasar até que a transação seja comprometida. Com efeito, está a permitir o loteamento para as gravações no registo de transações do servidor.

A tabela a seguir mostra alguns resultados de testes ad hoc. Os testes realizaram as mesmas inserções sequenciais com e sem transações. Para uma perspetiva mais alargada, o primeiro conjunto de testes foi realizado remotamente de um portátil para a base de dados no Microsoft Azure. O segundo conjunto de testes foi a partir de um serviço de nuvem e base de dados que ambos residiam dentro do mesmo centro de dados da Microsoft Azure (West US). O quadro seguinte mostra a duração em milissegundos de inserções sequenciais com e sem transações.

**No local de Azure:**

| Operações | Nenhuma transação (ms) | Transação (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure to Azure (mesmo datacenter)** :

| Operações | Nenhuma transação (ms) | Transação (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Os resultados não são referências. Consulte a [nota sobre os resultados do tempo neste artigo.](#note-about-timing-results-in-this-article)

Com base nos resultados dos testes anteriores, embrulhar uma única operação numa transação diminui o desempenho. Mas à medida que aumenta o número de operações numa única transação, a melhoria do desempenho torna-se mais acentuada. A diferença de desempenho também é mais percetível quando todas as operações ocorrem dentro do datacenter microsoft Azure. O aumento da latência da utilização da Base de Dados Azure SQL ou do Azure SQL Managed Instance de fora do Centro de Dados microsoft Azure ofusca o ganho de desempenho da utilização de transações.

Embora a utilização de transações possa aumentar o desempenho, continue a [observar as melhores práticas para transações e ligações.](/previous-versions/sql/sql-server-2008-r2/ms187484(v=sql.105)) Mantenha a transação o mais curta possível e feche a ligação da base de dados após o trabalho concluído. A declaração de utilização no exemplo anterior assegura que a ligação está fechada quando o bloco de código subsequente estiver concluído.

O exemplo anterior demonstra que pode adicionar uma transação local a qualquer código ADO.NET com duas linhas. As transações oferecem uma forma rápida de melhorar o desempenho do código que torna as operações de inserção sequencial, atualização e eliminação. No entanto, para o desempenho mais rápido, considere alterar ainda mais o código para tirar partido do loteamento do lado do cliente, como parâmetros valorizados pela tabela.

Para obter mais informações sobre transações em ADO.NET, consulte [As Transações Locais em ADO.NET.](/dotnet/framework/data/adonet/local-transactions)

### <a name="table-valued-parameters"></a>Parâmetros de valor de tabela

Os parâmetros valorizados pela tabela suportam os tipos de tabela definidos pelo utilizador como parâmetros em declarações Transact-SQL, procedimentos armazenados e funções. Esta técnica de loteamento do lado do cliente permite-lhe enviar várias linhas de dados dentro do parâmetro valorado da tabela. Para utilizar parâmetros valorizados por tabela, primeiro defina um tipo de tabela. A seguinte declaração Transact-SQL cria um tipo de tabela chamado **MyTableType** .

```sql
    CREATE TYPE MyTableType AS TABLE
    ( mytext TEXT,
      num INT );
```

Em código, cria-se uma **DataTable** com os mesmos nomes e tipos do tipo de tabela. Passe esta **DataTable** num parâmetro numa consulta de texto ou chamada de procedimento armazenado. O exemplo a seguir mostra esta técnica:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

No exemplo anterior, o objeto **SqlCommand** insere linhas a partir de um parâmetro de valor de mesa, **\@ TestTvp** . O objeto **DataTable** anteriormente criado é atribuído a este parâmetro com o método **SqlCommand.Parâmetros.Add.** O loteamento dos encaixes numa chamada aumenta significativamente o desempenho sobre as inserções sequenciais.

Para melhorar ainda mais o exemplo anterior, utilize um procedimento armazenado em vez de um comando baseado em texto. O seguinte comando Transact-SQL cria um procedimento armazenado que requer o parâmetro de tabela **SimpleTestTableType.**

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows]
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num)
SELECT mytext, num FROM @TestTvp
END
GO
```

Em seguida, altere a declaração de objeto **SqlCommand** no exemplo de código anterior para o seguinte.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

Na maioria dos casos, os parâmetros valorizados pela tabela têm um desempenho equivalente ou melhor do que outras técnicas de loteamento. Os parâmetros valorizados pela tabela são muitas vezes preferíveis, porque são mais flexíveis do que outras opções. Por exemplo, outras técnicas, como a cópia a granel SQL, apenas permitem a inserção de novas linhas. Mas com parâmetros valorizados pela tabela, pode utilizar a lógica no procedimento armazenado para determinar quais as linhas que são atualizações e quais são inserções. O tipo de tabela também pode ser modificado para conter uma coluna "Operação" que indique se a linha especificada deve ser inserida, atualizada ou eliminada.

A tabela a seguir mostra os resultados dos testes ad hoc para a utilização de parâmetros valorizados pela tabela em milissegundos.

| Operações | No local para Azure (ms) | Azure mesmo datacenter (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Os resultados não são referências. Consulte a [nota sobre os resultados do tempo neste artigo.](#note-about-timing-results-in-this-article)

O ganho de desempenho do loteamento é imediatamente aparente. No teste sequencial anterior, 1000 operações demoraram 129 segundos fora do datacenter e a 21 segundos do centro de dados. Mas com parâmetros valorizados pela tabela, 1000 operações demoram apenas 2,6 segundos fora do datacenter e 0,4 segundos dentro do datacenter.

Para obter mais informações sobre parâmetros valorizados por tabela, consulte [parâmetros valorizados por tabela](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

### <a name="sql-bulk-copy"></a>Cópia a granel SQL

A cópia a granel SQL é outra forma de inserir grandes quantidades de dados numa base de dados-alvo. .Net aplicações podem usar a classe **SqlBulkCopy** para realizar operações de inserção a granel. **SqlBulkCopy** é semelhante em função à ferramenta de linha de comando, **Bcp.exe,** ou à declaração Transact-SQL, **BULK INSERT** . O exemplo de código que se segue mostra como copiar em massa as linhas na **datatable** de origem, tabela, para a tabela de destino, MyTable.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Há alguns casos em que a cópia a granel é preferida sobre parâmetros valorizados pela tabela. Consulte a tabela de comparação dos parâmetros Table-Valued versus OPERAções DE INSERÇÃO A GRANEL no artigo [Parâmetros valorizados por quadro](/sql/relational-databases/tables/use-table-valued-parameters-database-engine).

Os seguintes resultados dos testes ad hoc mostram o desempenho do lote com **SqlBulkCopy** em milissegundos.

| Operações | No local para Azure (ms) | Azure mesmo datacenter (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Os resultados não são referências. Consulte a [nota sobre os resultados do tempo neste artigo.](#note-about-timing-results-in-this-article)

Em tamanhos de lote mais pequenos, os parâmetros de uso avaliados em tabela superaram a classe **SqlBulkCopy.** No entanto, **a SqlBulkCopy** realizou 12-31% mais rápido do que os parâmetros valorizados pela tabela para os testes de 1.000 e 10.000 linhas. Tal como os parâmetros valorizados pela tabela, **a SqlBulkCopy** é uma boa opção para inserções em lote, especialmente quando comparada com o desempenho de operações não em lote.

Para obter mais informações sobre cópias a granel em ADO.NET, consulte [operações de cópia em massa](/dotnet/framework/data/adonet/sql/bulk-copy-operations-in-sql-server).

### <a name="multiple-row-parameterized-insert-statements"></a>Declarações insiras parametrizadas de várias linhas

Uma alternativa para pequenos lotes é construir uma grande declaração de INSERÇÃO parametrizada que insere várias linhas. O seguinte exemplo de código demonstra esta técnica.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Este exemplo pretende mostrar o conceito básico. Um cenário mais realista passaria pelas entidades necessárias para construir simultaneamente a cadeia de consulta e os parâmetros de comando. Você está limitado a um total de 2100 parâmetros de consulta, o que limita o número total de linhas que podem ser processadas desta forma.

Os seguintes resultados dos testes ad hoc mostram o desempenho deste tipo de declaração de inserção em milissegundos.

| Operações | Parâmetros valorizados por tabela (ms) | INSERÇÃO DE Declaração Única (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Os resultados não são referências. Consulte a [nota sobre os resultados do tempo neste artigo.](#note-about-timing-results-in-this-article)

Esta abordagem pode ser ligeiramente mais rápida para lotes com menos de 100 linhas. Embora a melhoria seja pequena, esta técnica é outra opção que pode funcionar bem no seu cenário específico de aplicação.

### <a name="dataadapter"></a>DataAdapter

A classe **DataAdapter** permite modificar um objeto **DataSet** e, em seguida, submeter as alterações como operações INSERT, UPDATE e DELETE. Se estiver a utilizar o **DataAdapter** desta forma, é importante notar que são feitas chamadas separadas para cada operação distinta. Para melhorar o desempenho, utilize a propriedade **UpdateBatchSize** para o número de operações que devem ser lotadas de cada vez. Para obter mais informações, consulte [a realização de operações de lote utilizando dataAdapters](/dotnet/framework/data/adonet/performing-batch-operations-using-dataadapters).

### <a name="entity-framework"></a>Entity Framework

[O Núcleo-Quadro da Entidade](/ef/efcore-and-ef6/#saving-data) suporta o loteamento.

### <a name="xml"></a>XML

Para a completude, sentimos que é importante falar sobre xML como uma estratégia de loteamento. No entanto, a utilização de XML não tem vantagens sobre outros métodos e várias desvantagens. A abordagem é semelhante aos parâmetros valorizados pela tabela, mas um ficheiro ou cadeia XML é passado para um procedimento armazenado em vez de uma tabela definida pelo utilizador. O procedimento armazenado analisa os comandos no procedimento armazenado.

Há várias desvantagens nesta abordagem:

* Trabalhar com XML pode ser complicado e propenso a erros.
* A análise do XML na base de dados pode ser intensiva em CPU.
* Na maioria dos casos, este método é mais lento do que os parâmetros valorizados pela tabela.

Por estas razões, não é recomendada a utilização de XML para consultas de lote.

## <a name="batching-considerations"></a>Considerações de loteamento

As seguintes secções fornecem mais orientação para a utilização de lotes em aplicações Azure SQL Management Instance.

### <a name="tradeoffs"></a>Vantagens e desvantagens

Dependendo da sua arquitetura, o lote pode envolver uma troca entre desempenho e resiliência. Por exemplo, considere o cenário em que o seu papel inesperadamente cai. Se perder uma linha de dados, o impacto é menor do que o impacto de perder um grande lote de linhas não submetidas. Existe um maior risco quando se faz fila de tampão antes de enviá-los para a base de dados numa janela de tempo especificada.

Por causa desta troca, avalie o tipo de operações que você lote. Lote mais agressivamente (lotes maiores e janelas de tempo mais longos) com dados menos críticos.

### <a name="batch-size"></a>Tamanho do lote

Nos nossos testes, normalmente não havia vantagem em partir grandes lotes em pedaços menores. Na verdade, esta subdivisão resultou frequentemente num desempenho mais lento do que em apresentar um único lote grande. Por exemplo, considere um cenário em que queira inserir 1000 linhas. A tabela a seguir mostra o tempo que demora a utilizar parâmetros valorizados para inserir 1000 linhas quando divididos em lotes menores.

| Tamanho do lote | Iterações | Parâmetros valorizados por tabela (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Os resultados não são referências. Consulte a [nota sobre os resultados do tempo neste artigo.](#note-about-timing-results-in-this-article)

Pode ver que a melhor performance para 1000 linhas é submetê-las todas de uma vez. Em outros testes (não mostrados aqui), houve um pequeno ganho de desempenho para quebrar um lote de 10000 linhas em dois lotes de 5000. Mas o esquema de tabela para estes testes é relativamente simples, por isso deve realizar testes nos seus dados específicos e tamanhos de lote para verificar estes resultados.

Outro fator a ter em conta é que, se o lote total se tornar demasiado grande, a Base de Dados Azure SQL ou a Azure SQL Managed Instance podem acelerar e recusar-se a comprometer o lote. Para obter os melhores resultados, teste o seu cenário específico para determinar se existe um tamanho ideal do lote. Configure o tamanho do lote no tempo de execução para permitir ajustes rápidos com base no desempenho ou erros.

Finalmente, equilibre o tamanho do lote com os riscos associados ao lote. Se houver erros transitórios ou o papel falhar, considere as consequências de voltar a tentar a operação ou de perder os dados no lote.

### <a name="parallel-processing"></a>Processamento paralelo

E se tivesses a abordagem de reduzir o tamanho do lote, mas usasses vários fios para executar o trabalho? Mais uma vez, os nossos testes mostraram que vários lotes multi-leituras menores normalmente executavam pior do que um único lote maior. As seguintes tentativas de ensaio para inserir 1000 linhas em um ou mais lotes paralelos. Este teste mostra como os lotes mais simultâneos diminuíram o desempenho.

| Tamanho do lote [Iterações] | Dois fios (ms) | Quatro fios (ms) | Seis fios (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Os resultados não são referências. Consulte a [nota sobre os resultados do tempo neste artigo.](#note-about-timing-results-in-this-article)

Existem várias razões potenciais para a degradação do desempenho devido ao paralelismo:

* Há várias chamadas simultâneas de rede em vez de uma.
* Múltiplas operações contra uma única tabela podem resultar em contenção e bloqueio.
* Há despesas gerais associadas a multi-leituras.
* A despesa de abertura de múltiplas ligações supera o benefício do processamento paralelo.

Se você direciona diferentes tabelas ou bases de dados, é possível ver algum ganho de desempenho com esta estratégia. O fragmento de base de dados ou as federações seriam um cenário para esta abordagem. O Sharding utiliza várias bases de dados e encaminha dados diferentes para cada base de dados. Se cada pequeno lote for para uma base de dados diferente, então realizar as operações em paralelo pode ser mais eficiente. No entanto, o ganho de desempenho não é significativo o suficiente para ser usado como base para uma decisão de utilização de fragmentos de base de dados na sua solução.

Em alguns desenhos, a execução paralela de lotes menores pode resultar numa melhor produção de pedidos num sistema em carga. Neste caso, embora seja mais rápido processar um único lote maior, processar vários lotes em paralelo pode ser mais eficiente.

Se utilizar a execução paralela, considere controlar o número máximo de fios de trabalhador. Um número menor pode resultar em menos contenção e um tempo de execução mais rápido. Além disso, considere a carga adicional que isto coloca na base de dados-alvo, tanto em conexões como em transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados

A orientação típica sobre o desempenho da base de dados também afeta o lote. Por exemplo, o desempenho do inserção é reduzido para tabelas que têm uma grande chave primária ou muitos índices não aglomerados.

Se os parâmetros valorizados pela tabela utilizarem um procedimento armazenado, pode utilizar o comando **SET NOCOUNT ON no** início do procedimento. Esta declaração suprime o regresso da contagem das linhas afetadas no procedimento. No entanto, nos nossos testes, a utilização de **SET NOCOUNT ON** não teve efeito ou diminuiu o desempenho. O procedimento de teste armazenado era simples com um único comando **INSERT** a partir do parâmetro valorado da tabela. É possível que procedimentos armazenados mais complexos beneficiem desta declaração. Mas não assuma que adicionar **SET NOCOUNT ON** ao seu procedimento armazenado melhora automaticamente o desempenho. Para compreender o efeito, teste o seu procedimento armazenado com e sem a declaração **DE NOCOUNT ON.**

## <a name="batching-scenarios"></a>Cenários de loteamento

As secções seguintes descrevem como utilizar parâmetros valorizados por tabela em três cenários de aplicação. O primeiro cenário mostra como o tampão e o lote podem funcionar em conjunto. O segundo cenário melhora o desempenho através da realização de operações de master-detail numa única chamada de procedimento armazenada. O cenário final mostra como usar parâmetros valorizados por tabela numa operação "UPSERT".

### <a name="buffering"></a>Buffering

Embora existam alguns cenários que são óbvios candidatos ao lote, existem muitos cenários que poderiam tirar partido do loteamento por processamento atrasado. No entanto, o processamento atrasado também comporta um maior risco de perda de dados em caso de falha inesperada. É importante compreender este risco e considerar as consequências.

Por exemplo, considere uma aplicação web que rastreie o histórico de navegação de cada utilizador. Em cada pedido de página, a aplicação poderia fazer uma chamada de base de dados para registar a visualização da página do utilizador. Mas um maior desempenho e escalabilidade podem ser alcançados através do tamponamento das atividades de navegação dos utilizadores e, em seguida, enviando estes dados para a base de dados em lotes. Pode ativar a atualização da base de dados pelo tempo e/ou tamanho do tampão decorridos. Por exemplo, uma regra pode especificar que o lote deve ser processado após 20 segundos ou quando o tampão atinge 1000 itens.

O exemplo de código a seguir utiliza [extensões reativas - Rx](/previous-versions/dotnet/reactive-extensions/hh242985(v=vs.103)) para processar eventos tamponados levantados por uma classe de monitorização. Quando o tampão preenche ou é atingido um tempo limite, o lote de dados do utilizador é enviado para a base de dados com um parâmetro de valor de tabela.

A classe NavHistoryData modela os detalhes de navegação do utilizador. Contém informações básicas como o identificador do utilizador, o URL acedido e o tempo de acesso.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

A classe NavHistoryDataMonitor é responsável por tamponar os dados de navegação do utilizador para a base de dados. Contém um método, RecordUserNavigationEntry, que responde através da elevação de um evento **OnAdded.** O código a seguir mostra a lógica do construtor que utiliza o Rx para criar uma coleção observável com base no evento. Em seguida, subscreve esta coleção observável com o método Buffer. A sobrecarga especifica que o tampão deve ser enviado a cada 20 segundos ou 1000 entradas.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

O manipulador converte todos os itens tampão em um tipo de valor de mesa e, em seguida, passa este tipo para um procedimento armazenado que processa o lote. O código que se segue mostra a definição completa tanto para as classes NavHistoryDataEventArgs como para as classes NavHistoryDataMonitor.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

O manipulador converte todos os itens tampão em um tipo de valor de mesa e, em seguida, passa este tipo para um procedimento armazenado que processa o lote. O código que se segue mostra a definição completa tanto para as classes NavHistoryDataEventArgs como para as classes NavHistoryDataMonitor.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Para utilizar esta classe de tampão, a aplicação cria um objeto Estático NavHistoryDataMonitor. Sempre que um utilizador acede a uma página, a aplicação chama o método NavHistoryDataMonitor.RecordUserNavigationEntry. A lógica de tamponamento continua a tratar do envio destas entradas para a base de dados em lotes.

### <a name="master-detail"></a>Detalhe principal

Os parâmetros valorizados pela tabela são úteis para cenários simples de INSERÇÃO. No entanto, pode ser mais desafiante para inserções de lotes que envolvem mais do que uma tabela. O cenário "mestre/detalhe" é um bom exemplo. A mesa principal identifica a entidade primária. Uma ou mais tabelas de detalhes armazenam mais dados sobre a entidade. Neste cenário, as relações-chave estrangeiras impõem a relação dos detalhes a uma entidade-mestre única. Considere uma versão simplificada de uma tabela PurchaseOrder e a sua tabela OrderDetail associada. O seguinte Transact-SQL cria a tabela PurchaseOrder com quatro colunas: OrderID, OrderDate, CustomerID e Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder]
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Cada encomenda contém uma ou mais compras de produtos. Esta informação é captada na tabela PurchaseOrderDetail. O seguinte Transact-SQL cria a tabela PurchaseOrderDetail com cinco colunas: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED
( [OrderID] ASC, [OrderDetailID] ASC ))
```

A coluna OrderID na tabela PurchaseOrderDetail deve fazer referência a uma encomenda da tabela PurchaseOrder. A seguinte definição de chave estrangeira impõe este constrangimento.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Para utilizar parâmetros valorizados por tabela, deve ter um tipo de tabela definido pelo utilizador para cada tabela-alvo.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Em seguida, defina um procedimento armazenado que aceite tabelas deste tipo. Este procedimento permite que uma aplicação desemalte localmente um conjunto de encomendas e detalhes de encomenda numa única chamada. O seguinte Transact-SQL fornece a declaração de procedimento completa armazenada para este exemplo de pedido de compra.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE (
SubmittedKey int,
ActualKey int,
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table,
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

Neste exemplo, a tabela definida localmente @IdentityLink armazena os valores reais do OrderID das linhas recém-inseridas. Estes identificadores de ordem são diferentes dos valores temporários de OrderID nos @orders parâmetros e @details valorizados da tabela. Por esta razão, a @IdentityLink tabela liga então os valores OrderID do @orders parâmetro aos valores reais do OrderID para as novas linhas na tabela PurchaseOrder. Após este passo, a @IdentityLink tabela pode facilitar a inserção dos detalhes da encomenda com o verdadeiro OrderID que satisfaz a restrição de chave estrangeira.

Este procedimento armazenado pode ser utilizado a partir de código ou de outras chamadas Transact-SQL. Consulte a secção de parâmetros valorizados por tabela deste artigo para obter um exemplo de código. O seguinte Transact-SQL mostra como chamar o sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Esta solução permite que cada lote utilize um conjunto de valores OrderID que começam em 1. Estes valores temporários de OrderID descrevem as relações no lote, mas os valores reais do OrderID são determinados no momento da operação de inserção. Pode executar as mesmas declarações no exemplo anterior repetidamente e gerar encomendas únicas na base de dados. Por esta razão, considere adicionar mais lógica de código ou base de dados que impeça a duplicação de encomendas ao utilizar esta técnica de loteamento.

Este exemplo demonstra que operações de base de dados ainda mais complexas, como operações de detalhe principal, podem ser em lote usando parâmetros de valor de tabela.

### <a name="upsert"></a>UPSERT

Outro cenário de loteamento envolve simultaneamente a atualização das linhas existentes e a inserção de novas linhas. Esta operação é por vezes referida como uma operação "UPSERT" (atualização + inserção). Em vez de fazer chamadas separadas para INSERIR e ATUALIZAR, a declaração MERGE é mais adequada para esta tarefa. A declaração MERGE pode efetuar operações de inserção e atualização numa única chamada.

Os parâmetros valorizados pelo quadro podem ser utilizados com a declaração MERGE para executar atualizações e inserções. Por exemplo, considere uma tabela de empregados simplificada que contenha as seguintes colunas: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED
([EmployeeID] ASC ))
```

Neste exemplo, pode utilizar o facto de que o SocialSecurityNumber é único para realizar uma FUSÃO de vários colaboradores. Primeiro, crie o tipo de tabela definido pelo utilizador:

```sql
CREATE TYPE EmployeeTableType AS TABLE
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Em seguida, crie um procedimento armazenado ou escreva código que utilize a declaração MERGE para executar a atualização e inserir. O exemplo a seguir utiliza a declaração MERGE num parâmetro de valor de @employees tabela, do tipo EmployeeTableType. O conteúdo da @employees tabela não é mostrado aqui.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees)
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN
UPDATE SET
target.FirstName = source.FirstName,
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Para obter mais informações, consulte a documentação e os exemplos para a declaração da MERGE. Embora o mesmo trabalho possa ser realizado numa chamada de procedimento armazenado em várias etapas com operações separadas de INSERT e UPDATE, a declaração MERGE é mais eficiente. O código de base de dados também pode construir chamadas Transact-SQL que utilizam a declaração MERGE diretamente sem exigir duas chamadas de base de dados para INSERIR e ATUALIZAR.

## <a name="recommendation-summary"></a>Resumo da recomendação

A lista que se segue apresenta um resumo das recomendações de loteamento discutidas neste artigo:

* Utilize tampão e lotação para aumentar o desempenho e escalabilidade das aplicações Azure SQL Management Instance.
* Compreenda as trocas entre o lote/tampão e a resiliência. Durante uma falha de função, o risco de perder um lote não processado de dados críticos de negócio pode superar o benefício de desempenho do loteamento.
* Tente manter todas as chamadas para a base de dados dentro de um único datacenter para reduzir a latência.
* Se escolher uma única técnica de loteamento, os parâmetros de valor de mesa oferecem o melhor desempenho e flexibilidade.
* Para obter o desempenho de inserção mais rápido, siga estas diretrizes gerais, mas teste o seu cenário:
  * Para < 100 linhas, utilize um único comando INSERT parametrizado.
  * Para < 1000 linhas, utilize parâmetros de valor de mesa.
  * Para >= 1000 linhas, utilize o SqlBulkCopy.
* Para atualizar e eliminar operações, utilize parâmetros de valor de tabela com lógica de procedimento armazenado que determine o funcionamento correto em cada linha do parâmetro da tabela.
* Diretrizes sobre o tamanho do lote:
  * Utilize os maiores tamanhos de lote que fazem sentido para a sua aplicação e requisitos de negócio.
  * Equilibrar o ganho de desempenho de grandes lotes com os riscos de falhas temporárias ou catastróficas. Qual é a consequência de retrós assim ou perda dos dados no lote?
  * Teste o maior tamanho do lote para verificar se a Base de Dados Azure SQL ou a Azure SQL Managed Instance não a rejeita.
  * Crie definições de configuração que controlem o lote, como o tamanho do lote ou a janela de tempo de tampão. Estas definições proporcionam flexibilidade. Pode alterar o comportamento de loteamento na produção sem recolocar o serviço na nuvem.
* Evite a execução paralela de lotes que operam numa única tabela numa base de dados. Se optar por dividir um único lote por várias linhas de trabalho, faça testes para determinar o número ideal de fios. Após um limiar não especificado, mais fios diminuirão o desempenho em vez de o aumentarem.
* Considere o tampão no tamanho e no tempo como uma forma de implementar o loteamento para mais cenários.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se na forma como o design da base de dados e as técnicas de codificação relacionadas com o lote podem melhorar o desempenho da sua aplicação e a escalabilidade. Mas este é apenas um fator na sua estratégia geral. Para obter mais formas de melhorar o desempenho e a escalabilidade, consulte [a orientação de desempenho da Base de Dados](database/performance-guidance.md) e [considerações de preço e desempenho para uma piscina elástica.](database/elastic-pool-overview.md)