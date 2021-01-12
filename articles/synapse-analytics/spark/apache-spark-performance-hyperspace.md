---
title: Índices de hiperespaço para Apache Spark
description: Otimização de desempenho para o Apache Spark com índices do Hyperspace
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 3aedef8452ad3e972f78958fc0765639692d76d6
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121061"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>Hiperespaço: Subsistema de indexação para Apache Spark

O Hiperspace introduz a capacidade para os utilizadores do Apache Spark criarem índices nos seus conjuntos de dados, tais como CSV, JSON e Parquet, e usá-los para a potencial consulta e aceleração da carga de trabalho.

Neste artigo, destacamos os fundamentos do Hiperespaço, enfatizamos a sua simplicidade e mostramos como pode ser usado por qualquer um.

Isenção de responsabilidade: O hiperespaço ajuda a acelerar as suas cargas de trabalho ou consultas em duas circunstâncias:

* As consultas contêm filtros em predicados com elevada seletividade. Por exemplo, é melhor selecionar 100 linhas correspondentes de um milhão de candidatos.
* As consultas contêm uma junção que requer baralhações pesadas. Por exemplo, é melhor juntar-se a um conjunto de dados de 100 GB com um conjunto de dados de 10 GB.

É possível que queira monitorizar cuidadosamente as suas cargas de trabalho e determinar se a indexação está a ajudá-lo caso a caso.

Este documento também está disponível em formato de caderno, para [Python,](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb) [C.](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)e [Scala.](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)

## <a name="setup"></a>Configuração

Para começar, inicie uma nova sessão de Faíscas. Uma vez que este documento é um tutorial apenas para ilustrar o que o Hyperspace pode oferecer, irá fazer uma alteração de configuração que nos permite destacar o que o Hyperspace está a fazer em pequenos conjuntos de dados. 

Por padrão, a Spark usa a rede de transmissão para otimizar as consultas de junção quando o tamanho dos dados para um lado da junção é pequeno (o que é o caso dos dados da amostra que usamos neste tutorial). Portanto, desativamos as transmissões para que, mais tarde, quando executarmos consultas de junção, a Spark use a sesutar de tipo-fusão. Isto é principalmente para mostrar como os índices de hiperespaço seriam usados em escala para acelerar consultas de junção.

A saída de executar a seguinte célula mostra uma referência à sessão spark criada com sucesso e imprime '-1' como o valor para o config de junção modificado, o que indica que a junção de transmissão é desativada com sucesso.

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

Resultados em:

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>Preparação de dados

Para preparar o seu ambiente, criará registos de dados de amostras e guardá-los-á como ficheiros de dados do Parquet. O parquet é usado para ilustração, mas também pode usar outros formatos, como o CSV. Nas células subsequentes, verá como pode criar vários índices de Hiperespaço neste conjunto de dados da amostra e fazer com que o Spark os utilize ao executar consultas.

Os registos de exemplo correspondem a dois conjuntos de dados: departamento e funcionário. Deve configurar os caminhos "empLocation" e "deptLocation" para que na conta de armazenamento eles apitarem para a localização desejada para guardar ficheiros de dados gerados.

A produção de execução da célula seguinte mostra o conteúdo dos nossos conjuntos de dados como listas de trigémeos seguidos de referências aos DataFrames criados para salvar o conteúdo de cada conjunto de dados na nossa localização preferida.

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

Resultados em:

```console
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  

empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

Vamos verificar o conteúdo dos ficheiros Parquet que criamos para garantir que contêm registos esperados no formato correto. Mais tarde, usaremos estes ficheiros de dados para criar índices de Hiperespaço e executar consultas de amostras.

Executar os seguintes produtos e saídas celulares que exibem as linhas nos quadros de dados de funcionários e departamentos de forma tabular. Devem existir 14 funcionários e 4 departamentos, cada um combinando com um dos trigémeos que criou na célula anterior.

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

Resultados em:

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>Índices

O hiperespaço permite criar índices em registos digitalizados a partir de ficheiros de dados persistidos. Depois de terem sido criados com sucesso, uma entrada que corresponde ao índice é adicionada aos metadados do Hiperespaço. Estes metadados são mais tarde utilizados pelo otimizador da Apache Spark (com as nossas extensões) durante o processamento de consultas para encontrar e utilizar índices adequados.

Após a criação de índices, pode executar várias ações:

* **Refresque se os dados subjacentes forem alterando.** Pode refrescar um índice existente para capturar as alterações.
* **Apague se o índice não for necessário.** Pode executar uma eliminação suave, ou seja, o índice não é fisicamente apagado, mas é marcado como "eliminado" para que não seja mais utilizado nas suas cargas de trabalho.
* **Vácuo se um índice deixar de ser necessário.** Pode aspirar um índice, que força a eliminação física dos conteúdos de índice e metadados associados completamente a partir dos metadados do Hyperspace.

Refresque se os dados subjacentes forem alterando, pode atualizar um índice existente para capturar isso.
Elimine se o índice não for necessário, pode executar um soft-delete que é, o índice não é fisicamente eliminado, mas é marcado como 'eliminado' para que não seja mais utilizado nas suas cargas de trabalho.

As seguintes secções mostram como tais operações de gestão de índices podem ser feitas no Hiperespaço.

Primeiro, é necessário importar as bibliotecas necessárias e criar um exemplo de Hiperespaço. Mais tarde, você usará este caso para invocar diferentes APIs hiperespaciais para criar índices nos seus dados de amostra e modificar esses índices.

A saída de executar a célula seguinte mostra uma referência à instância criada do Hiperespaço.

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
from hyperspace import *

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

Resultados em:

```console
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>Criar índices

Para criar um índice de Hiperespaço, é necessário fornecer duas informações:

* Um DataFrame de Faísca que faz referência aos dados a indexados.
* Um objeto de configuração de índice, IndexConfig, que especifica o nome do índice e as colunas indexadas e incluídas do índice.

Começa por criar três índices de Hiperespaço nos dados da nossa amostra: dois índices no conjunto de dados do departamento denominado "deptIndex1" e "deptIndex2" e um índice no conjunto de dados dos colaboradores denominado "empIndex". Para cada índice, é necessário um IndexConfig correspondente para capturar o nome juntamente com as listas de colunas para as colunas indexadas e incluídas. Executar a seguinte célula cria estes IndexConfigs, e a sua saída lista-os.

> [!Note]
> Uma coluna de índice é uma coluna que aparece nos filtros ou se junta condições. Uma coluna incluída é uma coluna que aparece no seu select/project.

Por exemplo, na seguinte consulta:

```sql
SELECT X
FROM T
WHERE Y = 2
```

Y pode ser uma coluna de índice, e X pode ser uma coluna incluída.

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

Resultados em:

```console
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
Agora, cria três índices usando as configurações do índice. Para isso, você invoca o comando "createIndex" no nosso caso Hyperspace. Este comando requer uma configuração de índice e o dataFrame que contém linhas para ser indexado. Executar a seguinte célula cria três índices.

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

## <a name="list-indexes"></a>Índices de listas

O código que se segue mostra como pode listar todos os índices disponíveis numa instância hyperspace. Utiliza a API de "indexes" que devolve informações sobre os índices existentes como um DataFrame de Faísca para que possa realizar operações adicionais. 

Por exemplo, pode invocar operações válidas neste DataFrame para verificar o seu conteúdo ou analisá-lo mais aprofundadamente (por exemplo, filtrar índices específicos ou agrupar-se de acordo com alguma propriedade desejada).

A célula que se segue utiliza a ação de 'show' do DataFrame para imprimir completamente as linhas e mostrar detalhes dos nossos índices de forma tabular. Para cada índice, pode ver todas as informações que o Hyperspace armazenou sobre o mesmo nos metadados. Notará imediatamente o seguinte:

* config.indexName, config.indexedColumns, config.includeColumns, e status.status são os campos a que um utilizador normalmente se refere.
* dfSignature é gerado automaticamente pelo Hyperspace e é único para cada índice. O hiperespaço utiliza esta assinatura internamente para manter o índice e explorá-lo no tempo de consulta.


Na seguinte produção, os três índices devem ter "ATIVE" como estado e o seu nome, colunas indexadas e colunas incluídas devem corresponder ao que definimos nas configurações de índice acima.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

Resultados em:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>Eliminar índices

Pode deixar cair um índice existente utilizando a API "deleteIndex" e fornecendo o nome do índice. A eliminação do índice faz uma eliminação suave: Atualiza principalmente o estado do índice nos metadados do Hiperespaço de "ATIVE" para "DELETED". Isto excluirá o índice de queda de qualquer otimização de consultas futuras e o Hyperspace já não escolhe esse índice para qualquer consulta. 

No entanto, os ficheiros de índice para um índice eliminado continuam disponíveis (uma vez que é um soft-delete), de modo que o índice pode ser restaurado se o utilizador pedir.

A célula seguinte elimina o índice com o nome "deptIndex2" e lista os metadados do Hiperespacia depois disso. A saída deve ser semelhante à célula acima para "List Indexes", com exceção do "deptIndex2", que agora deve ter o seu estatuto alterado para "DELETED".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

Resultados em:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>Restaurar índices

Pode utilizar a API "restoreIndex" para restaurar um índice eliminado. Isto trará de volta a versão mais recente do índice para o status ATIVE e torna-o utilizável novamente para consultas. A célula que se segue mostra um exemplo de utilização "restaurar o Index". Elimina "deptIndex1" e restaura-o. A saída mostra que "deptIndex1" entrou pela primeira vez no estado "DELETED" depois de invocar o comando "deleteIndex" e voltou ao estado "ATIVE" depois de chamar "restoreIndex".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

Resultados em:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>Índices de vácuo

Pode executar uma eliminação dura, isto é, remover completamente os ficheiros e a entrada de metadados para um índice eliminado utilizando o comando **vacuumIndex.** Esta ação é irreversível. Elimina fisicamente todos os ficheiros de índice, e é por isso que é difícil apagar.

A célula que se segue aspira o índice "deptIndex2" e mostra metadados hiperespaciais após a aspiração. Deverá ver entradas de metadados para dois índices "deptIndex1" e "empIndex" ambos com estatuto "ATIVE" e sem entrada para "deptIndex2".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

Resultados em:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>Ativar ou desativar o Hiperespaço

O hiperespaço fornece APIs para ativar ou desativar o uso do índice com o Spark.

* Ao utilizar o comando **enableHyperspace,** as regras de otimização do Hiperespaço tornam-se visíveis para o otimizador de Faíscas e exploram os índices de Hiperespaço existentes para otimizar as consultas dos utilizadores.
* Ao utilizar o comando **Desativação Do Espaço,** as regras do Hiperespaço já não se aplicam durante a otimização de consultas. O hiperespaço incapacitante não tem impacto nos índices criados porque permanecem intactos.

A célula que se segue mostra como pode utilizar estes comandos para ativar ou desativar o Hiperespaço. A saída mostra uma referência à sessão spark existente cuja configuração é atualizada.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

Resultados em:

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>Utilização de índices

Para fazer com que o Spark utilize índices de Hiperespaço durante o processamento de consultas, tem de se certificar de que o Hiperspace está ativado.

A célula que se segue ativa o Hiperespaço e cria dois DataFrames contendo os registos de dados da amostra, que utiliza para executar consultas de exemplo. Para cada DataFrame, são impressas algumas linhas de amostra.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

Resultados em:

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;Isto só mostra as 5 melhores linhas &nbsp;&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>Tipos de índice

Atualmente, o Hyperspace tem regras para explorar índices para dois grupos de consultas:

* Consultas de seleção com pré-predicados de filtragem de seleção de procura ou de alcance.
* Junte as consultas com uma igualdade junte-se predicado (isto é, equijoins).

## <a name="indexes-for-accelerating-filters"></a>Índices para acelerar filtros

O primeiro exemplo de consulta faz uma procura nos registos do departamento, como mostrado na célula seguinte. Em SQL, esta consulta parece o seguinte exemplo:

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

A saída de executar as seguintes células mostra:

* Resultado da consulta, que é um nome de departamento único.
* Plano de consulta que Spark usou para executar a consulta.

No plano de consulta, o operador **FileScan** na parte inferior do plano mostra a fonte de dados de onde os registos foram lidos. A localização deste ficheiro indica o caminho para a versão mais recente do índice "deptIndex1". Esta informação mostra que de acordo com a consulta e usando as regras de otimização do Hiperespaço, Spark decidiu explorar o índice adequado em tempo de execução.

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

Resultados em:

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

O segundo exemplo é uma consulta de seleção de gama nos registos do departamento. Em SQL, esta consulta parece o seguinte exemplo:

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

Semelhante ao primeiro exemplo, a saída da célula seguinte mostra os resultados da consulta (nomes de dois departamentos) e o plano de consulta. A localização do ficheiro de dados no operador **fileScan** mostra que "deptIndex1" foi usado para executar a consulta.

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

Resultados em:

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
O terceiro exemplo é uma consulta que junta o departamento e os registos dos funcionários na identificação do departamento. A declaração sql equivalente é mostrada da seguinte forma:

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
A saída de executar a seguinte célula mostra os resultados da consulta, que são os nomes de 14 funcionários e o nome do departamento em que cada funcionário trabalha. O plano de consulta também está incluído na saída. Note como as localizações dos ficheiros para dois operadores **de FileScan** mostram que a Spark usou índices "empIndex" e "deptIndex1" para executar a consulta.

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

Resultados em:

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>Apoio à semântica SQL

A utilização do índice é transparente para saber se utiliza o DataFrame API ou o Spark SQL. O exemplo a seguir mostra o mesmo exemplo de junção como antes, na forma SQL, mostrando a utilização de índices se aplicável.

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

Resultados em:

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>Explique a API

Os índices são ótimos, mas como sabe se estão a ser usados? O hiperespaço permite que os utilizadores comparem o seu plano original com o plano dependente de índices atualizado antes de executarem a sua consulta. Tem a opção de escolher entre HTML, texto simples ou modo consola para exibir a saída do comando.

A célula que se segue mostra um exemplo com HTML. A secção realçada representa a diferença entre os planos originais e atualizados, juntamente com os índices utilizados.

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin)(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

Resultados em:

### <a name="plan-with-indexes"></a>Plano com índices

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>Plano sem índices

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>Índices utilizados

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>Atualizar índices

Se os dados originais sobre os quais um índice foi criado alterarem, o índice deixará de capturar o estado mais recente dos dados. Pode refrescar um índice velho utilizando o comando **RefreshIndex.** Este comando faz com que o índice seja totalmente reconstruído e atualiza-o de acordo com os registos de dados mais recentes. Vamos mostrar-lhe como refrescar gradualmente o seu índice em outros cadernos.

As duas células que se seguem mostram um exemplo para este cenário:

* A primeira célula adiciona mais dois departamentos aos dados originais dos departamentos. Lê e imprime uma lista de departamentos para verificar se novos departamentos são adicionados corretamente. A produção mostra seis departamentos no total: quatro antigos e dois novos. Invocando **atualizações Index** "deptIndex1" para que o índice capture novos departamentos.
* A segunda célula executa o nosso exemplo de consulta de seleção de alcance. Os resultados devem agora conter quatro departamentos: dois são os vistos antes quando fizemos a consulta anterior, e dois são os novos departamentos que adicionámos.

### <a name="specific-index-refresh"></a>Atualização específica do índice

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

Resultados em:

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>Seleção de gama

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

Resultados em:

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="hybrid-scan-for-mutable-datasets"></a>Varredura híbrida para conjuntos de dados mutáveis

Muitas vezes, se os seus dados de origem subjacentes tivessem alguns novos ficheiros anexados ou ficheiros existentes eliminados, o seu índice ficará velho e o Hyperspace decidirá não usá-lo. No entanto, há momentos em que só se quer usar o índice sem ter de o refrescar sempre. Pode haver várias razões para fazê-lo:

- Não quer atualizar continuamente o seu índice, mas em vez disso quer fazê-lo periodicamente porque entende melhor as suas cargas de trabalho.
- Adicionou/removeu apenas alguns ficheiros e não quer esperar que mais um trabalho de atualização termine.

Para permitir que ainda utilize um índice antigo, o Hyperspace introduz uma nova técnica que permite aos utilizadores utilizar índices desatualizados ou velhos (por exemplo, os dados de origem subjacentes tinham alguns novos ficheiros anexados ou ficheiros existentes eliminados) sem índices refrescantes.

Para tal, quando define a configuração adequada para permitir a digitalização híbrida, o Hyperspace modifica o plano de consulta para alavancar as alterações como seguintes:
* Os ficheiros anexados podem ser fundidos para indexar dados utilizando a União ou o BucketUnion (para aderir). Os dados anexados também podem ser aplicados antes da fusão, se necessário.
* Os ficheiros eliminados podem ser tratados injetando a condição filter-NOT-IN na coluna de linhagem dos dados do índice, para que as linhas indexadas dos ficheiros eliminados possam ser excluídas na hora da consulta.

Pode verificar a transformação do plano de consulta em exemplos seguidos.

> [!NOTE]
> Atualmente, a varredura híbrida é suportada apenas para dados não divididos.

### <a name="hybrid-scan-for-appended-files---non-partitioned-data"></a>Varredura híbrida para ficheiros anexados - dados não divididos

Os dados não divididos são utilizados no exemplo seguinte. Neste exemplo, esperamos que o índice Deder pode ser usado para a consulta e o BucketUnion é introduzido para ficheiros anexados.

:::zone pivot = "programming-language-scala"

```scala
val testData = Seq(
    ("orange", 3, "2020-10-01"),
    ("banana", 1, "2020-10-01"),
    ("carrot", 5, "2020-10-02"),
    ("beetroot", 12, "2020-10-02"),
    ("orange", 2, "2020-10-03"),
    ("banana", 11, "2020-10-03"),
    ("carrot", 3, "2020-10-03"),
    ("beetroot", 2, "2020-10-04"),
    ("cucumber", 7, "2020-10-05"),
    ("pepper", 20, "2020-10-06")
    ).toDF("name", "qty", "date")

val testDataLocation = s"$dataPath/productTable"

testData.write.mode("overwrite").parquet(testDataLocation)
val testDF = spark.read.parquet(testDataLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
testdata = [
    ("orange", 3, "2020-10-01"),
    ("banana", 1, "2020-10-01"),
    ("carrot", 5, "2020-10-02"),
    ("beetroot", 12, "2020-10-02"),
    ("orange", 2, "2020-10-03"),
    ("banana", 11, "2020-10-03"),
    ("carrot", 3, "2020-10-03"),
    ("beetroot", 2, "2020-10-04"),
    ("cucumber", 7, "2020-10-05"),
    ("pepper", 20, "2020-10-06")
]

testdata_location = data_path + "/productTable"
from pyspark.sql.types import StructField, StructType, StringType, IntegerType
testdata_schema = StructType([
    StructField('name', StringType(), True),
    StructField('qty', IntegerType(), True),
    StructField('date', StringType(), True)])

test_df = spark.createDataFrame(testdata, testdata_schema)
test_df.write.mode("overwrite").parquet(testdata_location)
test_df = spark.read.parquet(testdata_location)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Sql.Types;

var products = new List<GenericRow>() {
    new GenericRow(new object[] {"orange", 3, "2020-10-01"}),
    new GenericRow(new object[] {"banana", 1, "2020-10-01"}),
    new GenericRow(new object[] {"carrot", 5, "2020-10-02"}),
    new GenericRow(new object[] {"beetroot", 12, "2020-10-02"}),
    new GenericRow(new object[] {"orange", 2, "2020-10-03"}),
    new GenericRow(new object[] {"banana", 11, "2020-10-03"}),
    new GenericRow(new object[] {"carrot", 3, "2020-10-03"}),
    new GenericRow(new object[] {"beetroot", 2, "2020-10-04"}),
    new GenericRow(new object[] {"cucumber", 7, "2020-10-05"}),
    new GenericRow(new object[] {"pepper", 20, "2020-10-06"})
};
var productsSchema = new StructType(new List<StructField>()
{
    new StructField("name", new StringType()),
    new StructField("qty", new IntegerType()),
    new StructField("date", new StringType())
});

DataFrame testData = spark.CreateDataFrame(products, productsSchema); 
string testDataLocation = $"{dataPath}/productTable";
testData.Write().Mode("overwrite").Parquet(testDataLocation);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
// CREATE INDEX
hyperspace.createIndex(testDF, IndexConfig("productIndex2", Seq("name"), Seq("date", "qty")))

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
val filter1 = testDF.filter("name = 'banana'")
val filter2 = testDF.filter("qty > 10")
val query = filter1.join(filter2, "name")

// Check Join index rule is applied properly.
hyperspace.explain(query)(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# CREATE INDEX
hyperspace.createIndex(test_df, IndexConfig("productIndex2", ["name"], ["date", "qty"]))

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
filter1 = test_df.filter("name = 'banana'")
filter2 = test_df.filter("qty > 10")
query = filter1.join(filter2, "name")

# Check Join index rule is applied properly.
hyperspace.explain(query, True, displayHTML)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// CREATE INDEX
DataFrame testDF = spark.Read().Parquet(testDataLocation);
var productIndex2Config = new IndexConfig("productIndex", new string[] {"name"}, new string[] {"date", "qty"});
hyperspace.CreateIndex(testDF, productIndex2Config);

// Check Join index rule is applied properly.
DataFrame filter1 = testDF.Filter("name = 'banana'");
DataFrame filter2 = testDF.Filter("qty > 10");
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

Resultado em:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#607, qty#608, date#609, qty#632, date#633]
+- SortMergeJoin [name#607], [name#631], Inner
   :- *(1) Project [name#607, qty#608, date#609]
   :  +- *(1) Filter (isnotnull(name#607) && (name#607 = banana))
   :     +- *(1) FileScan parquet [name#607,date#609,qty#608] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#631, qty#632, date#633]
      +- *(2) Filter (((isnotnull(qty#632) && (qty#632 > 10)) && isnotnull(name#631)) && (name#631 = banana))
         +- *(2) FileScan parquet [name#631,date#633,qty#632] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#607, qty#608, date#609, qty#632, date#633]
+- SortMergeJoin [name#607], [name#631], Inner
   :- *(2) Sort [name#607 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#607, 200), [id=#453]
   :     +- *(1) Project [name#607, qty#608, date#609]
   :        +- *(1) Filter (isnotnull(name#607) && (name#607 = banana))
   :           +- *(1) FileScan parquet [name#607,qty#608,date#609] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#631 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#631, 200), [id=#459]
         +- *(3) Project [name#631, qty#632, date#633]
            +- *(3) Filter (((isnotnull(qty#632) && (qty#632 > 10)) && isnotnull(name#631)) && (name#631 = banana))
               +- *(3) FileScan parquet [name#631,qty#632,date#633] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=0

```

:::zone pivot = "programming-language-scala"

```scala
// Append new files.
val appendData = Seq(
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")).toDF("name", "qty", "date")
appendData.write.mode("append").parquet(testDataLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Append new files.
append_data = [
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")
]
append_df = spark.createDataFrame(append_data, testdata_schema)
append_df.write.mode("append").parquet(testdata_location)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Append new files.
var appendProducts = new List<GenericRow>()
{
    new GenericRow(new object[] {"orange", 13, "2020-11-01"}),
    new GenericRow(new object[] {"banana", 5, "2020-11-01"})
};
    
DataFrame appendData = spark.CreateDataFrame(appendProducts, productsSchema);
appendData.Write().Mode("Append").Parquet(testDataLocation);

```

::: zone-end

A varredura híbrida é desativada por defeito. Portanto, você verá que, como anexamos novos dados, o Hyperspace decidirá *não* usar o índice.

Na saída, não verá diferenças de plano (portanto, sem destaque).

:::zone pivot = "programming-language-scala"

```scala
// Hybrid Scan configs are false by default.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "false")
spark.conf.set("spark.hyperspace.index.hybridscan.delete.enabled", "false")

val testDFWithAppend = spark.read.parquet(testDataLocation)
val filter1 = testDFWithAppend.filter("name = 'banana'")
val filter2 = testDFWithAppend.filter("qty > 10")
val query = filter1.join(filter2, "name")
hyperspace.explain(query)(displayHTML(_))
query.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Hybrid Scan configs are false by default.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "false")
spark.conf.set("spark.hyperspace.index.hybridscan.delete.enabled", "false")

test_df_with_append = spark.read.parquet(testdata_location)
filter1 = test_df_with_append.filter("name = 'banana'")
filter2 = test_df_with_append.filter("qty > 10")
query = filter1.join(filter2, "name")
hyperspace.explain(query, True, displayHTML)
query.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Hybrid Scan configs are false by default.
spark.Conf().Set("spark.hyperspace.index.hybridscan.enabled", "false");
spark.Conf().Set("spark.hyperspace.index.hybridscan.delete.enabled", "false");

DataFrame testDFWithAppend = spark.Read().Parquet(testDataLocation);
DataFrame filter1 = testDFWithAppend.Filter("name = 'banana'");
DataFrame filter2 = testDFWithAppend.Filter("qty > 10");
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

Resultado em:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#685, date#686]
+- SortMergeJoin [name#678], [name#684], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#589]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#684 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#684, 200), [id=#595]
         +- *(3) Project [name#684, qty#685, date#686]
            +- *(3) Filter (((isnotnull(qty#685) && (qty#685 > 10)) && (name#684 = banana)) && isnotnull(name#684))
               +- *(3) FileScan parquet [name#684,qty#685,date#686] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

=============================================================
Plan without indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#685, date#686]
+- SortMergeJoin [name#678], [name#684], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#536]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#684 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#684, 200), [id=#542]
         +- *(3) Project [name#684, qty#685, date#686]
            +- *(3) Filter (((isnotnull(qty#685) && (qty#685 > 10)) && (name#684 = banana)) && isnotnull(name#684))
               +- *(3) FileScan parquet [name#684,qty#685,date#686] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
|banana|  1|2020-10-01| 11|2020-10-03|
+------+---+----------+---+----------
```

### <a name="enable-hybrid-scan"></a>Ativar a varredura híbrida

No plano com índices, pode ver a partição de haxixe exchange necessária apenas para ficheiros anexados para que possamos ainda utilizar os dados do índice "baralhados" com ficheiros anexados. BucketUnion é usado para fundir ficheiros "baralhados" anexados com os dados do índice.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hybrid Scan config. "delete" config is not necessary since we only appended data.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "true")
spark.enableHyperspace
// Need to redefine query to recalculate the query plan.
val query = filter1.join(filter2, "name")
hyperspace.explain(query)(displayHTML(_))
query.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Enable Hybrid Scan config. "delete" config is not necessary.
spark.conf.set("spark.hyperspace.index.hybridscan.enabled", "true")

# Need to redefine query to recalculate the query plan.
query = filter1.join(filter2, "name")
hyperspace.explain(query, True, displayHTML)
query.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Enable Hybrid Scan config. "delete" config is not necessary.
spark.Conf().Set("spark.hyperspace.index.hybridscan.enabled", "true");
spark.EnableHyperspace();
// Need to redefine query to recalculate the query plan.
DataFrame query = filter1.Join(filter2, filter1.Col("name") == filter2.Col("name"));

query.Show();

hyperspace.Explain(query, true, input => DisplayHTML(input));
```

::: zone-end

Resultado em:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#732, date#733]
+- SortMergeJoin [name#678], [name#731], Inner
   :- *(3) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- BucketUnion 200 buckets, bucket columns: [name]
   :     :- *(1) Project [name#678, qty#679, date#680]
   :     :  +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :     :     +- *(1) FileScan parquet [name#678,date#680,qty#679] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   :     +- Exchange hashpartitioning(name#678, 200), [id=#775]
   :        +- *(2) Project [name#678, qty#679, date#680]
   :           +- *(2) Filter (isnotnull(name#678) && (name#678 = banana))
   :              +- *(2) FileScan parquet [name#678,date#680,qty#679] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(6) Sort [name#731 ASC NULLS FIRST], false, 0
      +- BucketUnion 200 buckets, bucket columns: [name]
         :- *(4) Project [name#731, qty#732, date#733]
         :  +- *(4) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
         :     +- *(4) FileScan parquet [name#731,date#733,qty#732] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
         +- Exchange hashpartitioning(name#731, 200), [id=#783]
            +- *(5) Project [name#731, qty#732, date#733]
               +- *(5) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
                  +- *(5) FileScan parquet [name#731,date#733,qty#732] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Plan without indexes:
=============================================================
Project [name#678, qty#679, date#680, qty#732, date#733]
+- SortMergeJoin [name#678], [name#731], Inner
   :- *(2) Sort [name#678 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#678, 200), [id=#701]
   :     +- *(1) Project [name#678, qty#679, date#680]
   :        +- *(1) Filter (isnotnull(name#678) && (name#678 = banana))
   :           +- *(1) FileScan parquet [name#678,qty#679,date#680] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#731 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#731, 200), [id=#707]
         +- *(3) Project [name#731, qty#732, date#733]
            +- *(3) Filter (((isnotnull(qty#732) && (qty#732 > 10)) && isnotnull(name#731)) && (name#731 = banana))
               +- *(3) FileScan parquet [name#731,qty#732,date#733] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=0


+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana|  1|2020-10-01| 11|2020-10-03|
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
+------+---+----------+---+----------+
```

## <a name="incremental-index-refresh"></a>Atualização incremental do índice

Quando está pronto para atualizar os seus índices mas não quer reconstruir todo o seu índice, o Hyperspace suporta a atualização de índices de forma incremental utilizando a `hs.refreshIndex("name", "incremental")` API. Isto eliminará a necessidade de uma reconstrução completa do índice de raiz, utilizando ficheiros de índice previamente criados, bem como atualizando índices apenas nos dados recém-adicionados.

É claro que use periodicamente a API complementar `optimizeIndex` (mostrada abaixo) para se certificar de que não vê regressões de desempenho. Recomendamos que o chamado otimize pelo menos uma vez por cada 10 vezes que `refreshIndex(..., "incremental")` ligar, assumindo que os dados adicionados/removidos são < 10% do conjunto de dados original. Por exemplo, se o seu conjunto de dados original for de 100 GB, e tiver adicionado/removido dados em incrementos/decrementos de 1 GB, pode ligar `refreshIndex` 10 vezes antes de ligar `optimizeIndex` . Por favor, note que este exemplo é simplesmente usado para ilustração e você tem que adaptá-lo para suas cargas de trabalho.

No exemplo abaixo, note a adição de um nó Sort no plano de consulta quando os índices são usados. Isto porque os índices parciais são criados nos ficheiros de dados anexados, fazendo com que a Spark introduza um `Sort` . Por favor, note também que `Shuffle` a troca ainda está eliminada do plano, dando-lhe a aceleração adequada.

:::zone pivot = "programming-language-scala"

```scala
def query(): DataFrame = {
    val testDFWithAppend = spark.read.parquet(testDataLocation)
    val filter1 = testDFWithAppend.filter("name = 'banana'")
    val filter2 = testDFWithAppend.filter("qty > 10")
    filter1.join(filter2, "name")
}

hyperspace.refreshIndex("productIndex2", "incremental")

hyperspace.explain(query())(displayHTML(_))
query().show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
def query():
    test_df_with_append = spark.read.parquet(testdata_location)
    filter1 = test_df_with_append.filter("name = 'banana'")
    filter2 = test_df_with_append.filter("qty > 10")
    return filter1.join(filter2, "name")

hyperspace.refreshIndex("productIndex2", "incremental")

hyperspace.explain(query(), True, displayHTML)
query().show()
```

::: zone-end

Resultado em:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#820, qty#821, date#822, qty#827, date#828]
+- SortMergeJoin [name#820], [name#826], Inner
   :- *(1) Sort [name#820 ASC NULLS FIRST], false, 0
   :  +- *(1) Project [name#820, qty#821, date#822]
   :     +- *(1) Filter (isnotnull(name#820) && (name#820 = banana))
   :        +- *(1) FileScan parquet [name#820,date#822,qty#821] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Sort [name#826 ASC NULLS FIRST], false, 0
      +- *(2) Project [name#826, qty#827, date#828]
         +- *(2) Filter (((isnotnull(qty#827) && (qty#827 > 10)) && (name#826 = banana)) && isnotnull(name#826))
            +- *(2) FileScan parquet [name#826,date#828,qty#827] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#820, qty#821, date#822, qty#827, date#828]
+- SortMergeJoin [name#820], [name#826], Inner
   :- *(2) Sort [name#820 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#820, 200), [id=#927]
   :     +- *(1) Project [name#820, qty#821, date#822]
   :        +- *(1) Filter (isnotnull(name#820) && (name#820 = banana))
   :           +- *(1) FileScan parquet [name#820,qty#821,date#822] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#826 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#826, 200), [id=#933]
         +- *(3) Project [name#826, qty#827, date#828]
            +- *(3) Filter (((isnotnull(qty#827) && (qty#827 > 10)) && (name#826 = banana)) && isnotnull(name#826))
               +- *(3) FileScan parquet [name#826,qty#827,date#828] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), EqualTo(name,banana), IsNotNull(name)], ReadSchema: struct

+------+---+----------+---+----------+
|  name|qty|      date|qty|      date|
+------+---+----------+---+----------+
|banana|  1|2020-10-01| 11|2020-10-03|
|banana| 11|2020-10-03| 11|2020-10-03|
|banana|  5|2020-11-01| 11|2020-10-03|
+------+---+----------+---+----------+
```

## <a name="optimize-index-layout"></a>Otimizar o layout do índice

Depois de chamar atualizações incrementais várias vezes em dados recentemente anexados (por exemplo, se o utilizador escrever para dados em pequenos lotes ou em caso de cenários de streaming), o número de ficheiros de índice tendem a tornar-se grandes afetando o desempenho do índice (grande número de pequenos ficheiros problemáticos). O Hiperspace fornece `hyperspace.optimizeIndex("indexName")` API para otimizar o layout do índice e reduzir o problema dos grandes ficheiros.

No plano abaixo, note que o Hyperspace removeu o nó de Sort adicional no plano de consulta. Otimizar pode ajudar a evitar a triagem de qualquer balde de índice que contenha apenas um ficheiro. No entanto, isso só será verdade se todos os baldes de índice tiverem no máximo 1 ficheiro por balde, depois `optimizeIndex` .

:::zone pivot = "programming-language-scala"

```scala
// Append some more data and call refresh again.
val appendData = Seq(
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")).toDF("name", "qty", "date")
appendData.write.mode("append").parquet(testDataLocation)

hyperspace.refreshIndex("productIndex2", "incremental")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Append some more data and call refresh again.
append_data = [
    ("orange", 13, "2020-11-01"),
    ("banana", 5, "2020-11-01")
]
append_df = spark.createDataFrame(append_data, testdata_schema)
append_df.write.mode("append").parquet(testdata_location)

hyperspace.refreshIndex("productIndex2", "incremental"
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
// Call optimize. Ensure that Sort is removed after optimization (This is possible here because after optimize, in this case, every bucket contains only 1 file.).
hyperspace.optimizeIndex("productIndex2")

hyperspace.explain(query())(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Call optimize. Ensure that Sort is removed after optimization (This is possible here because after optimize, in this case, every bucket contains only 1 file.).
hyperspace.optimizeIndex("productIndex2")

hyperspace.explain(query(), True, displayHTML)
```

::: zone-end

Resultado em:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#954, qty#955, date#956, qty#961, date#962]
+- SortMergeJoin [name#954], [name#960], Inner
   :- *(1) Project [name#954, qty#955, date#956]
   :  +- *(1) Filter (isnotnull(name#954) && (name#954 = banana))
   :     +- *(1) FileScan parquet [name#954,date#956,qty#955] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#960, qty#961, date#962]
      +- *(2) Filter (((isnotnull(qty#961) && (qty#961 > 10)) && isnotnull(name#960)) && (name#960 = banana))
         +- *(2) FileScan parquet [name#960,date#962,qty#961] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#954, qty#955, date#956, qty#961, date#962]
+- SortMergeJoin [name#954], [name#960], Inner
   :- *(2) Sort [name#954 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#954, 200), [id=#1070]
   :     +- *(1) Project [name#954, qty#955, date#956]
   :        +- *(1) Filter (isnotnull(name#954) && (name#954 = banana))
   :           +- *(1) FileScan parquet [name#954,qty#955,date#956] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#960 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#960, 200), [id=#1076]
         +- *(3) Project [name#960, qty#961, date#962]
            +- *(3) Filter (((isnotnull(qty#961) && (qty#961 > 10)) && isnotnull(name#960)) && (name#960 = banana))
               +- *(3) FileScan parquet [name#960,qty#961,date#962] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=3
```

### <a name="optimize-modes"></a>Otimizar modos

O modo predefinido para otimização é o modo "rápido" onde os ficheiros menores do que um limiar predefinido são escolhidos para otimização. Para maximizar o efeito da otimização, o Hyperspace permite outro modo de otimização "completo" como mostrado abaixo. Este modo escolhe todos os ficheiros de índice para otimização independentemente do seu tamanho de ficheiro e cria o melhor layout possível do índice. Isto também é mais lento do que o modo de otimização padrão porque mais dados estão sendo processados aqui.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.optimizeIndex("productIndex2", "full")

hyperspace.explain(query())(displayHTML(_))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python
hyperspace.optimizeIndex("productIndex2", "full")

hyperspace.explain(query(), True, displayHTML)
```

::: zone-end

Resultado em:

```console
=============================================================
Plan with indexes:
=============================================================
Project [name#1000, qty#1001, date#1002, qty#1007, date#1008]
+- SortMergeJoin [name#1000], [name#1006], Inner
   :- *(1) Project [name#1000, qty#1001, date#1002]
   :  +- *(1) Filter (isnotnull(name#1000) && (name#1000 = banana))
   :     +- *(1) FileScan parquet [name#1000,date#1002,qty#1001] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200
   +- *(2) Project [name#1006, qty#1007, date#1008]
      +- *(2) Filter (((isnotnull(qty#1007) && (qty#1007 > 10)) && isnotnull(name#1006)) && (name#1006 = banana))
         +- *(2) FileScan parquet [name#1006,date#1008,qty#1007] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/p..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct, SelectedBucketsCount: 1 out of 200

=============================================================
Plan without indexes:
=============================================================
Project [name#1000, qty#1001, date#1002, qty#1007, date#1008]
+- SortMergeJoin [name#1000], [name#1006], Inner
   :- *(2) Sort [name#1000 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(name#1000, 200), [id=#1160]
   :     +- *(1) Project [name#1000, qty#1001, date#1002]
   :        +- *(1) Filter (isnotnull(name#1000) && (name#1000 = banana))
   :           +- *(1) FileScan parquet [name#1000,qty#1001,date#1002] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct
   +- *(4) Sort [name#1006 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(name#1006, 200), [id=#1166]
         +- *(3) Project [name#1006, qty#1007, date#1008]
            +- *(3) Filter (((isnotnull(qty#1007) && (qty#1007 > 10)) && isnotnull(name#1006)) && (name#1006 = banana))
               +- *(3) FileScan parquet [name#1006,qty#1007,date#1008] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/data-777519/prod..., PartitionFilters: [], PushedFilters: [IsNotNull(qty), GreaterThan(qty,10), IsNotNull(name), EqualTo(name,banana)], ReadSchema: struct

=============================================================
Indexes used:
=============================================================
productIndex2:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspace/indexes-777519/productIndex2/v__=4
```

## <a name="next-steps"></a>Passos seguintes

* [Projeto Hyperspace](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](../index.yml)