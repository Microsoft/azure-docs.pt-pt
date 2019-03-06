---
title: Como utilizar a criação de batches de mensagens em fila para melhorar o desempenho de aplicações de base de dados do Azure SQL
description: O tópico fornece uma evidência que criação de batches de operações de base de dados bastante melhora a velocidade e escalabilidade das suas aplicações de base de dados do Azure SQL. Embora essas técnicas de criação de batches funcionam para qualquer base de dados do SQL Server, o foco deste artigo está no Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 0ef6d258be0165c7a73ce060879f55f1c7f404f9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453529"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Como utilizar a criação de batches de mensagens em fila para melhorar o desempenho de aplicações de base de dados SQL

Criação de batches de operações para a base de dados do Azure SQL significativamente melhora o desempenho e escalabilidade das suas aplicações. Para compreender as vantagens, a primeira parte deste artigo aborda alguns resultados de teste de exemplo que comparam seqüenciais e em lote pedidos para uma base de dados SQL. O restante do artigo mostra as técnicas, cenários e considerações para ajudá-lo a utilizar a criação de batches com êxito nas suas aplicações do Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Por que é criação de batches importante para a base de dados SQL

Criação de batches de chamadas para um serviço remoto é uma estratégia bem conhecida para aumentar o desempenho e escalabilidade. É corrigido que são os custos de processamento para todas as interações com um serviço remoto, como serialização e desserialização transferência de rede. Empacotar várias transações separadas num único lote minimiza esses custos.

Neste documento, queremos examinar a base de dados do SQL vários cenários e estratégias de criação de batches. Embora essas estratégias também são importantes para aplicações no local que utilizam o SQL Server, existem vários motivos para realçar a utilização de criação de batches de base de dados SQL:

* Existe uma latência de rede potencialmente maior no acesso à base de dados SQL, especialmente se estiver a aceder à base de dados SQL de fora do mesmo datacenter do Microsoft Azure.
* As características de multi-inquilino da base de dados SQL, significa que a eficiência da dados acesso camada está correlacionada com a escalabilidade geral da base de dados. Base de dados SQL tem de impedir que qualquer utilizador/inquilino único monopolizem os recursos de base de dados para o dano de outros inquilinos. Em resposta a utilização em excesso de quotas predefinidas, a base de dados SQL pode reduzir o débito ou responder com exceções de limitação. Eficiências, como o processamento em lote, permitem que faça mais de trabalho na base de dados do SQL antes de atingir estes limites. 
* Processamento em lotes também está em vigor para arquiteturas que utilizam várias bases de dados (fragmentação). A eficiência da sua interação com cada unidade de banco de dados ainda é um fator importante na sua escalabilidade geral. 

Uma das vantagens da utilização de base de dados SQL é que não precisa de gerir os servidores que alojam a base de dados. No entanto, esta infraestrutura gerida também significa que precisa pensar de forma diferente sobre otimizações de base de dados. Já não pode procurar para melhorar a infraestrutura de rede ou de hardware de base de dados. Microsoft Azure controla esses ambientes. A principal área que pode controlar é como seu aplicativo interage com a base de dados SQL. Criação de batches é um dessas otimizações. 

A primeira parte do documento examina várias técnicas de criação de batches para aplicações de .NET que utilizam a base de dados SQL. As últimas duas secções abrangem cenários e as diretrizes de criação de batches.

## <a name="batching-strategies"></a>Estratégias de criação de batches

### <a name="note-about-timing-results-in-this-article"></a>Tenha em atenção sobre os resultados de temporização neste artigo

> [!NOTE]
> Resultados não são parâmetros de comparação, mas devem mostrar **desempenho relativo**. Temporizações baseiam-se uma média de, pelo menos, 10 execuções de teste. As operações são inserções numa tabela vazia. Esses testes foram medidos pré-V12, e não necessariamente correspondem às débito que podem ocorrer numa base de dados V12 utilizando o novo [escalões de serviço DTU](sql-database-service-tiers-dtu.md) ou [escalões de serviço de vCore](sql-database-service-tiers-vcore.md). O benefício relativo da criação de batches técnica deve ser semelhante.

### <a name="transactions"></a>Transações

Parece estranho para iniciar uma revisão de processamento em lote, discutindo as transações. Mas o uso de transações do lado do cliente tem um efeito de criação de batches do lado do servidor sutil que melhora o desempenho. E transações podem ser adicionadas com apenas algumas linhas de código, para que eles fornecem uma maneira rápida para melhorar o desempenho de operações seqüenciais.

Considere o seguinte código c# que contém uma seqüência de inserção e operações numa tabela simple de atualização.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
O seguinte código ADO.NET executa sequencialmente estas operações.

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

A melhor forma de otimizar esse código é implementar alguma forma de criação de batches de lado do cliente dessas chamadas. Mas há uma forma simples de aumentar o desempenho desse código, simplesmente envolvendo a sequência de chamadas numa transação. Aqui está o mesmo código que utiliza uma transação.

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

Transações, na verdade, estão a ser utilizadas em ambos estes exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita encapsula todas as chamadas. Pela documentação para o [registo de transações de escrita-ahead](https://msdn.microsoft.com/library/ms186259.aspx), registros de log são libertados para o disco quando a transação seja confirmada. Portanto, ao incluir mais chamadas numa transação, a gravação no log de transação pode atrasar até que a transação será confirmada. Na verdade, pretende ativar a criação de batches das escritas para o log de transação do servidor.

A tabela seguinte mostra alguns resultados de testes ad hoc. Os testes de efetuar as inserções sequenciais mesmo com e sem transações. Para o ponto de vista mais, o primeiro conjunto de testes foi executada remotamente de um laptop para a base de dados no Microsoft Azure. O segundo conjunto de testes foi executada a partir de um serviço em nuvem e a base de dados que ambos residia no mesmo datacenter do Microsoft Azure (E.U.A. oeste). A tabela seguinte mostra a duração em milissegundos de inserções seqüenciais com e sem transações.

**No local para o Azure**:

| Operações | Não existem transações (ms) | Transações (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure para o Azure (mesmo datacenter)**:

| Operações | Não existem transações (ms) | Transações (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [observação sobre os resultados de temporização neste artigo](#note-about-timing-results-in-this-article).

Com base nos resultados de teste anteriores, envolvendo uma única operação numa transação, na verdade, reduz o desempenho. Mas, à medida que aumenta o número de operações numa única transação, o melhor desempenho se torna mais marcado. A diferença de desempenho também é mais perceptível quando todas as operações ocorrem dentro do datacenter do Microsoft Azure. A latência de aumento da utilização de base de dados SQL de fora do datacenter do Microsoft Azure overshadows o ganho de desempenho do uso de transações.

Embora o uso de transações pode aumentar o desempenho, continuar a [observar as práticas recomendadas para ligações de transações e](https://msdn.microsoft.com/library/ms187484.aspx). Mantenha a transação o mais curta possível e feche a ligação de base de dados após a conclusão do trabalho. A utilizar a instrução no exemplo anterior assegura que a conexão é fechada quando o bloco de código subsequente é concluída.

O exemplo anterior demonstra que pode adicionar uma transação local para qualquer código ADO.NET com duas linhas. Transações oferecem uma forma rápida de melhorar o desempenho do código que faz sequencial inserir, atualizar e eliminar operações. No entanto, para obter um desempenho mais rápido, considere alterar o código mais tire partido do lado do cliente processamento em lote, tais como parâmetros de valor de tabela.

Para obter mais informações sobre transações no ADO.NET, consulte [transações locais no ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>parâmetros de valor de tabela

Parâmetros de valor de tabela suportam tipos de tabela definido pelo utilizador como parâmetros em instruções Transact-SQL, procedimentos armazenados e funções. Essa técnica de criação de batches de lado do cliente permite-lhe enviar várias linhas de dados em que o parâmetro de valor de tabela. Para utilizar os parâmetros de valor de tabela, primeiro defina um tipo de tabela. A instrução de Transact-SQL seguinte cria um tipo de tabela com o nome **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

No código, criar um **DataTable** com os mesmos nomes de exatos e tipos do tipo de tabela. Transmiti-la **DataTable** num parâmetro numa consulta de texto ou um procedimento armazenado chamar. O exemplo seguinte mostra esta técnica:

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

No exemplo anterior, o **SqlCommand** objeto insere linhas de um parâmetro de valor de tabela **@TestTvp**. Criado anteriormente **DataTable** será atribuído para este parâmetro com o **SqlCommand.Parameters.Add** método. Criação de batches significativamente as inserções numa chamada aumenta o desempenho ao longo de inserções seqüenciais.

Para melhorar ainda mais o exemplo anterior, utilize um procedimento armazenado em vez de um comando baseado em texto. O seguinte comando do Transact-SQL cria um procedimento armazenado que demora a **SimpleTestTableType** parâmetro de valor de tabela.

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

Em seguida, altere a **SqlCommand** objeto declaração no exemplo de código anterior para o seguinte.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

Na maioria dos casos, o valor de tabela parâmetros têm um desempenho equivalente ou melhor do que outras técnicas de criação de batches. Parâmetros de valor de tabela, muitas vezes, são preferíveis, porque são mais flexíveis do que outras opções. Por exemplo, outras técnicas, tais como cópia em massa SQL, apenas permitem a inserção de novas linhas. Mas com parâmetros de valor de tabela, pode utilizar lógica no procedimento armazenado para determinar quais linhas são atualizações e que são insere. O tipo de tabela também pode ser modificado para conter uma coluna de "Operação" que indica se a linha especificada deve ser inserida, atualizada ou excluída.

A tabela seguinte mostra os resultados do teste ad hoc para o uso de parâmetros de valor de tabela em milissegundos.

| Operações | No local para o Azure (ms) | Mesmo de datacenter do Azure (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [observação sobre os resultados de temporização neste artigo](#note-about-timing-results-in-this-article).
> 
> 

O ganho de desempenho do processamento em lote é imediatamente aparente. O teste sequencial anterior, 1000 operações demorou segundos 129 fora do datacenter e 21 segundos a partir do Centro de dados. Mas com parâmetros de valor de tabela, 1000 operações demorar apenas 2,6 segundos fora do datacenter e segundos 0.4 no Centro de dados.

Para obter mais informações sobre os parâmetros de valor de tabela, consulte [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Cópia em massa SQL

Cópia em massa SQL é outra forma de inserir grandes quantidades de dados num banco de dados de destino. As aplicações de .NET podem utilizar o **SqlBulkCopy** operações de inserção de classe para executar em massa. **SqlBulkCopy** é semelhante em função para a ferramenta de linha de comandos **Bcp.exe**, ou a instrução de Transact-SQL **BULK INSERT**. O exemplo de código seguinte mostra como cópia em massa as linhas na fonte **DataTable**, tabela, para a tabela de destino no SQL Server, MyTable.

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

Existem alguns casos em que a cópia em massa é preferencial através de parâmetros de valor de tabela. Consulte a tabela de comparação de parâmetros de valor de tabela de mensagens em fila em relação a operações de inserção em massa no artigo [Table-Valued parâmetros](https://msdn.microsoft.com/library/bb510489.aspx).

Os resultados de teste ad hoc seguintes mostram o desempenho de processamento em lote com **SqlBulkCopy** em milissegundos.

| Operações | No local para o Azure (ms) | Mesmo de datacenter do Azure (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [observação sobre os resultados de temporização neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Em tamanhos mais pequenos de lote, os parâmetros de valor de tabela de utilização superava o **SqlBulkCopy** classe. No entanto, **SqlBulkCopy** executadas mais rapidamente do que os parâmetros de valor de tabela % de 12-31 para os testes de 1000 e 10 000 linhas. Como parâmetros de valor de tabela, **SqlBulkCopy** é uma boa opção para inserções em lote, especialmente quando comparado com o desempenho de operações não colocar em lote.

Para obter mais informações sobre a cópia em massa no ADO.NET, consulte [operações de cópia em massa no SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instruções de parametrizado inserir várias linhas

Uma alternativa para pequenos lotes é construir uma instrução de inserção de parametrizado grandes que insere várias linhas. O exemplo de código seguinte demonstra essa técnica.

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

Este exemplo destina-se para mostrar o conceito básico. Um cenário mais realista seria fazer um loop por entidades necessárias para construir a cadeia de consulta e os parâmetros de comando em simultâneo. Está limitado a um total de parâmetros de consulta de 2100, portanto, isso limita o número total de linhas que pode ser processado dessa maneira.

Os resultados de teste ad hoc seguintes mostram o desempenho deste tipo de instrução insert em milissegundos.

| Operações | Parâmetros de valor de tabela de mensagens em fila (ms) | INSERÇÃO de declaração única (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [observação sobre os resultados de temporização neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Esta abordagem pode ser um pouco mais rápida para lotes que são menos de 100 linhas. Embora a melhoria é pequena, essa técnica é outra opção que pode funcionar bem no seu cenário de aplicação específica.

### <a name="dataadapter"></a>DataAdapter

O **DataAdapter** classe permite-lhe modificar um **conjunto de dados** de objeto e, em seguida, submeter as alterações como operações INSERT, UPDATE e DELETE. Se estiver a utilizar o **DataAdapter** desta forma, é importante observar que as chamadas separadas sejam feitas para cada operação distinta. Para melhorar o desempenho, utilize o **UpdateBatchSize** propriedade para o número de operações que devem ser loteados cada vez. Para obter mais informações, consulte [execução de Batch operações usando DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Estrutura de entidades

Estrutura de entidades não suporta atualmente a criação de batches. Diferentes desenvolvedores da Comunidade foi efetuada uma tentativa demonstrar as soluções alternativas, como substituição a **SaveChanges** método. Mas as soluções geralmente são complexos e personalizados para o aplicativo e o modelo de dados. O projeto do Entity Framework codeplex tem atualmente uma página de discussão sobre essa solicitação de recurso. Para ver esta discussão, consulte [anotações de reuniões de Design - 2 de Agosto de 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

Para ser completo, Achamos que é importante falar sobre o XML como uma estratégia de criação de batches. No entanto, o uso de XML tem nenhuma vantagem sobre outros métodos e várias desvantagens. A abordagem é semelhante aos parâmetros de valor de tabela, mas um arquivo XML ou uma cadeia de caracteres é passada para um procedimento armazenado em vez de uma tabela definida pelo utilizador. O procedimento armazenado analisa os comandos no procedimento armazenado.

Existem várias desvantagens nessa abordagem:

* Trabalhar com XML pode ser complicado e propenso a erros.
* Analisar o XML na base de dados pode ser intensiva da CPU.
* Na maioria dos casos, esse método é mais lento do que os parâmetros de valor de tabela.

Por esses motivos, o uso de XML para consultas em lote não é recomendado.

## <a name="batching-considerations"></a>Considerações sobre a criação de batches

As secções seguintes fornecem mais orientações para a utilização de criação de batches em aplicativos de base de dados SQL.

### <a name="tradeoffs"></a>Vantagens e desvantagens

Dependendo da sua arquitetura, o processamento em lote pode envolver uma compensação entre o desempenho e resiliência. Por exemplo, considere o cenário em que sua função inesperadamente fica inativo. Se perder uma linha de dados, o impacto é menor do que o impacto da perda de um lote grande de linhas unsubmitted. Existe um risco maior quando a memória intermédia linhas antes de os enviar para a base de dados numa janela de tempo especificado.

Devido a essa compensação, avalie o tipo de operações que o batch. Batch de forma mais agressiva (lotes maiores e mais tempo do windows de tempo) com dados que são menos críticos.

### <a name="batch-size"></a>Tamanho do batch

No nossos testes, normalmente, não havia nenhuma vantagem de divisão de lotes grandes em segmentos mais pequenos. Na verdade, muitas vezes, essa subdivisão resultou num desempenho mais lento do que submeter um único lote grande. Por exemplo, considere um cenário onde deseja inserir 1000 linhas. A tabela seguinte mostra o tempo que demora a utilizar os parâmetros de valor de tabela para inserir 1000 linhas quando são divididos em menores lotes.

| Tamanho do batch | Iterações | Parâmetros de valor de tabela de mensagens em fila (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [observação sobre os resultados de temporização neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Pode ver que o melhor desempenho para 1000 linhas é submetê-las ao mesmo tempo. Em outros testes (não mostrados aqui), Ocorreu um pequeno ganho de desempenho para dividir um lote de 10000 linha em dois lotes de 5000. Mas o esquema da tabela para esses testes é relativamente simples, pelo que deverá efetuar testes nos seus dados específicos e tamanhos de batch para verificar nessas descobertas.

Outro fator a ter em consideração é que, se o batch total ficar demasiado grande, base de dados SQL pode limitar e se recusar a consolidar o lote. Para obter melhores resultados, teste o seu cenário específico para determinar se existe um tamanho de lote ideal. Fazer com que o tamanho do lote configurável no tempo de execução para ativar os ajustes rápidos com base no desempenho ou erros.

Por fim, equilibrar o tamanho do batch com os riscos associados à criação de batches. Se existem erros transitórios ou a função falha, considere as conseqüências de repetir a operação ou de perda de dados no lote.

### <a name="parallel-processing"></a>Processamento paralelo

E se a abordagem de reduzir o tamanho do lote mas utilizado vários threads para executar o trabalho? Novamente, os nossos testes mostraram que vários lotes mais pequenos multithread, normalmente a realizadas pior do que um único lote maior. O seguinte teste tenta inserir 1000 linhas num ou mais lotes paralelas. Este teste mostra como mais lotes simultâneas, na verdade, diminuíram o desempenho.

| Tamanho do lote [iterações] | Dois threads (ms) | Quatro threads (ms) | Seis segmentos (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [observação sobre os resultados de temporização neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Existem vários motivos possíveis para a degradação do desempenho devido a paralelismo:

* Existem várias chamadas de rede simultâneas em vez de um.
* Várias operações em relação a uma única tabela podem resultar em contenção e a bloquear.
* Existem sobrecargas associadas multithread.
* A despesa de abrir várias ligações prevalece sobre o benefício de processamento paralelo.

Se destinar tabelas diferentes ou bases de dados, é possível ver alguns ganho com esta estratégia de desempenho. Fragmentação de base de dados ou federações seria um cenário para essa abordagem. Fragmentação utiliza várias bases de dados e encaminha dados diferentes para cada base de dados. Se cada lote pequeno para uma base de dados diferente, em seguida, efetuar as operações em paralelo pode ser mais eficiente. No entanto, o ganho de desempenho não é significativo o suficiente para utilizar como base para uma decisão para utilizar a fragmentação de base de dados na sua solução.

Em alguns designs, execução paralela de lotes mais pequenos pode resultar num melhor débito de pedidos num sistema sob carga. Neste caso, mesmo que seja mais rápido processar um único lote maior, processamento de vários lotes em paralelo pode ser mais eficiente.

Se usar a execução paralela, considere a controlar o número máximo de threads de trabalho. Um número mais pequeno pode resultar em menos contenção e um menor tempo de execução. Além disso, considere a carga adicional que isso coloca sobre a base de dados de destino no ligações e transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados

Documentação de orientação típica no desempenho da base de dados também afeta a criação de batches. Por exemplo, inserir desempenho é reduzido para as tabelas que têm uma chave primária grandes ou muitos índices não em cluster.

Se os parâmetros de valor de tabela usa um procedimento armazenado, pode usar o comando **SET NOCOUNT ON** no início do procedimento. Esta declaração suprime o retorno de contagem de linhas afetadas no procedimento. No entanto, no nossos testes, a utilização de **SET NOCOUNT ON** tinha sem qualquer efeito ou diminui o desempenho. O procedimento armazenado de teste foi simples com um único **inserir** comando do parâmetro de valor de tabela. É possível que procedimentos armazenados mais complexos beneficiariam com a presente declaração. Mas não pense que adicionar **SET NOCOUNT ON** para seu procedimento armazenado automaticamente melhora o desempenho. Para compreender o efeito, testar seu procedimento armazenado com e sem o **SET NOCOUNT ON** instrução.

## <a name="batching-scenarios"></a>Cenários de criação de batches

As secções seguintes descrevem como utilizar os parâmetros de valor de tabela em três cenários de aplicações. O primeiro cenário mostra como colocação em memória intermédia e processamento em lote podem trabalhar em conjunto. O segundo cenário melhora o desempenho ao realizar operações de mestre-detalhes numa chamada de procedimento armazenado único. O último cenário mostra como utilizar os parâmetros de valor de tabela numa operação de "UPSERT".

### <a name="buffering"></a>Colocação em memória intermédia

Embora existam alguns cenários que são candidatos óbvios para processamento em lote, significa que existem muitos cenários em que fosse possível tirar partido de processamento em lote pelo processamento atrasado. No entanto, o processamento atrasado também carrega um risco maior do que os dados são perdidos se ocorrer uma falha inesperada. É importante compreender este risco e considerar as conseqüências.

Por exemplo, considere uma aplicação web que controla o histórico de navegação de cada utilizador. Em cada solicitação de página, o aplicativo poderia fazer uma chamada para gravar a vista de página do utilizador de base de dados. Mas o mais elevado desempenho e escalabilidade podem ser alcançados ao armazenamento em buffer atividades de navegação dos usuários e, em seguida, enviar estes dados para a base de dados em lotes. Pode acionar a atualização da base de dados por tempo decorrido e/ou tamanho da memória intermédia. Por exemplo, uma regra pode especificar que o batch deve ser processado após 20 segundos ou quando a memória intermédia atinge 1000 itens.

O seguinte código de exemplo utiliza [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) para processar eventos em buffer gerados por uma classe de monitorização. Quando a memória intermédia preenche ou um tempo limite for atingido, o lote de dados do utilizador é enviado para a base de dados com um parâmetro de valor de tabela.

A seguinte classe NavHistoryData modela os detalhes de navegação do usuário. Contém informações básicas, como o identificador de utilizador, o URL acedidos e a hora de acesso.

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

A classe NavHistoryDataMonitor é responsável pela colocação em memória intermédia os dados de navegação do utilizador para a base de dados. Contém um método, RecordUserNavigationEntry, que responde ao criar uma **OnAdded** eventos. O código seguinte mostra a lógica do construtor que usa Rx para criar uma coleção observable com base no evento. Ele, em seguida, assina nessa observablecollection com o método de memória intermédia. A sobrecarga Especifica que a memória intermédia deve ser enviada a cada 20 segundos ou entradas de 1000.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

O manipulador converte todos os itens em buffer num tipo de valor de tabela e, em seguida, passa este tipo para um procedimento armazenado que processa o batch. O código a seguir mostra a definição completa para o NavHistoryDataEventArgs e as classes de NavHistoryDataMonitor.

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

O manipulador converte todos os itens em buffer num tipo de valor de tabela e, em seguida, passa este tipo para um procedimento armazenado que processa o batch. O código a seguir mostra a definição completa para o NavHistoryDataEventArgs e as classes de NavHistoryDataMonitor.

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

Para usar essa classe de armazenamento em buffer, o aplicativo cria um objeto de NavHistoryDataMonitor estático. Sempre que um usuário acessa uma página, o aplicativo chama o método NavHistoryDataMonitor.RecordUserNavigationEntry. Continua a lógica de armazenamento em buffer para cuidar de enviar estas entradas para a base de dados em lotes.

### <a name="master-detail"></a>Mestra de detalhes

Parâmetros de valor de tabela são úteis para cenários de inserção simples. No entanto, pode ser mais complicadas de inserções de batch que envolvem mais do que uma tabela. O cenário de "mestre/Detalhes" é um bom exemplo. A tabela mestra identifica a entidade principal. Uma ou mais tabelas de detalhe armazenam mais dados sobre a entidade. Neste cenário, as relações de chaves externas impõem a relação de detalhes para uma entidade principal exclusiva. Considere uma versão simplificada de uma tabela de PurchaseOrder e sua tabela OrderDetail associada. O Transact-SQL seguinte cria a tabela de PurchaseOrder com quatro colunas: OrderID, OrderDate, CustomerID e o estado.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Cada pedido contém um ou mais compras de produto. Estas informações são capturadas na tabela PurchaseOrderDetail. O Transact-SQL seguinte cria a tabela de PurchaseOrderDetail com cinco colunas: OrderID, OrderDetailID, ProductID, UnitPrice e OrderQty.

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

A coluna de OrderID na tabela PurchaseOrderDetail tem de referenciar uma ordem da tabela PurchaseOrder. A seguinte definição de uma chave estrangeira impõe essa restrição.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Para poder utilizar os parâmetros de valor de tabela, tem de ter um tipo de tabela definido pelo utilizador para cada tabela de destino.

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

Em seguida, defina um procedimento armazenado que aceita tabelas de um desses tipos. Este procedimento permite que um aplicativo para o lote localmente um conjunto de pedidos e detalhes do pedido numa única chamada. O Transact-SQL seguinte fornece a declaração de procedimento armazenado concluído para este exemplo de ordem de compra.

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

Neste exemplo, definida localmente @IdentityLink tabela armazena os valores de OrderID reais de linhas recentemente inseridas. Estes identificadores de ordem são diferentes entre os valores de OrderID temporários no @orders e @details parâmetros de valor de tabela. Por esse motivo, o @IdentityLink tabela liga-se, em seguida, os valores de OrderID do @orders parâmetro para os valores de OrderID real para as novas linhas na tabela PurchaseOrder. Após este passo, o @IdentityLink tabela pode facilitar a inserir os detalhes da encomenda com o OrderID real que satisfaz a restrição de chave estrangeira.

Este procedimento armazenado pode ser utilizado a partir do código ou a partir de outras chamadas de Transact-SQL. Consulte a secção de parâmetros de valor de tabela deste documento para obter um exemplo de código. O Transact-SQL seguinte mostra como chamar o sp_InsertOrdersBatch.

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

Esta solução permite que cada batch utilizar um conjunto de valores de OrderID, que começam em 1. Estes valores de OrderID temporários descrevem as relações no lote, mas os valores de OrderID reais são determinados no momento da operação de inserção. Pode executar repetidamente as mesmas instruções no exemplo anterior e gerar ordens exclusivos na base de dados. Por esse motivo, considere adicionar mais lógica de código ou a base de dados que o impede de pedidos duplicados ao utilizar esta técnica de criação de batches.

Este exemplo demonstra que possam ser loteadas operações de banco de dados ainda mais complexas, como o mestre-detalhes de operações, utilizando parâmetros de valor de tabela.

### <a name="upsert"></a>UPSERT

Outro cenário de lotes envolve simultaneamente atualizar linhas existentes e a inserção de novas linhas. Esta operação é por vezes referida como uma operação de "UPSERT" (atualização + insert). Em vez de fazer chamadas separadas para inserir e ATUALIZAR, a instrução MERGE é mais adequada para esta tarefa. A instrução MERGE pode executar ambos os insert e operações numa única chamada de atualização.

Parâmetros de valor de tabela podem ser utilizados com a instrução de intercalação para executar atualizações e inserções. Por exemplo, considere uma tabela de funcionários simplificada, que contém as seguintes colunas: EmployeeID, FirstName, LastName, Númerodoinps:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

Neste exemplo, pode usar o fato de que o Númerodoinps são exclusivo para executar uma intercalação das vários funcionários. Primeiro, crie o tipo de tabela definido pelo utilizador:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Em seguida, crie um procedimento armazenado ou escrever um código que usa a instrução MERGE para efetuar a atualização e inserir. O exemplo seguinte utiliza a instrução MERGE num parâmetro de valor de tabela, @employees, do tipo EmployeeTableType. O conteúdo do @employees tabela não são mostrados aqui.

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

Para obter mais informações, consulte a documentação e exemplos para a instrução MERGE. Embora o mesmo trabalho pode ser realizado de uma várias etapas armazenadas chamada de procedimento com separar INSERT e operações de ATUALIZAÇÃO, a instrução MERGE é mais eficiente. Código de base de dados também pode construir as chamadas de Transact-SQL que utilizam a instrução MERGE diretamente sem a necessidade de duas chamadas de base de dados para o INSERT e UPDATE.

## <a name="recommendation-summary"></a>Resumida de recomendação

A lista seguinte fornece um resumo das recomendações de criação de batches discutidos neste artigo:

* Utilize o armazenamento em buffer e criação de batches para aumentar o desempenho e escalabilidade das aplicações de base de dados SQL.
* Entenda as compensações entre a criação de batches/colocação em memória intermédia e resiliência. Durante uma falha de função, o risco de perda de um lote não processado de dados críticos da empresa poderá compensar o benefício de desempenho de processamento em lote.
* Tentativa de manter todas as chamadas para a base de dados num único datacenter para reduzir a latência.
* Se optar por uma única técnica de criação de batches, parâmetros de valor de tabela oferecem o melhor desempenho e flexibilidade.
* Para obter o desempenho de inserção mais rápido, siga estas Diretrizes gerais, mas seu cenário de teste:
  * < 100 linhas, utilize um único comando INSERT parametrizado.
  * < A 1000 linhas, use parâmetros de valor de tabela.
  * Para > = 1000 linhas, utilize SqlBulkCopy.
* Para atualizar e eliminar operações, usar parâmetros de valor de tabela com a lógica do procedimento armazenado que determina a operação correta em cada linha no parâmetro de tabela.
* Diretrizes de tamanho de lote:
  * Utilize o tamanho de lote maior que façam sentido para a sua aplicação e os requisitos comerciais.
  * Equilibrar o ganho de desempenho de lotes grandes com os riscos de falhas temporários ou catastróficos. Qual é a conseqüência de repetições ou perda de dados no lote? 
  * Teste o maior tamanho de lote para verificar que o banco de dados SQL não rejeitá-la.
  * Crie definições de configuração desse controle processamento em lote, como o tamanho do lote ou a janela de tempo de buffer. Estas definições fornecem flexibilidade. Pode alterar o comportamento de criação de batches na produção sem implementar novamente o serviço em nuvem.
* Evite a execução paralela de lotes que operam numa única tabela numa base de dados. Se optar por dividir um único lote em vários threads de trabalho, execute testes para determinar o número ideal de threads. Depois de um limiar não especificado, mais threads irão diminuir o desempenho em vez de aumentá-la.
* Considere a colocação em memória intermédia no tamanho e o tempo como uma forma de implementar a criação de batches de mais cenários.

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se sobre como as técnicas relacionadas a criação de batches de codificação e de design de banco de dados podem melhorar o desempenho da aplicação e a escalabilidade. Mas isso é apenas um dos fatores na sua estratégia geral. Para obter mais formas de melhorar o desempenho e escalabilidade, consulte [orientações de desempenho de base de dados do Azure SQL para bases de dados individuais](sql-database-performance-guidance.md) e [considerações sobre preço e desempenho de um conjunto elástico](sql-database-elastic-pool-guidance.md).

