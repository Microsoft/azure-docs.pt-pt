---
title: Credenciais de acesso seguro com serviços ligados em Apache Spark para Azure Synapse Analytics
description: Este artigo fornece conceitos sobre como integrar seguramente o Apache Spark para a Azure Synapse Analytics com outros serviços utilizando serviços ligados e biblioteca simbólica
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693582"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Credenciais seguras com serviços ligados usando a TokenLibrary

O acesso a dados de fontes externas é um padrão comum. A menos que a fonte de dados externa permita o acesso anónimo, é provável que tenha de garantir a sua ligação com uma cadeia de credenciais, secretas ou de ligação.  

A Synapse utiliza o Azure Ative Directory (AAD) por padrão para autenticação entre recursos.  Se precisar de se ligar a um recurso utilizando outras credenciais, utilize o TokenLibrary diretamente.  O TokenLibrary simplifica o processo de recuperação de fichas SAS, fichas AAD, cordas de conexão e segredos armazenados num serviço ligado ou a partir de um Cofre de Chaves Azure.

Ao recuperar segredos do Azure Key Vault, recomendamos a criação de um serviço ligado ao seu Cofre de Chaves Azure.  Certifique-se de que o espaço de trabalho Synapse gerido identidade de serviço gerido (MSI) tem privilégios Secret Get no seu Cofre chave Azure.  A Sinapse autenticará o Cofre da Chave Azure utilizando a identidade de serviço gerida pelo espaço de trabalho synapse. Se ligar diretamente ao Cofre de Chaves Azure sem um serviço ligado, autenticará utilizando a credencial do seu utilizador Azure Ative Directory.

Para mais informações, consulte [os serviços ligados.](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="usage"></a>Utilização

### <a name="tokenlibraryhelp"></a>TokenLibrary.help()
Esta função exibe a documentação de ajuda para o TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>TokenLibrary para Azure Data Lake Storage Gen2

#### <a name="adls-gen2-primary-storage"></a>Armazenamento Primário ADLS Gen2

Aceder a ficheiros do Azure Data Lake Storage utiliza o Azure Ative Directory passthrough para autenticação por padrão e não requer a utilização explícita do TokenLibrary.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>Armazenamento da ADLS Gen2 com serviços ligados

A Synapse proporciona uma experiência integrada de serviços ligados ao ligar-se ao Azure Data Lake Storage Gen2.  Os Serviços Ligados podem ser configurados para autenticar através de uma **Chave de Conta,** **Principal de Serviço,** **Identidade Gerida,** Ou **Credencial.**

Quando o método de autenticação do serviço ligado for definido como **Chave conta,** o serviço ligado autenticará utilizando a chave de conta de armazenamento fornecida, solicitará uma chave SAS e aplica-a automaticamente ao pedido de armazenamento utilizando o **LinkedServiceBasedSASProvider**.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Quando o método de autenticação do serviço ligado for definido para **Managed Identity** ou **Service Principal,** o serviço ligado utilizará o token principal de identidade ou serviço gerido com o fornecedor **LinkedServiceBasedTokenProvider.**  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>Armazenamento da ADLS Gen2 (sem serviços ligados)

Ligue-se ao armazenamento da ADLS Gen2 diretamente utilizando uma chave SAS utilize o **ConfBasedSASProvider** e forneça a chave SAS para a definição de configuração **spark.storage.synapse.sas.**

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>Armazenamento ADLS Gen2 com cofre de chaves Azure

Ligue-se ao armazenamento da ADLS Gen2 utilizando um token SAS armazenado em segredo do Azure Key Vault.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary para outros serviços ligados

Para se conectar a outros serviços ligados, pode fazer uma chamada direta para o TokenLibrary.

#### <a name="getconnectionstring"></a>getConnectionString()

 Para recuperar a cadeia de ligação, utilize a função **getConnectionString** e passe no **nome de serviço ligado**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

O getConnectionStringAsMap é uma função de ajudante disponível em Scala e Python para analisar valores específicos de um par _de valores chave=na_ cadeia de conexão, tais como

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

utilize a **função getConnectionStringAsMap** e passe a chave para devolver o valor.  No exemplo de cadeia de ligação acima, 

_**TokenLibrary.getConnectionStringAsMap ("DefaultEndpointsProtocol")**_

voltaria

**_"https"_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Para recuperar um segredo armazenado a partir do Cofre da Chave Azure, recomendamos que crie um serviço ligado ao Cofre da Chave Azure dentro do espaço de trabalho da Synapse. A identidade de serviço gerida pelo espaço de trabalho da Synapse terá de ser concedida permissão **GET** Secrets para o Cofre da Chave Azure.  O serviço ligado usará a identidade de serviço gerida para ligar ao serviço Azure Key Vault para recuperar o segredo.  Caso contrário, a ligação direta ao Azure Key Vault utilizará a credencial Azure Ative Directory (AAD) do utilizador.  Neste caso, o utilizador terá de receber as permissões Get Secret no Cofre da Chave Azure.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Para obter um segredo do Azure Key Vault, utilize a função **TokenLibrary.getSecret().**

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Passos seguintes

- [Escreva para piscina SQL dedicada](./synapse-spark-sql-pool-import-export.md)
