---
title: Garantir credenciais de acesso com serviços ligados em Apache Spark para Azure Synapse Analytics
description: Este artigo fornece conceitos sobre como integrar seguramente o Apache Spark para a Azure Synapse Analytics com outros serviços utilizando serviços ligados e biblioteca simbólica
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: d542e6ef784d763e406aad28231431cbc382fbfd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450907"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Garantir as suas credenciais através de serviços ligados com a TokenLibrary
O acesso a dados de fontes externas é um padrão comum. A menos que a fonte de dados externa permita o acesso anónimo, é provável que tenha de garantir a sua ligação com uma cadeia de credenciais, secretas ou de ligação.  

A Azure Synapse Analytics fornece serviços ligados para simplificar o processo de integração armazenando os detalhes da conexão num serviço ligado ou cofre de chaves Azure. Uma vez criado um serviço ligado, a faísca Apache pode fazer referência ao serviço ligado para aplicar as informações de ligação no seu código. 

Para mais informações, consulte [os serviços ligados.](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> [!NOTE]
> Aceder a ficheiros a partir do Azure Data Lake Storage dentro do seu espaço de trabalho utiliza a passagem AAD para autenticação, portanto, não precisará de utilizar o TokenLibrary. 


## <a name="prerequisite"></a>Pré-requisito
* Serviço ligado - Deve criar um serviço ligado à fonte de dados externo e fazer referência ao serviço ligado a partir da Biblioteca Token. Saiba mais sobre [serviços ligados.](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Ligue-se à ADLS Gen2 fora do espaço de trabalho de Synapse

A Synapse proporciona uma experiência integrada de serviços ligados para a Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Use a Biblioteca Token

Para se conectar a outros serviços ligados, pode fazer uma chamada direta para o TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Para recuperar a cadeia de ligação, utilize a função <b>getConnectionString</b> e passe no <b>nome de serviço ligado</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Para analisar valores específicos de um par <i>chave=valor</i> na cadeia de ligação, tais como 

<i>PredefiniçãoendpointsProtocol=https; Nome de \<AccountName> conta= ContaKey=\<AccountKey></i>

utilize a <b>função getConnectionStringAsMap</b> e passe a chave para devolver o valor.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Passos seguintes

- [Escreva para piscina SQL dedicada](./synapse-spark-sql-pool-import-export.md)

