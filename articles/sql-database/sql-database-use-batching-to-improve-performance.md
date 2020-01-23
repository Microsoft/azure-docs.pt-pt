---
title: Como usar o envio em lote para melhorar o desempenho do aplicativo
description: O tópico fornece evidências de que o envio em lote de operações de banco de dados melhora muito a velocidade e a escalabilidade dos aplicativos do banco de dados SQL do Azure. Embora essas técnicas de envio em lote funcionem para qualquer SQL Server banco de dados, o foco do artigo está no Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: cacc01151edaf31db938cf8abf3d46e75397758f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545029"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Como usar o envio em lote para melhorar o desempenho do aplicativo do banco de dados SQL

As operações de envio em lote para o banco de dados SQL do Azure aprimoram significativamente o desempenho e a escalabilidade de seus aplicativos. Para entender os benefícios, a primeira parte deste artigo aborda alguns exemplos de resultados de teste que comparam solicitações sequenciais e em lote para um banco de dados SQL. O restante do artigo mostra as técnicas, os cenários e as considerações para ajudá-lo a usar o envio em lote com êxito em seus aplicativos do Azure.

## <a name="why-is-batching-important-for-sql-database"></a>Por que o envio em lote é importante para o banco de dados SQL

O envio em lote de chamadas para um serviço remoto é uma estratégia bem conhecida para aumentar o desempenho e a escalabilidade. Há custos de processamento fixos para quaisquer interações com um serviço remoto, como serialização, transferência de rede e desserialização. O empacotamento de várias transações separadas em um único lote minimiza esses custos.

Neste documento, queremos examinar várias estratégias e cenários de envio em lote do banco de dados SQL. Embora essas estratégias também sejam importantes para aplicativos locais que usam SQL Server, há várias razões para realçar o uso de envio em lote para o banco de dados SQL:

* Há potencialmente maior latência de rede no acesso ao banco de dados SQL, especialmente se você estiver acessando o banco de dados SQL de fora do mesmo datacenter Microsoft Azure.
* As características de multilocatários do banco de dados SQL significam que a eficiência da camada de acesso ao dado se correlaciona com a escalabilidade geral do banco. O banco de dados SQL deve impedir que um único locatário/usuário monopolizasse recursos de banco de dados para o prejudicial de outros locatários. Em resposta ao uso em excesso de cotas predefinidas, o banco de dados SQL pode reduzir a taxa de transferência ou responder com exceções de limitação. As eficiências, como o envio em lote, permitem que você faça mais trabalho no banco de dados SQL antes de atingir esses limites. 
* O envio em lote também é eficaz para arquiteturas que usam vários bancos de dados (fragmentação). A eficiência de sua interação com cada unidade de banco de dados ainda é um fator importante em sua escalabilidade geral. 

Um dos benefícios de usar o banco de dados SQL é que você não precisa gerenciar os servidores que hospedam o banco de dados. No entanto, essa infraestrutura gerenciada também significa que você precisa pensar de maneira diferente sobre otimizações de banco de dados. Você não pode mais procurar melhorar o hardware de banco de dados ou a infraestrutura de rede. Microsoft Azure controla esses ambientes. A área principal que você pode controlar é como seu aplicativo interage com o banco de dados SQL. O envio em lote é uma dessas otimizações. 

A primeira parte do documento examina várias técnicas de envio em lote para aplicativos .NET que usam o banco de dados SQL. As duas últimas seções abordam as diretrizes e os cenários de envio em lote.

## <a name="batching-strategies"></a>Estratégias de envio em lote

### <a name="note-about-timing-results-in-this-article"></a>Observação sobre os resultados de tempo neste artigo

> [!NOTE]
> Os resultados não são parâmetros de comparação, mas servem para mostrar o **desempenho relativo**. Os intervalos são baseados em uma média de pelo menos 10 execuções de teste. As operações são inseridas em uma tabela vazia. Esses testes foram medidos previamente com V12 e não correspondem necessariamente à taxa de transferência que você pode ter em um banco de dados V12 usando as novas [camadas de serviço de DTU](sql-database-service-tiers-dtu.md) ou as camadas de [serviço vCore](sql-database-service-tiers-vcore.md). O benefício relativo da técnica de envio em lote deve ser semelhante.

### <a name="transactions"></a>Transações

Parece estranho iniciar uma revisão do envio em lote discutindo transações. Mas o uso de transações do lado do cliente tem um efeito sutil no envio em lote do lado do servidor que melhora o desempenho. E as transações podem ser adicionadas com apenas algumas linhas de código, portanto, elas fornecem uma maneira rápida de melhorar o desempenho de operações sequenciais.

Considere o código C# a seguir que contém uma sequência de operações INSERT e Update em uma tabela simples.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
O código ADO.NET a seguir executa sequencialmente essas operações.

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

A melhor maneira de otimizar esse código é implementar alguma forma de envio em lote do lado do cliente dessas chamadas. Mas há uma maneira simples de aumentar o desempenho desse código simplesmente encapsulando a sequência de chamadas em uma transação. Este é o mesmo código que usa uma transação.

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

As transações estão realmente sendo usadas em ambos os exemplos. No primeiro exemplo, cada chamada individual é uma transação implícita. No segundo exemplo, uma transação explícita encapsula todas as chamadas. De acordo com a documentação do [log de transações write-ahead](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL), os registros de log são liberados para o disco quando a transação é confirmada. Portanto, ao incluir mais chamadas em uma transação, a gravação no log de transações pode atrasar até que a transação seja confirmada. Na verdade, você está habilitando o envio em lote para as gravações no log de transações do servidor.

A tabela a seguir mostra alguns resultados de testes ad hoc. Os testes executaram as mesmas inserções sequenciais com e sem transações. Para obter mais perspectiva, o primeiro conjunto de testes foi executado remotamente de um laptop para o banco de dados no Microsoft Azure. O segundo conjunto de testes foi executado de um serviço de nuvem e de um banco de dados que residem dentro do mesmo Microsoft Azure datacenter (oeste dos EUA). A tabela a seguir mostra a duração em milissegundos de inserções sequenciais com e sem transações.

**Local para o Azure**:

| Operations | Nenhuma transação (MS) | Transação (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure para Azure (mesmo datacenter)** :

| Operations | Nenhuma transação (MS) | Transação (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).

Com base nos resultados do teste anterior, o encapsulamento de uma única operação em uma transação realmente diminui o desempenho. Mas à medida que você aumenta o número de operações em uma única transação, a melhoria de desempenho se torna mais marcada. A diferença de desempenho também é mais perceptível quando todas as operações ocorrem dentro do datacenter Microsoft Azure. A maior latência de usar o banco de dados SQL de fora do Microsoft Azure datacenter excede o ganho de desempenho do uso de transações.

Embora o uso de transações possa aumentar o desempenho, continue a [observar as práticas recomendadas para transações e conexões](https://msdn.microsoft.com/library/ms187484.aspx). Mantenha a transação o mais curta possível e feche a conexão de banco de dados após a conclusão do trabalho. A instrução using no exemplo anterior garante que a conexão seja fechada quando o bloco de código subsequente for concluído.

O exemplo anterior demonstra que você pode adicionar uma transação local a qualquer código ADO.NET com duas linhas. As transações oferecem uma maneira rápida de melhorar o desempenho do código que faz operações de inserção sequencial, atualização e exclusão. No entanto, para o desempenho mais rápido, considere alterar o código ainda mais para tirar proveito do envio em lote do lado do cliente, como parâmetros com valor de tabela.

Para obter mais informações sobre transações em ADO.NET, consulte [transações locais em ADO.net](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>Parâmetros com valor de tabela

Os parâmetros com valor de tabela dão suporte a tipos de tabela definidos pelo usuário como parâmetros em instruções Transact-SQL, procedimentos armazenados e funções. Essa técnica de envio em lote do lado do cliente permite que você envie várias linhas de dados dentro do parâmetro com valor de tabela. Para usar parâmetros com valor de tabela, primeiro defina um tipo de tabela. A instrução Transact-SQL a seguir cria um tipo de tabela chamado **mytablename**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

No código, você cria uma **DataTable** com exatamente os mesmos nomes e tipos do tipo de tabela. Passe essa **DataTable** em um parâmetro em uma consulta de texto ou chamada de procedimento armazenado. O exemplo a seguir mostra essa técnica:

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

No exemplo anterior, o objeto **SqlCommand** insere linhas de um parâmetro com valor de tabela, **\@TestTvp**. O objeto **DataTable** criado anteriormente é atribuído a esse parâmetro com o método **SqlCommand. Parameters. Add** . O envio em lote das inserções em uma chamada aumenta significativamente o desempenho em relação às inserções sequenciais.

Para melhorar ainda mais o exemplo anterior, use um procedimento armazenado em vez de um comando baseado em texto. O comando Transact-SQL a seguir cria um procedimento armazenado que usa o parâmetro com valor de tabela **SimpleTestTableType** .

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

Na maioria dos casos, os parâmetros com valor de tabela têm um desempenho equivalente ou melhor do que outras técnicas de envio em lote. Os parâmetros com valor de tabela geralmente são preferíveis, pois são mais flexíveis do que outras opções. Por exemplo, outras técnicas, como cópia em massa do SQL, só permitem a inserção de novas linhas. Mas com parâmetros com valor de tabela, você pode usar a lógica no procedimento armazenado para determinar quais linhas são atualizações e quais são inserções. O tipo de tabela também pode ser modificado para conter uma coluna de "operação" que indica se a linha especificada deve ser inserida, atualizada ou excluída.

A tabela a seguir mostra os resultados de teste ad hoc para o uso de parâmetros com valor de tabela em milissegundos.

| Operations | Local para o Azure (MS) | Mesmo datacenter do Azure (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).
> 
> 

O lucro de desempenho do envio em lote é imediatamente aparente. No teste sequencial anterior, 1000 operações levaram 129 segundos fora do datacenter e 21 segundos de dentro do datacenter. Mas com parâmetros com valor de tabela, 1000 operações levam apenas 2,6 segundos fora do datacenter e 0,4 segundos dentro do datacenter.

Para obter mais informações sobre parâmetros com valor de tabela, consulte [parâmetros com valor de tabela](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Cópia em massa do SQL

A cópia em massa do SQL é outra maneira de inserir grandes quantidades de dados em um banco de dado de destino. Os aplicativos .NET podem usar a classe **SqlBulkCopy** para executar operações de inserção em massa. **SqlBulkCopy** é semelhante em função à ferramenta de linha de comando, **bcp. exe**ou à instrução Transact-SQL, **BULK INSERT**. O exemplo de código a seguir mostra como copiar em massa as linhas na **DataTable**de origem, tabela, para a tabela de destino em SQL Server, MyTable.

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

Há alguns casos em que a cópia em massa é preferida em relação a parâmetros com valor de tabela. Consulte a tabela de comparação de parâmetros com valor de tabela versus operações de BULK INSERT no artigo [parâmetros com valor de tabela](https://msdn.microsoft.com/library/bb510489.aspx).

Os seguintes resultados de teste ad hoc mostram o desempenho do envio em lote com **SqlBulkCopy** em milissegundos.

| Operations | Local para o Azure (MS) | Mesmo datacenter do Azure (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Em tamanhos de lote menores, o uso de parâmetros com valor de tabela desempenhou a classe **SqlBulkCopy** . No entanto, o **SqlBulkCopy** realizou 12-31% mais rápido do que os parâmetros com valor de tabela para os testes de 1.000 e 10.000 linhas. Assim como os parâmetros com valor de tabela, **SqlBulkCopy** é uma boa opção para inserções em lote, especialmente quando comparado ao desempenho de operações não em lotes.

Para obter mais informações sobre cópia em massa no ADO.NET, consulte [operações de cópia em massa no SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instruções INSERT com parâmetros de várias linhas

Uma alternativa para pequenos lotes é construir uma grande instrução INSERT parametrizada que insere várias linhas. O exemplo de código a seguir demonstra essa técnica.

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

Este exemplo destina-se a mostrar o conceito básico. Um cenário mais realista percorreria as entidades necessárias para construir a cadeia de caracteres de consulta e os parâmetros de comando simultaneamente. Você está limitado a um total de 2100 parâmetros de consulta, portanto, isso limita o número total de linhas que podem ser processadas dessa maneira.

Os seguintes resultados de teste ad hoc mostram o desempenho desse tipo de instrução INSERT em milissegundos.

| Operations | Parâmetros com valor de tabela (MS) | INSERÇÃO de instrução única (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Essa abordagem pode ser um pouco mais rápida para lotes com menos de 100 linhas. Embora a melhoria seja pequena, essa técnica é outra opção que pode funcionar bem em seu cenário de aplicativo específico.

### <a name="dataadapter"></a>DataAdapter

A classe **DataAdapter** permite que você modifique um objeto **DataSet** e, em seguida, envie as alterações como operações de inserção, atualização e exclusão. Se você estiver usando o **DataAdapter** dessa maneira, é importante observar que chamadas separadas são feitas para cada operação distinta. Para melhorar o desempenho, use a propriedade **UpdateBatchSize** para o número de operações que devem ser agrupadas em lote por vez. Para obter mais informações, consulte [realizando operações em lote usando adaptadores](https://msdn.microsoft.com/library/aadf8fk2.aspx)de dados.

### <a name="entity-framework"></a>Entity Framework

No momento, o Entity Framework não dá suporte ao envio em lote. Desenvolvedores diferentes na Comunidade tentaram demonstrar soluções alternativas, como substituir o método **SaveChanges** . Mas as soluções normalmente são complexas e personalizadas para o aplicativo e o modelo de dados. O Entity Framework Projeto CodePlex atualmente tem uma página de discussão sobre essa solicitação de recurso. Para exibir essa discussão, consulte [notas de reunião de design – 2 de agosto de 2012](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

Para fins de integridade, sentimos que é importante falar sobre XML como uma estratégia de envio em lote. No entanto, o uso de XML não tem vantagens sobre outros métodos e várias desvantagens. A abordagem é semelhante aos parâmetros com valor de tabela, mas um arquivo XML ou uma cadeia de caracteres é passada para um procedimento armazenado em vez de uma tabela definida pelo usuário. O procedimento armazenado analisa os comandos no procedimento armazenado.

Há várias desvantagens nessa abordagem:

* Trabalhar com XML pode ser trabalhoso e propenso a erros.
* A análise do XML no banco de dados pode consumir muitos recursos de CPU.
* Na maioria dos casos, esse método é mais lento do que os parâmetros com valor de tabela.

Por esses motivos, o uso de XML para consultas em lote não é recomendado.

## <a name="batching-considerations"></a>Considerações sobre o envio em lote

As seções a seguir fornecem mais diretrizes para o uso de envio em lote em aplicativos de banco de dados SQL.

### <a name="tradeoffs"></a>Compensações

Dependendo da sua arquitetura, o envio em lote pode envolver uma compensação entre o desempenho e a resiliência. Por exemplo, considere o cenário em que a função fica inesperadamente inativa. Se você perder uma linha de dados, o impacto será menor do que o impacto da perda de um grande lote de linhas não enviadas. Há um risco maior quando você armazena em buffer as linhas antes de enviá-las ao banco de dados em uma janela de tempo especificada.

Devido a essa compensação, avalie o tipo de operações em lote. Lote mais agressivamente (lotes maiores e janelas de tempo maior) com dados menos críticos.

### <a name="batch-size"></a>Tamanho do batch

Em nossos testes, normalmente não havia vantagem em dividir grandes lotes em partes menores. Na verdade, essa subdivisão geralmente resultou em um desempenho mais lento do que o envio de um único lote grande. Por exemplo, considere um cenário em que você deseja inserir 1000 linhas. A tabela a seguir mostra quanto tempo leva para usar parâmetros com valor de tabela para inserir 1000 linhas quando divididas em lotes menores.

| Tamanho do batch | Iterations | Parâmetros com valor de tabela (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Você pode ver que o melhor desempenho para 1000 linhas é enviar todas elas de uma só vez. Em outros testes (não mostrados aqui), houve um pequeno lucro de desempenho para dividir um lote de 10000 linhas em dois lotes de 5000. Mas o esquema de tabela para esses testes é relativamente simples, portanto, você deve executar testes em seus dados específicos e tamanhos de lote para verificar essas descobertas.

Outro fator a ser considerado é que, se o lote total se tornar muito grande, o banco de dados SQL poderá limitar e recusar a confirmação do lote. Para obter os melhores resultados, teste seu cenário específico para determinar se há um tamanho de lote ideal. Torne o tamanho do lote configurável no tempo de execução para habilitar os ajustes rápidos com base no desempenho ou nos erros.

Por fim, equilibre o tamanho do lote com os riscos associados ao envio em lote. Se houver erros transitórios ou a função falhar, considere as consequências de repetir a operação ou de perder os dados no lote.

### <a name="parallel-processing"></a>Processamento paralelo

E se você tomou a abordagem de reduzir o tamanho do lote, mas usou vários threads para executar o trabalho? Novamente, nossos testes mostraram que vários lotes menores de multithreads costumavam ser piores do que um único lote maior. O teste a seguir tenta inserir 1000 linhas em um ou mais lotes paralelos. Esse teste mostra como mais lotes simultâneos realmente diminuíram o desempenho.

| Tamanho do lote [iterações] | Dois threads (MS) | Quatro threads (MS) | Seis threads (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Os resultados não são parâmetros de comparação. Consulte a [Observação sobre os resultados de tempo neste artigo](#note-about-timing-results-in-this-article).
> 
> 

Há vários motivos possíveis para a degradação do desempenho devido ao paralelismo:

* Há várias chamadas de rede simultâneas em vez de uma.
* Várias operações em uma única tabela podem resultar em contenção e bloqueio.
* Há sobrecargas associadas ao multithreading.
* A despesa de abrir várias conexões supera o benefício do processamento paralelo.

Se você direcionar tabelas ou bancos de dados diferentes, é possível ver algum lucro de desempenho com essa estratégia. A fragmentação ou as federações de banco de dados seriam um cenário para essa abordagem. A fragmentação usa vários bancos de dados e roteia diferentes data para cada um deles. Se cada lote pequeno estiver indo para um banco de dados diferente, executar as operações em paralelo pode ser mais eficiente. No entanto, o lucro de desempenho não é significativo o suficiente para usar como base para uma decisão de usar a fragmentação de banco de dados em sua solução.

Em alguns designs, a execução paralela de lotes menores pode resultar em uma melhor taxa de transferência de solicitações em um sistema sob carga. Nesse caso, embora seja mais rápido processar um único lote maior, o processamento de vários lotes em paralelo pode ser mais eficiente.

Se você usar a execução paralela, considere controlar o número máximo de threads de trabalho. Um número menor pode resultar em menos contenção e em um tempo de execução mais rápido. Além disso, considere a carga adicional que isso coloca no banco de dados de destino tanto em conexões quanto em transações.

### <a name="related-performance-factors"></a>Fatores de desempenho relacionados

Diretrizes típicas sobre o desempenho do banco de dados também afetam o envio em lote. Por exemplo, o desempenho de inserção é reduzido para tabelas que têm uma chave primária grande ou muitos índices não clusterizados.

Se os parâmetros com valor de tabela usarem um procedimento armazenado, você poderá usar o comando **SET NOCOUNT ON** no início do procedimento. Essa instrução suprime o retorno da contagem das linhas afetadas no procedimento. No entanto, em nossos testes, o uso de **SET NOCOUNT ON** não tinha nenhum efeito ou diminuiu o desempenho. O procedimento armazenado de teste era simples com um único comando **Insert** do parâmetro com valor de tabela. É possível que procedimentos armazenados mais complexos se beneficiem dessa declaração. Mas não presuma que a adição de **SET NOCOUNT ON** ao seu procedimento armazenado melhora o desempenho automaticamente. Para entender o efeito, teste seu procedimento armazenado com e sem a instrução **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Cenários de envio em lote

As seções a seguir descrevem como usar parâmetros com valor de tabela em três cenários de aplicativo. O primeiro cenário mostra como o armazenamento em buffer e o envio em lote podem trabalhar juntos. O segundo cenário melhora o desempenho executando operações de detalhes mestre em uma única chamada de procedimento armazenado. O cenário final mostra como usar parâmetros com valor de tabela em uma operação "UPSERT".

### <a name="buffering"></a>Buffer

Embora existam alguns cenários que são candidatos óbvios para envio em lote, há muitos cenários que podem aproveitar o envio em lote por meio de processamento atrasado. No entanto, o processamento atrasado também tem um risco maior de que os dados sejam perdidos no caso de uma falha inesperada. É importante entender esse risco e considerar as consequências.

Por exemplo, considere um aplicativo Web que controla o histórico de navegação de cada usuário. Em cada solicitação de página, o aplicativo pode fazer uma chamada de banco de dados para registrar a exibição de página do usuário. Mas o desempenho e a escalabilidade mais altos podem ser obtidos armazenando em buffer as atividades de navegação dos usuários e enviando esses dados para o banco de dado em lotes. Você pode disparar a atualização do banco de dados por tempo decorrido e/ou tamanho do buffer. Por exemplo, uma regra pode especificar que o lote deve ser processado após 20 segundos ou quando o buffer atinge 1000 itens.

O exemplo de código a seguir usa [extensões reativas-RX](https://msdn.microsoft.com/data/gg577609) para processar eventos em buffer gerados por uma classe de monitoramento. Quando o buffer é preenchido ou um tempo limite é atingido, o lote de dados do usuário é enviado ao banco de dado com um parâmetro com valor de tabela.

A classe NavHistoryData a seguir modela os detalhes de navegação do usuário. Ele contém informações básicas, como o identificador de usuário, a URL acessada e o tempo de acesso.

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

A classe NavHistoryDataMonitor é responsável por armazenar em buffer os dados de navegação do usuário para o banco de dado. Ele contém um método, RecordUserNavigationEntry, que responde gerando um evento **OnAdded** . O código a seguir mostra a lógica do construtor que usa RX para criar uma coleção observável com base no evento. Em seguida, ele assina essa coleção observável com o método de buffer. A sobrecarga Especifica que o buffer deve ser enviado a cada 20 segundos ou 1000 entradas.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

O manipulador converte todos os itens em buffer em um tipo com valor de tabela e, em seguida, passa esse tipo para um procedimento armazenado que processa o lote. O código a seguir mostra a definição completa para as classes NavHistoryDataEventArgs e NavHistoryDataMonitor.

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

O manipulador converte todos os itens em buffer em um tipo com valor de tabela e, em seguida, passa esse tipo para um procedimento armazenado que processa o lote. O código a seguir mostra a definição completa para as classes NavHistoryDataEventArgs e NavHistoryDataMonitor.

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

Para usar essa classe de buffer, o aplicativo cria um objeto NavHistoryDataMonitor estático. Cada vez que um usuário acessa uma página, o aplicativo chama o método NavHistoryDataMonitor. RecordUserNavigationEntry. A lógica de buffer continua a cuidar do envio dessas entradas para o banco de dados em lotes.

### <a name="master-detail"></a>Detalhes do mestre

Os parâmetros com valor de tabela são úteis para cenários de inserção simples. No entanto, pode ser mais desafiador para inserções em lote que envolvem mais de uma tabela. O cenário de "mestre/detalhes" é um bom exemplo. A tabela mestra identifica a entidade primária. Uma ou mais tabelas de detalhes armazenam mais dados sobre a entidade. Nesse cenário, as relações de chave estrangeira impõem a relação de detalhes a uma entidade mestra exclusiva. Considere uma versão simplificada de uma tabela PurchaseOrder e sua tabela OrderDetail associada. O Transact-SQL a seguir cria a tabela PurchaseOrder com quatro colunas: OrderID, OrderDate, CustomerID e status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Cada pedido contém uma ou mais compras de produtos. Essas informações são capturadas na tabela PurchaseOrderDetail. O Transact-SQL a seguir cria a tabela PurchaseOrderDetail com cinco colunas: OrderID, OrderDetailid, ProductID, UnitPrice e OrderQty.

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

A coluna OrderID na tabela PurchaseOrderDetail deve fazer referência a uma ordem da tabela PurchaseOrder. A definição a seguir de uma chave estrangeira impõe essa restrição.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Para usar parâmetros com valor de tabela, você deve ter um tipo de tabela definido pelo usuário para cada tabela de destino.

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

Em seguida, defina um procedimento armazenado que aceite tabelas desses tipos. Esse procedimento permite que um aplicativo batche localmente um conjunto de pedidos e detalhes do pedido em uma única chamada. O Transact-SQL a seguir fornece a declaração de procedimento armazenado completa para este exemplo de ordem de compra.

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

Neste exemplo, a tabela @IdentityLink definida localmente armazena os valores reais de OrderID das linhas recentemente inseridas. Esses identificadores de ordem são diferentes dos valores temporários de OrderID no @orders e @details parâmetros com valor de tabela. Por esse motivo, a tabela @IdentityLink conecta os valores de OrderID do parâmetro @orders aos valores reais de OrderID para as novas linhas na tabela PurchaseOrder. Após essa etapa, a tabela @IdentityLink pode facilitar a inserção dos detalhes do pedido com o OrderID real que satisfaz a restrição FOREIGN KEY.

Esse procedimento armazenado pode ser usado do código ou de outras chamadas Transact-SQL. Consulte a seção parâmetros com valor de tabela deste documento para obter um exemplo de código. O Transact-SQL a seguir mostra como chamar o sp_InsertOrdersBatch.

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

Essa solução permite que cada lote use um conjunto de valores NúmeroDoPedido que começam em 1. Esses valores temporários de OrderID descrevem as relações no lote, mas os valores reais de OrderID são determinados no momento da operação de inserção. Você pode executar as mesmas instruções no exemplo anterior repetidamente e gerar ordens exclusivas no banco de dados. Por esse motivo, considere adicionar mais código ou lógica de banco de dados que impeça pedidos duplicados ao usar essa técnica de envio em lote.

Este exemplo demonstra que até mesmo operações de banco de dados mais complexas, como operações de detalhes mestre, podem ser processadas em lote usando parâmetros com valor de tabela.

### <a name="upsert"></a>UPSERT

Outro cenário de envio em lote envolve a atualização simultânea de linhas existentes e a inserção de novas linhas. Essa operação às vezes é chamada de operação "UPSERT" (atualização + inserção). Em vez de fazer chamadas separadas para INSERT e UPDATE, a instrução MERGE é mais adequada para essa tarefa. A instrução MERGE pode executar operações de inserção e atualização em uma única chamada.

Os parâmetros com valor de tabela podem ser usados com a instrução MERGE para executar atualizações e inserções. Por exemplo, considere uma tabela de funcionários simplificada que contém as seguintes colunas: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

Neste exemplo, você pode usar o fato de que o SocialSecurityNumber é exclusivo para executar uma MESCLAgem de vários funcionários. Primeiro, crie o tipo de tabela definido pelo usuário:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Em seguida, crie um procedimento armazenado ou escreva um código que use a instrução MERGE para executar a atualização e a inserção. O exemplo a seguir usa a instrução MERGE em um parâmetro com valor de tabela, @employees, do tipo EmployeeTableType. O conteúdo da tabela @employees não é mostrado aqui.

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

Para obter mais informações, consulte a documentação e exemplos para a instrução MERGE. Embora o mesmo trabalho possa ser executado em uma chamada de procedimento armazenado de várias etapas com operações de inserção e atualização separadas, a instrução MERGE é mais eficiente. O código do banco de dados também pode construir chamadas Transact-SQL que usam a instrução MERGE diretamente sem exigir duas chamadas de banco de dados para INSERT e UPDATE.

## <a name="recommendation-summary"></a>Resumo de recomendação

A lista a seguir fornece um resumo das recomendações de envio em lote discutidas neste artigo:

* Use o armazenamento em buffer e o envio em lote para aumentar o desempenho e a escalabilidade de aplicativos de banco de dados SQL.
* Entenda as compensações entre o envio em lote/buffer e a resiliência. Durante uma falha de função, o risco de perder um lote não processado de dados críticos para os negócios pode superar o benefício de desempenho do envio em lote.
* Tente manter todas as chamadas para o banco de dados em um único datacenter para reduzir a latência.
* Se você escolher uma única técnica de envio em lote, os parâmetros com valor de tabela oferecem o melhor desempenho e flexibilidade.
* Para obter o desempenho de inserção mais rápido, siga estas diretrizes gerais, mas teste seu cenário:
  * Para < linhas 100, use um único comando INSERT com parâmetros.
  * Para < linhas 1000, use parâmetros com valor de tabela.
  * Para > = 1000 linhas, use SqlBulkCopy.
* Para operações de atualização e exclusão, use parâmetros com valor de tabela com lógica de procedimento armazenado que determina a operação correta em cada linha no parâmetro de tabela.
* Diretrizes de tamanho do lote:
  * Use os maiores tamanhos de lote que fazem sentido para seus requisitos de aplicativo e de negócios.
  * Equilibre o lucro de desempenho de grandes lotes com os riscos de falhas temporárias ou catastróficas. Qual é a consequência de novas tentativas ou perda dos dados no lote? 
  * Teste o maior tamanho de lote para verificar se o banco de dados SQL não o rejeita.
  * Crie definições de configuração que controlam o envio em lote, como o tamanho do lote ou a janela de tempo de buffer. Essas configurações fornecem flexibilidade. Você pode alterar o comportamento de envio em lote na produção sem reimplantar o serviço de nuvem.
* Evite a execução paralela de lotes que operam em uma única tabela em um banco de dados. Se você optar por dividir um único lote entre vários threads de trabalho, execute testes para determinar o número ideal de threads. Após um limite não especificado, mais threads diminuirão o desempenho em vez de aumentá-lo.
* Considere o armazenamento em buffer no tamanho e no tempo como uma maneira de implementar o envio em lote para mais cenários.

## <a name="next-steps"></a>Passos seguintes

Este artigo se concentrou em como as técnicas de design e codificação de banco de dados relacionadas ao envio em lote podem melhorar o desempenho e a escalabilidade do aplicativo. Mas esse é apenas um fator em sua estratégia geral. Para obter mais maneiras de melhorar o desempenho e a escalabilidade, consulte [diretrizes de desempenho do banco de dados SQL do Azure para obter bancos](sql-database-performance-guidance.md) e [considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md).

